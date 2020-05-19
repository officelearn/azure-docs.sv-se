---
title: Vad är Azure static Web Apps?
description: Viktiga funktioner och funktioner i Azures statiska Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: d416044599535e8acd363d09099e8667bba59a0a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599969"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Vad är Azures statiska Web Apps för hands version?

Azures statiska Web Apps är en tjänst som automatiskt skapar och distribuerar fullständiga stack-webbappar till Azure från en GitHub-lagringsplats.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Översikt över statiska Web Apps":::

Arbets flödet för den statiska Azure-Web Apps är anpassat till en utvecklares dagliga arbets flöde. Appar skapas och distribueras baserat på GitHub-interaktioner.

När du skapar en statisk Azure Web Apps-resurs ställer Azure in ett arbets flöde för GitHub åtgärder i appens käll kods lager som övervakar en valfri gren. Varje gång du push-skickar eller accepterar pull-begäranden till den bevakade grenen skapar och distribuerar GitHub-åtgärden automatiskt appen och dess API till Azure.

Statiska webbappar skapas ofta med hjälp av bibliotek och ramverk som vinkel, reagera, svelte eller Vue. Dessa appar innehåller HTML-, CSS-, Java Script-och bild till gångar som utgör programmet. Med en traditionell webb server betjänas dessa till gångar från en enda server tillsammans med eventuella nödvändiga API-slutpunkter.

Med statiska Web Apps separeras statiska till gångar från en traditionell webb server och betjänas i stället från platser geografiskt fördelade över hela världen. Den här distributionen gör det mycket snabbare att betjäna filer eftersom filerna är fysiskt närmare slutanvändarna. Dessutom är API-slutpunkterna värdbaserade med en [arkitektur utan server](../azure-functions/functions-overview.md), vilket gör att du inte behöver ha en fullständig backend-server samtidigt.

## <a name="key-features"></a>Huvudfunktioner

- **Gratis webbhotell** för statiskt innehåll som HTML, CSS, Java Script och avbildningar.
- **Inbyggt API** -stöd som tillhandahålls av Azure Functions.
- **GitHub-integration från första part** där lagrings ändringar utlöser versioner och distributioner.
- **Globalt distribuerat** statiskt innehåll, vilket ger innehållet närmare dina användare.
- **Kostnads fria SSL-certifikat**, som automatiskt förnyas.
- **Anpassade domäner** \* för att tillhandahålla anpassade anpassningar till din app.
- **Sömlös säkerhets modell** med en omvänd proxy vid anrop till API: er som inte kräver CORS-konfiguration.
- **Integrering av autentiseringsprovider** med Azure Active Directory, Facebook, Google, GitHub och Twitter.
- **Anpassningsbar roll definition** och tilldelningar för auktorisering.
- **Regler för routning på Server sidan** möjliggör fullständig kontroll över det innehåll och de vägar som du hanterar.
- **Genererade produktions versioner** som drivs av pull-begäranden som aktiverar för hands versioner av din webbplats innan publicering.

## <a name="what-you-can-do-with-static-web-apps"></a>Vad du kan göra med statiska Web Apps

- **Bygg moderna JavaScript-program** med ramverk och bibliotek som [vinkel](https://angular.io/), [reagera](https://reactjs.org/), [svelte](https://svelte.dev/), [Vue](https://vuejs.org/) med en [Azure Functions](https://azure.microsoft.com/services/functions/) backend.
- **Publicera statiska platser** med ramverk som [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Distribuera webb program** med ramverk som [Nästa. js](deploy-nextjs.md) och [Nuxt. js](deploy-nuxtjs.md).

\*Spetsig domän registreringar stöds inte under för hands versionen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bygg din första statiska app](getting-started.md)
