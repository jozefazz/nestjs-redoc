
## NestJS ReDoc (Swagger UI Alternative) - Jozefazz Version

## Introduction
I have tried most of the Redoc integrate NestJS solutions available on the market, but due to a lack of maintenance over a long period, many outdated packages were being used, leading to installation failures. This version has been modified and improved, allowing for perfect integration with NestJS 10.0.0

-Jozefazz
## Features

- Customizable theme
- It's almost a drop in replacement for you current swagger UI, you only need to import this package and modify any settings you may want to change (e.g: Page title, ReDoc options)

NB: Please read the [ReDoc](https://redocly.com/docs/redoc/) Documentation firstly.

## 🗿 Installation

Using npm: `npm install @jozefazz/nestjs-redoc`


## How to use

You need to install the [Swagger Module](https://github.com/nestjs/swagger) first if you want to get definitions updated with your project.

In future versions you will be able to pass a URL parameter as document, but for the moment you need this document object from the swagger module

```typescript
const options = new DocumentBuilder()
  .setTitle('Look, i have a title')
  .setDescription('A very nice description')
  .setBasePath('/api/v1')
  .build();
const document = SwaggerModule.createDocument(app, options);
```

Then add the following example code.

**Note**: All properties are optional, if you don't specify a title we will fallback to the one you used in your DocumentBuilder instance.

```typescript
const redocOptions: RedocOptions = {
  title: 'Hello Nest',
  logo: {
    url: 'https://redocly.github.io/redoc/petstore-logo.png',
    backgroundColor: '#F0F0F0',
    altText: 'PetStore logo'
  },
  sortPropsAlphabetically: true,
  hideDownloadButton: false,
  hideHostname: false,
  auth: {
    enabled: true,
    user: 'admin',
    password: '123'
  },
  tagGroups: [
    {
      name: 'Core resources',
      tags: ['cats'],
    },
  ],
};
// Instead of using SwaggerModule.setup() you call this module
await RedocModule.setup('/docs', app, document, redocOptions);
```

### You can build a function for the setup
Create a file "setup.redoc.ts"
```typescript
import type { INestApplication } from '@nestjs/common';
import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';
import { RedocModule, RedocOptions } from '@jozefazz/nestjs-redoc';
import * as process from 'process';

export async function setupRedoc(app: INestApplication) {
  const documentBuilder = new DocumentBuilder()
    .setTitle(`${process.env.DOCS_TITLE}`)
    .setVersion('1.0')
    .setDescription('Powered by Jozefazz')
    .addBearerAuth(
      {
        description: `Please enter token in following format: Bearer <JWT>`,
        name: 'Authorization',
        bearerFormat: 'Bearer',
        scheme: 'Bearer',
        type: 'http',
        in: 'Header',
      },
      'access-token',
    );

  if (process.env.API_VERSION) {
    documentBuilder.setVersion(process.env.API_VERSION);
  }

  const document = SwaggerModule.createDocument(app, documentBuilder.build());
  const redocOptions: RedocOptions = {
    title: `${process.env.DOCS_TITLE}`,
    logo: {
      url: `${process.env.DOCS_LOGO}`,
      backgroundColor: '#d0e8c5',
      altText: 'LOGO',
    },
    theme: {
      typography: {
        fontSize: '16px',
        fontWeightBold: '900',
      },
      sidebar: {
        backgroundColor: '#d0e8c5',
      },
      rightPanel: {
        backgroundColor: '#01312b',
      },
    },
    sortPropsAlphabetically: true,
    sortOperationsAlphabetically: true,
    hideDownloadButton: false,
    hideHostname: false,
    noAutoAuth: true,
    pathInMiddlePanel: true,
    auth: {
      enabled: true,
      user: 'admin',
      password: `${process.env.DOCS_PASSWORD}`,
    },
    tagGroups: [
      {
        name: 'Core resources',
        tags: ['authentication', 'user'],
      },
    ],
  };
  await RedocModule.setup('docs', app, document, redocOptions);
  console.info(
    `Redoc Documentation: http://localhost:${process.env.PORT}/docs`,
  );
}

