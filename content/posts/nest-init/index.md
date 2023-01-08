---
title: "Nest init"
date: "2023-01-07"
description: "Very step by step article to start a nest project"
images: ["posts/nest-init/nest.jpg"]
tags: [nest]
---

![vagrant](nest.jpg)

> You can find the source-code of this tutorial at: [https://github.com/ynwd/nest-init](https://github.com/ynwd/nest-init)

## Create app
```bash
# install nest-cli
$ npm install -g @nestjs/cli

# create a new web app
$ nest new my-nest-project

# change directory
$ cd my-nest-project

# run web app
$ npm run start:dev
```

Open you browser and type this url: [http://localhost:3000](http://localhost:3000)

## Generating a new resource
```bash
# create a new CRUD resource named user at modules folder
# the '--no-spec' option will tell nest-cli not to generate test file
$ nest g res modules/user --no-spec
```


It will automatically generate the below file and register it to `app.module.ts`:
- `user.module.ts`
- `user.controller.ts`
- `user.service.ts`
- `user.entity.ts`
- `create-user.dto.ts`
- `update-user.dto.ts`

Open [http://localhost:3000/user](http://localhost:3000/user). You will be surprised if all the endpoints for CRUD are created automatically.

You can adjust the files above to suite your use case.

## Create a new module, service, and controller

If resource generator doesn't suit your needs, you can also create manually with these command

```bash
# create new module at modules folder, named auth
$ nest g mo modules/auth --no-spec

# create new service at modules folder, named auth
$ nest g s modules/auth --no-spec

# create new controlle at modules folder, named auth
$ nest g co modules/auth --no-spec
```

It will automatically generate the below file and register it to `app.module.ts`:
- `auth.module.ts`
- `auth.controller.ts`
- `auth.service.ts`

