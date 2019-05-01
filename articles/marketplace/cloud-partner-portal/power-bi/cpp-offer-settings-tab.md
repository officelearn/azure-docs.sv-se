---
title: Erbjudandeinställningar till en Power BI-appen | Azure Marketplace
description: Konfigurera inställningar för erbjudande till en Power BI-appen för Microsoft AppSource-marknadsplatsen.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: be2c2b4f5d9461aa0fdc6dde89931ed4b6418ced
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943468"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI-appar erbjuder inställningsflik

När du öppnar den **nytt erbjudande** sidan för service-appar du först se den **erbjuder inställningar** fliken. Du kan ange de primära identifierarna och namn för ditt erbjudande för på den här fliken. En asterisk (*) anger ett obligatoriskt fält.

![Fliken Erbjudandeinställningar](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Erbjudandet inställningar fält 

På den **erbjuder inställningar** fliken måste du ange information i följande obligatoriska fält. Required fields are indicted by an asterisk (*).

|  Fält        |  Beskrivning                                                               |
|---------------|----------------------------------------------------------------------------|
| **Erbjudande-ID\***  | En unik identifierare (inom en utgivarprofil för) för erbjudandet. Den här identifieraren kommer att synas på produkten URL: er, Azure Resource Manager-mallar och fakturering rapporter. Den maximala längden är 50 tecken. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). Det får inte avslutas med ett bindestreck. Den här identifieraren kan inte ändras när ett erbjudande lanseras. ID för erbjudande om Contoso publicerar ett erbjudande med `sample-SvcApp`, erbjudandet som är tilldelad AppSource-URL: en `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publisher\*** | Unik identifierare för din organisation i [AppSource](https://appsource.microsoft.com). Alla dina erbjudanden som ska associeras med ditt publisher-ID. Det här värdet kan inte ändras när erbjudandet har sparats.                         |
| **Namn\***      | Ett visningsnamn för ditt erbjudande. Det här namnet visas på AppSource och på partnerportalen i molnet. Den maximala längden är 50 tecken. Använd ett varumärke som känns igen för din produkt. Omfattar inte organisationens namn här om inte appen släpps med det namnet. Om det här erbjudandet ger på andra webbplatser och publiceringar, ska du använda samma namn i alla publikationer.    <br/>Om du publicerar ett erbjudande för förhandsversionen för Power BI-appar lägger du till strängen `(Preview)` i slutet av programmets namn, så här: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Nästa steg

På nästa flik, ska du ange [teknisk information](./cpp-technical-info-tab.md) för ditt erbjudande.