```
In your main.ts, add this line before "await app.listen(3000);"
```
await setupRedoc(app);
```

## Available options

### Redoc Options

| Option                       | Description                                                                                                                                           | Type              | Note                                       |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- | ------------------------------------------ |
| title                        | Web site title (e.g: ReDoc documentation)                                                                                                             | string            |
| favicon                      | Web site favicon URL                                                                                                                                  | string            | Fallbacks to the document title if not set |
| logo                         | Logo options                                                                                                                                          | LogoOptions       | See LogoOptions table                      |
| theme                        | Theme options                                                                                                                                         | ThemeOptions      | See ThemeOptions info                      |
| untrustedSpec                | If set, the spec is considered untrusted and all HTML/markdown is sanitized to prevent XSS, by default is false                                       | boolean           |
| supressWarnings              | If set, warnings are not rendered at the top of documentation (they are still logged to the console)                                                  | boolean           |
| hideHostname                 | If set, the protocol and hostname won't be shown in the operation definition                                                                          | boolean           |
| expandResponses              | Specify which responses to expand by default by response codes, values should be passed as comma-separated list without spaces (e.g: 200, 201, "all") | string            |
| requiredPropsFirst           | If set, show required properties first ordered in the same order as in required array                                                                 | boolean           |
| sortPropsAlphabetically      | If set, propeties will be sorted alphabetically                                                                                                       | boolean           |
| sortOperationsAlphabetically | If set, operations will be sorted alphabetically                                                                                                      | boolean           |
| showExtensions               | If set the fields starting with "x-" will be shown, can be a boolean or a string with names of extensions to display                                  | boolean           |
| noAutoAuth                   | If set, redoc won't inject authentication section automatically                                                                                       | boolean           |
| pathInMiddlePanel            | If set, path link and HTTP verb will be shown in the middle panel instead of the right one                                                            | boolean           |
| hideLoading                  | If set, loading spinner animation won't be shown                                                                                                      | boolean           |
| nativeScrollbars             | If set, a native scrollbar will be used instead of perfect-scroll, this can improve performance of the frontend for big specs                         | boolean           |
| hideDownloadButton           | This will hide the "Download spec" button, it only hides the button                                                                                   | boolean           |
| disableSearch                | If set, the search bar will be disabled                                                                                                               | boolean           |
| onlyRequiredInSamples        | Shows only required fileds in request samples                                                                                                         | boolean           |
| auth                         | Auth options                                                                                                                                          | AuthOptions       | See AuthOptions info                       |
| AuthOptions info             |
| enabled                      | If enabled, a prompt will pop out asking for authentication details, default: `false`                                                                 | boolean           |
| user                         | User name, default: `admin`                                                                                                                           | string            |
| password                     | User password, default: `123`                                                                                                                         | string            |
| tagGroups                    | Tag groups options                                                                                                                                    | TagGroupOptions[] | See Tag Group options                      |
| Tag Group options info       |
| name                         | Tag name                                                                                                                                              | string            |
| tags                         | Tag collection                                                                                                                                        | string[]          |
| redocVersion                 | Set an specific redoc version                                                                                                                         | string,number     | By default it's "latest"                   |




**Note**: If you want to change your ReDoc theme settings, take a look at the official ReDoc documentation: <https://github.com/Redocly/redoc/blob/master/src/theme.ts>

Apply the properties defined in ResolvedThemeInterface to the key called "theme" in the redoc options

### Logo options

| Option          | Description                                                                           | Type   | Example  |
| --------------- | ------------------------------------------------------------------------------------- | ------ | -------- |
| url             | The URL pointing to the spec Logo, must be in the format of a URL and an absolute URL | string |
| backgroundColor | Background color to be used, must be RGB color in hexadecimal format (e.g: #008080)   | string | #F0F0F0  |
| altText         | Alt tag for Logo                                                                      | string | PetStore |
| href            | href tag for Logo, it defaults to the host used for your API spec                     | string |

### Special Thanks
Forked from https://github.com/ojoanalogo/nestjs-redoc