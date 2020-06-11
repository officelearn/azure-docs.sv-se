---
title: Konfigurera klient dels ramverk med för hands versionen av Azure statisk Web Apps
description: Inställningar för populära front-end-ramverk som krävs för Azures statiska Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: cshoe
ms.openlocfilehash: 1da2403cc75d979ca34c96caff5ec7cffab7ffb0
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84680030"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Konfigurera klient dels ramverk och bibliotek med Azures statiska Web Apps för hands version

Den statiska Azure-Web Apps kräver att du har rätt konfigurations värden i [build-konfigurationsfilen](github-actions-workflow.md) för ditt klient ramverk eller bibliotek.

## <a name="configuration"></a>Konfiguration

I följande tabell visas inställningarna för en serie ramverk och bibliotek<sup>1</sup>.

Avsikten med tabell kolumnerna förklaras av följande objekt:

- **Plats för app-artefakt**: visar värdet för `app_artifact_location` , som är [mappen för inbyggda versioner av programfiler](github-actions-workflow.md#build-and-deploy).

- **Anpassat build-kommando**: när ramverket kräver ett annat kommando än `npm run build` eller `npm run azure:build` , kan du definiera ett [anpassat build-kommando](github-actions-workflow.md#custom-build-commands).

| Ramverk | Plats för app-artefakt | Anpassat build-kommando |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | ej tillämpligt <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Vinkel universell](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | saknas |
| [Backbone.js](https://backbonejs.org/) | `/` | saknas |
| [Ember](https://emberjs.com/) | `dist` | saknas |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Glimmer](https://glimmerjs.com/) | `dist` | saknas |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | saknas |
| [Hyperapp](https://hyperapp.dev/) | `/` | saknas |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | saknas |
| [jQuery](https://jquery.com/) | `/` | saknas |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | saknas |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | saknas |
| [Marko](https://markojs.com/) | `public` | saknas |
| [Meteor](https://www.meteor.com/) | `bundle` | saknas |
| [Mithril](https://mithril.js.org/) | `dist` | saknas |
| [Mera](https://www.polymer-project.org/) | `build/default` | saknas |
| [Preact](https://preactjs.com/) | `build` | saknas |
| [Reagera](https://reactjs.org/) | `build` | saknas |
| [>](https://stenciljs.com/) | `www` | saknas |
| [Svelte](https://svelte.dev/) | `public` | saknas |
| [Three.js](https://threejs.org/) | `/` | saknas |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | saknas |
| [Vue](http://vuejs.com/) | `dist` | saknas |

<sup>1</sup> tabellen ovan är inte avsedd att vara en fullständig lista över ramverk och bibliotek som fungerar med Azures statiska Web Apps.

<sup>2</sup> inte tillämpligt

## <a name="next-steps"></a>Nästa steg

- [Versions- och arbetsflödeskonfiguration](github-actions-workflow.md)
