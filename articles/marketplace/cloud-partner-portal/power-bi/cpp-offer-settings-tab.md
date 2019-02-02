---
title: Erbjuder inställningar för en Power BI-appen erbjuder – Azure Marketplace | Microsoft Docs
description: Konfigurera inställningar för erbjudandet för ett erbjudande med Power BI-appen för Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666788"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI-appar erbjuder inställningsflik

Den **nytt erbjudande** för service-appar öppnas i den första fliken med namnet **erbjuder inställningar**.  Du ger det primära ID: n och namn för ditt erbjudande i den här fliken.  En tillagda asterisk (*) på fältnamnet indikerar att det krävs.

![Fliken Inställningar för erbjudandet](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Erbjudandet inställningar fält 

I den **erbjuder inställningar** fliken måste du ange följande obligatoriska fält.

|  Fält        |  Beskrivning                                                               |
|---------------|----------------------------------------------------------------------------|
| **Erbjudande-ID**  | En unik identifierare (inom en utgivarprofil för) för erbjudandet. Den här identifieraren kommer att synas på produkten URL: er, Resource Manager-mallar och fakturering rapporter. Det har högst 50 tecken, endast bestå av alfanumeriska gemener och bindestreck (-), men får inte sluta med ett bindestreck. Det här fältet kan inte ändras när ett erbjudande lanseras. Till exempel om Contoso publicerar ett erbjudande med ID för erbjudande `sample-SvcApp`, den är tilldelad AppSource-URL: en `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Utgivare** | Unik identifierare för din organisation i [AppSource](https://appsource.microsoft.com). Alla dina erbjudanden som ska associeras med ditt publisher-ID. Det här värdet kan inte ändras när erbjudandet har sparats.                         |
| **Namn**      | Visningsnamn för ditt erbjudande. Det här namnet visas i AppSource och i partnerportalen i molnet. Det får innehålla högst 50 tecken. Här vägledningen är att inkludera ett beskrivande namn för varumärke för din produkt. Omfattar inte organisationens namn här såvida detta inte hur appen släpps. Om du marknadsföring det här erbjudandet i andra webbplatser och publiceringar, kan du kontrollera att namnet är samma i alla publikationer.    <br/>Om du publicerar ett erbjudande för förhandsversionen av Power BI-appar, förhandsgranskningsläge läggs strängen `(Preview)` att programmets namn, till exempel `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Nästa steg

Med nästa flik du vill specificera [teknisk information](./cpp-technical-info-tab.md) för ditt erbjudande.
