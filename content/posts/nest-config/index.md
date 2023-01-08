---
title: "Nest config"
date: "2023-01-08"
description: "Very step by step article to setup configuration in nest"
images: ["posts/nest-init/nest.jpg"]
tags: [nest]
---

> You can find the source code of this tutorial at: [https://github.com/ynwd/nest-init/tree/config](https://github.com/ynwd/nest-init/tree/config)


Install nest-config
```bash
$ npm i --save @nestjs/config 
```

Import config service (src/app.module.ts)
```ts
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { UserModule } from './modules/user/user.module';
import { AuthModule } from './modules/auth/auth.module';
import { ConfigModule } from '@nestjs/config';

@Module({
  imports: [ConfigModule.forRoot({ isGlobal: true }), UserModule, AuthModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}

```

> The @nestjs/config package internally uses dotenv.

The above code will load and parse a .env file from the default location (the project root directory), merge key/value pairs from the .env file with environment variables assigned to process.env, and store the result in a private structure that you can access through the ConfigService. 

Next, inject config service on database provider (src/database/database.providers.ts)
```ts
import { ConfigService } from '@nestjs/config';
import { DATA_SOURCE } from 'src/common/constants';
import { DataSource } from 'typeorm';

export const databaseProviders = [
  {
    provide: DATA_SOURCE,
    useFactory: async (configService: ConfigService) => {
      const dataSource = new DataSource({
        type: 'postgres',
        host: configService.get('DB_HOST'),
        port: configService.get<number>('DB_PORT'),
        username: configService.get('DB_USERNAME'),
        password: configService.get('DB_PASSWORD'),
        database: configService.get('DB_NAME'),
        synchronize: configService.get<boolean>('DB_SYNC'),
        entities: [__dirname + '/../**/*.entity{.ts,.js}'],
      });

      return dataSource.initialize();
    },
    inject: [ConfigService],
  },
];

```

This is the `.env` file that define app environment

```
DB_NAME=test
DB_USERNAME=postgres
DB_PASSWORD=root
DB_PORT=5432
DB_HOST=localhost
DB_SYNC=true
```
