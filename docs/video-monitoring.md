<!--
 * @Author: Null Zhao
 * @Date: 2022-04-07 17:18:19
 * @LastEditors: Null Zhao
 * @LastEditTime: 2022-04-11 16:27:49
 * @FilePath: \my-docs\docs\video-monitoring.md
 * @Description: 
 * ctrl+alt+i ctrl+win+t win+y
 * Copyright (c) 2022 by null, All Rights Reserved. 
-->
# 两种实现方式

## [架构图](./_assets/SFP-video-monitoring-Architecture.pptx"架构图")

?> 两种方法都需要考虑防火墙权限的问题和前端网址(https)与转码后的视频链接(若为http)无法连接的问题

1. 准备事项
  | 设备 | IP |
  | --- | --- |
  | 摄像头 | 192.30.23.11 |
  | 实体机 | 10.41.23.61 |
  | rancher/本机 | 10.47.16.156 |

### 通过实体机/虚拟机转码连接

?> 本方法因需要手动输入指令连接摄像头，因此只适用于连接摄像头数量较少的情况。

![架构图](./_assets/%E5%AE%9E%E4%BD%93%E6%9C%BA%E8%BD%AC%E7%A0%81.png"架构图")

2. 在实体机输入指令

  启动lal转码服务：
  ```
  docker run harbor-k8s.wzs.wistron.com.cn/wzs-ssd/lal:1.0.2
  ```

  将摄像头rtsp转码为rtmp视频流：
  ```
  docker run -d --restart=always --name sfp00001 harbor-k8s.wzs.wistron.com.cn/wzs-ssd/jrottenberg/ffmpeg:1.0.1 -fflags +genpts -use_wallclock_as_timestamps 1 -rtsp_transport tcp -i rtsp://admin:123qweASD@192.30.23.11:554/Streaming/Channels/102 -g 25 -vcodec copy -an -f flv "rtmp://10.47.16.156:1935/live/sfp00001"
  ```
  对上述指令须作3处修改：

    1. 替换name：sfp00001替换为任意自定义视频名称
    2. 替换摄像头ip: 192.30.23.11替换为要连接的摄像头ip
    3. 替换实体机ip: 10.47.16.156替换为进行转码的实体机的ip

### 通过node.js转码连接

![架构图](./_assets/node%E8%BD%AC%E7%A0%81.png)

2. 后端转码
  
  **application.ts添加如下代码**
  ```
  const mediaOption = {
    rtmp: {
        port: 1935,
        chunk_size: 60000,
        gop_cache: true,
        ping: 30,
        ping_timeout: 60,
    },
    http: {
        port: 8000,
        allow_origin: '*',
        mediaroot: './media',
    },
    https: {
        port: 8443,
        key:'./privatekey.pem',
        cert:'./certificate.pem',
    }
    };

    export class ApiApplication extends BootMixin(
    ServiceMixin(RepositoryMixin(RestApplication)),
    ) {

    constructor(options: ApplicationConfig = {}) {
        this.creatMediaServer();
    }

    creatMediaServer() {
        const nodeMediaServer = new NodeMediaServer(mediaOption);
        nodeMediaServer.run();
    }

    }
  ```

  **services/media-sever.service.ts**
  ```
    import {path as ffmpegPath} from '@ffmpeg-installer/ffmpeg';
    import {/* inject, */ BindingScope, injectable} from '@loopback/core';
    import ffmpeg from 'fluent-ffmpeg';
    import NodeMediaServer from 'node-media-server';
    import {ffmpegOptions} from '../config';

    @injectable({scope: BindingScope.TRANSIENT})
    export class MediaSeverService {
    nodeMediaServer: NodeMediaServer;
    constructor(/* Add @inject to inject parameters */) {}

    doRealtimeTranscoding(inputpath: string, room: string) {
        const outputh = ffmpegOptions.outputpath.replace('$room', room);
        // inputpath = 'rtsp://10.41.23.61:8554/dss11';
        const command = ffmpeg(inputpath).setFfmpegPath(ffmpegPath);
        // 转码参数设置
        command
        .addInputOption('-rtsp_transport', 'tcp', '-buffer_size', '102400')
        .outputFormat('flv')
        .videoCodec('copy')
        .noAudio()
        .output(outputh);
        // 状态log
        command
        .on('start', () => console.log(inputpath, 'Stream started.'))
        .on('codecData', () => console.log(inputpath, 'Stream codecData.'))
        .on('error', err => console.log(inputpath, err.message))
        .on('end', () => console.log(inputpath, 'Stream end!'));
        command.run();
        return outputh;
    }
    }
  ```

  **controllers/media-server.controller.ts** 
  ```
    import {service} from '@loopback/core';
    import {get, param, response} from '@loopback/rest';
    import {MediaSeverService} from '../../services';
    export class MediaServerController {
    constructor(@service() private mediaSeverService: MediaSeverService) {}
    @get('/media-server/doRealtimeTranscoding')
    @response(200, {type: 'string', description: '可连接的视频链接'})
    doRealtimeTranscoding(@param.query.string('inputpath') inputpath: string, @param.query.string('room') room: string) {
        return this.mediaSeverService.doRealtimeTranscoding(inputpath, room);
    }
    }
  ```

  **ffmpeg-options.config.json** 
  ```
  {
  "outputpath":"rtmp://localhost/live/$room"
  }
  ```
    
