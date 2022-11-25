# Фабрики (Factory providers) в Angular

Если мы заранее не знаем, какой именно сервис мы хотим инжектировать и
использовать в компоненте, мы можем в провайдере использовать фабрику-провайдер,
которая, в зависимости от каких-либо параметров инжектирует тот или иной сервис
в компонент.

Например, наш компонент может загружать данные из localStorage в оффлайн режиме
и с сервера в онлайн режиме. За это могут отвечать разные сервисы. Чтобы понять,
какой именно сервис инжектировать в компонент, мы используем factory provider.

```ts
import { Component, OnInit } from '@angular/core';
import { IsOnlineService } from './services/is-online.service';
import { LocalService } from './services/local.service';
import { OnlineService } from './services/online.service';

const dataFactory = (isOnline: IsOnlineService) => {
  if (!isOnline.isOnline) {
    return new LocalService();
  } else {
    return new OnlineService();
  }
}

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss'],
  providers: [{provide: OnlineService, useFactory: dataFactory, deps: [IsOnlineService]}],
})
export class AppComponent implements OnInit {
  title = 'test';

  dataSource!: string;
  isOnlineStatus!: boolean;

  constructor(private data: OnlineService, private isOnline: IsOnlineService){}

  ngOnInit() {
    this.dataSource = this.data.data; // = "I am local!"
    this.isOnlineStatus = this.isOnline.isOnline; // = false
  }
}
```

Тут в случае режима офлайн мы подменяем наш сервис подачи данных компоненту
чтобы он мог продолжать работать.
