<!--
 * @Author: Null Zhao
 * @Date: 2022-04-07 09:37:03
 * @LastEditors: Null Zhao
 * @LastEditTime: 2022-04-08 10:25:43
 * @FilePath: \my-docs\docs\opening-project-preparation.md
 * @Description: 
 * ctrl+alt+i ctrl+win+t win+y
 * Copyright (c) 2022 by null, All Rights Reserved. 
-->
- 技术栈

  找tec lead确认
| 领域 |	技术 | 
| --- |	--- | 
| UI |	Angular | 
| DB |	PostgreSQL DB |
| Data Bus | 	Kafka |
| Data Transfer |	Nifi |
| Backend |	Loopback4 |
| Portal搭建 |	IDM |
| 文件存储 |	minio |

- 部署(rollout)策略
  - 部署在一朵云供各厂使用
  - 在各厂单独部署

- 程式架构

  [SystemArchitecture模板.pptx](./_assets/SystemArchitecture模板.pptx "System Architecture模板")

- 敏捷工具
  - [leangoo](https://www.leangoo.com/)
  - Jira

- gitlab

  建立项目，code放在对应grouup/projects下
  
- Rancher申请

  发邮件至Tyler Zou/WZS/Wistron，内容如下：

  附件：[Racher资源申请.xlsx](./_assets/Racher%E8%B5%84%E6%BA%90%E7%94%B3%E8%AF%B7.xlsx)

> Dear Tyler，
> 
> &nbsp;&nbsp;&nbsp;&nbsp;现有“智慧消防”项目需要申请rancher资源，请帮忙建立，详情见附件。  

- matomo申请

  [台北](https://consolewcp.wistron.com/#/Resourcelist "creat resource即可")

  [中山](null "未知")

- idm申请

  发邮件至Ben Chan/WZS/Wistron，内容如下：

  附件：[智慧消防IDM-Client申请.xlsx](./_assets/%E6%99%BA%E6%85%A7%E6%B6%88%E9%98%B2IDM-Client%E7%94%B3%E8%AF%B7.xlsx)
  > Dear Ben,
  >
  >智慧消防系统需要Web统一身份认证，故申请local、dev、qas、prd四个环境的IDM client ，申请的具体信息如附件所示，非常感谢！
  >
  > 智慧消防系统：
  >
  > 专案: m1，
  >
  > teach leader: Jonson Yang，
  >
  > 主要功能：消防设施管理和消防管理。

- CICD

  [教程](https://gitlab.wistron.com/Z19071449/docs/-/tree/master/examples)

- db申请
  1. 填电子表单

    ![db申请表单.png](./_assets/db%E7%94%B3%E8%AF%B7%E8%A1%A8%E5%8D%95.png)

    !> 若未看到相关选项，请联系管理员开通权限

  2. 发邮件至Jane Lee, Zekai Yang，内容如下：

    附件: [系统申请表](./_assets/%E7%B3%BB%E7%B5%B1%E7%94%B3%E8%AB%8B%E8%A1%A8.docx)

    > 我们最近要开始M360 M1智慧消防项目，需要PostgreSQL DB。附件是申请PostgreSQL DB所需要材料，请帮忙申请，谢谢。
    >
    > 1.空间需求：
    > DEV & QAS & PRD: 2G
    >
    > 2.架构图：
    > ![智慧消防架构图](./_assets/%E6%99%BA%E6%85%A7%E6%B6%88%E9%98%B2%E6%9E%B6%E6%9E%84%E5%9B%BE.png)
    >
    > 3.申请原因：
    > ![db申请原因](./_assets/db%E7%94%B3%E8%AF%B7%E5%8E%9F%E5%9B%A0.png)
    >
    > 4.电子表单号：SL22012717
    
  !> 审核人员关注点为空间需求，功能，重要性描述，使用时长。  
   一般会先给dev测试db是否可以连接再给后续环境地址。