3. 前端连接

  **camera-by-flv.component.html** 
  ```
    <div class="video-box">
        <video controls>
        </video>
    <canvas>
        <p>您的浏览器不支持canvas</p>
    </canvas>
    </div>
  ```
    
  **camera-by-flv.component.ts** 
  ```
    import { Component, ElementRef, Input, OnInit, SimpleChanges } from '@angular/core';
    import Mpegts, * as mpegts from 'mpegts.js';
    import { LittleFuncsService } from 'src/app/shared/service/little-funcs.service';

    @Component({
    selector: 'app-camera-by-flv',
    templateUrl: './camera-by-flv.component.html',
    styleUrls: ['./camera-by-flv.component.less']
    })
    export class CameraByFlvComponent implements OnInit {
    // lal转码,端口为8080,node-media-server转码,端口为8000(http),8443(https)
    @Input() cameraUrl: string='';
    @Input() isLive: boolean = false;
    @Input() cameraLine!: number[][];
    @Input() color: string = 'rgb(255,0,0)';
    @Input() beta: number = 0.75;

    private player!: Mpegts.Player;
    private flag = false
    private pauseFlag = false;

    private video: { canvasDom?: HTMLCanvasElement, ctx?: CanvasRenderingContext2D } = {}
    private flex: number = 100;
    private basic = () => {
        if (this.video.canvasDom) {
        this.video.canvasDom.width = this.video.canvasDom.width;
        }
        this.video.ctx?.scale(this.flex, this.flex);
    }
    constructor(
        private eleref: ElementRef,
        private littleFuncs: LittleFuncsService
    ) { }

    ngOnChanges(changes: SimpleChanges): void {
        if (changes.hasOwnProperty('cameraLine') && changes['cameraLine'].currentValue) {
        if (!this.video.ctx) {
            return
        }
        if (this.pauseFlag) {
            return;
        }
        this.basic();
        this.drawPolygon(this.video.ctx, this.cameraLine, this.color, this.beta);
        }
    }
    ngOnInit(): void {
    }
    ngAfterContentInit(): void {
        // debugger
        // 获取DOM对象
        const videoElement = <HTMLVideoElement>this.eleref.nativeElement.querySelector('video');
        //获取canvas对象
        this.video.canvasDom = <HTMLCanvasElement>this.eleref.nativeElement.querySelector('canvas');
        try {
        if (mpegts.default.getFeatureList().mseLivePlayback) {
            if (this.isLive) {
            this.player = mpegts.default.createPlayer({
                type: 'flv',  // could also be mpegts, m2ts, flv
                isLive: true,
                hasAudio: false,    // 关闭声音
                url: `${this.cameraUrl}`
            }, {
                enableStashBuffer: false,
                liveBufferLatencyChasing: true,
                liveBufferLatencyMaxLatency: 1.1,
                liveBufferLatencyMinRemain: 0.1
            });
            this.player.attachMediaElement(videoElement);
            this.player.load();
            this.player.play();
            this.player.on('error', (err) => {
                this.flag = false;
                this.littleFuncs.errorNotification('mpegts play error!')
            })
            } else {
            videoElement.src = this.cameraUrl;
            }

        }

        } catch (error) {
        this.flag = false;
        this.littleFuncs.errorNotification('vviodeo js logic error!')
        }
        videoElement.addEventListener('canplay', (e) => {
        if (this.flag) {
            return;
        }
        if (this.video.canvasDom){
            this.video.canvasDom.width = videoElement.clientWidth;
            this.video.canvasDom.height = videoElement.clientHeight;
        }
        this.flex = Math.floor(768 / videoElement.videoWidth * 100000) / 100000;
        this.flag = true;
        if (this.video.ctx) {
            this.video.ctx = this.video.canvasDom?.getContext("2d")!;
            this.drawPolygon(this.video.ctx!, this.cameraLine, this.color, this.beta);
        }
        })
    }
    start(): void {
        this.pauseFlag = false;
        this.player.play();
    }

    pause(): void {
        this.pauseFlag = true;
        this.player.pause();
    }

    stop(): void {
        this.player.pause();
        this.player.unload();
        // 卸载DOM对象
        this.player.detachMediaElement();
        // 销毁flvjs对象
        this.player.destroy();
    }

    drawPolygon(ctx: CanvasRenderingContext2D, points: number[][], color: string, beta: number) {
        ctx.beginPath();
        points.forEach((ele, index) => {
        if (index) {
            ctx.lineTo(ele[0], ele[1]);
        } else {
            ctx.moveTo(ele[0], ele[1]);
        }
        });
        ctx.closePath();
        ctx.fillStyle = color;
        ctx.globalAlpha = beta;
        ctx.fill();
    }

    ngOnDestroy(): void {
        this.stop();
    }
    }
  ```

  **camera-by-flv.component.less** 
  ```
  .video-box {
    position: relative;
    width:max-content;
    height:361px;
    video {
        height: inherit;
    }
    canvas{
        position:absolute;
        left:0;
        top:0;
        pointer-events: none;
    }
  }
  ```