---
title: Erbjuder inställningar för en Power BI-appen erbjuder – Azure Marketplace | Microsoft Docs
description: Konfigurera inställningar för erbjudande till en Power BI-appen för Microsoft AppSource-marknadsplatsen.
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
ms.openlocfilehash: 49bbe5dcf17a9aa20cb47f477c59e7115d29dacc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819607"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI-appar erbjuder inställningsflik

När du öppnar den **nytt erbjudande** sidan för service-appar du först se den **erbjuder inställningar** fliken. Du kan ange de primära identifierarna och namn för ditt erbjudande för på den här fliken. En asterisk (*) anger ett obligatoriskt fält.

![Fliken Inställningar för erbjudandet](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Erbjudandet inställningar fält 

På den **erbjuder inställningar** fliken måste du ange information i följande obligatoriska fält:

|  Fält        |  Beskrivning                                                               |
|---------------|----------------------------------------------------------------------------|
| **Erbjudande-ID**  | En unik identifierare (inom en utgivarprofil för) för erbjudandet. Den här identifieraren kommer att synas på produkten URL: er, Azure Resource Manager-mallar och fakturering rapporter. Den maximala längden är 50 tecken. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). Det får inte avslutas med ett bindestreck. Den här identifieraren kan inte ändras när ett erbjudande lanseras. ID för erbjudande om Contoso publicerar ett erbjudande med `sample-SvcApp`, erbjudandet som är tilldelad AppSource-URL: en `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Utgivare** | Unik identifierare för din organisation i [AppSource](https://appsource.microsoft.com). Alla dina erbjudanden som ska associeras med ditt publisher-ID. Det här värdet kan inte ändras när erbjudandet har sparats.                         |
| **Namn**      | Ett visningsnamn för ditt erbjudande. Det här namnet visas på AppSource och på partnerportalen i molnet. Den maximala längden är 50 tecken. Använd ett varumärke som känns igen för din produkt. Omfattar inte organisationens namn här om inte appen släpps med det namnet. Om det här erbjudandet ger på andra webbplatser och publiceringar, ska du använda samma namn i alla publikationer.    <br/>Om du publicerar ett erbjudande för förhandsversionen för Power BI-appar lägger du till strängen `(Preview)` i slutet av programmets namn, så här: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Nästa steg

På nästa flik, ska du ange [teknisk information](./cpp-technical-info-tab.md) för ditt erbjudande.
