---
title: Konfigurera klient dels ramverk med för hands versionen av Azure statisk Web Apps
description: Inställningar för populära front-end-ramverk som krävs för Azures statiska Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 4b1bc58b6b4a87cd6e5e09e83020a38261b8746f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905376"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Konfigurera klient dels ramverk och bibliotek med Azures statiska Web Apps för hands version

Den statiska Azure-Web Apps kräver att du har rätt konfigurations värden i [build-konfigurationsfilen](github-actions-workflow.md) för ditt klient ramverk eller bibliotek.

## <a name="configuration"></a>Konfiguration

I följande tabell visas inställningarna för en serie ramverk och bibliotek<sup>1</sup>.

Avsikten med tabell kolumnerna förklaras av följande objekt:

- **Plats för app-artefakt**: visar värdet för `app_artifact_location` , som är [mappen för inbyggda versioner av programfiler](github-actions-workflow.md#build-and-deploy).

- **Anpassat build-kommando**: när ramverket kräver ett annat kommando än `npm run build` eller `npm run azure:build` , kan du definiera ett [anpassat build-kommando](github-actions-workflow.md#custom-build-commands).

| Ramverk | Plats för appartefakt | Anpassat build-kommando |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | ej tillämpligt <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Vinkel universell](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | Saknas |
| [Backbone.js](https://backbonejs.org/) | `/` | Saknas |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | Saknas |
| [Ember](https://emberjs.com/) | `dist` | Saknas |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | Saknas |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | Saknas |
| [Hyperapp](https://hyperapp.dev/) | `/` | Saknas |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | Saknas |
| [jQuery](https://jquery.com/) | `/` | Saknas |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | Saknas |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | Saknas |
| [Marko](https://markojs.com/) | `public` | Saknas |
| [Meteor](https://www.meteor.com/) | `bundle` | Saknas |
| [Mithril](https://mithril.js.org/) | `dist` | Saknas |
| [Mera](https://www.polymer-project.org/) | `build/default` | Saknas |
| [Preact](https://preactjs.com/) | `build` | Saknas |
| [React](https://reactjs.org/) | `build` | Saknas |
| [>](https://stenciljs.com/) | `www` | Saknas |
| [Svelte](https://svelte.dev/) | `public` | Saknas |
| [Three.js](https://threejs.org/) | `/` | Saknas |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | Saknas |
| [Vue.js](https://vuejs.org/) | `dist` | Saknas |

<sup>1</sup> tabellen ovan är inte avsedd att vara en fullständig lista över ramverk och bibliotek som fungerar med Azures statiska Web Apps.

<sup>2</sup> inte tillämpligt

## <a name="next-steps"></a>Nästa steg

- [Versions- och arbetsflödeskonfiguration](github-actions-workflow.md)
