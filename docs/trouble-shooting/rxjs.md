!> toPromise()弃用

```
import { lastValueFrom } from 'rxjs';
lastValueFrom(this.http.get('../assets/monitoringConfig.json')).then((test: any) => {
  console.log(test.url);
})
```