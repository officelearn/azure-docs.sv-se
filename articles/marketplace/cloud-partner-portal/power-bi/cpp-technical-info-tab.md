---
title: Teknisk information till en Power BI-appen – Azure Marketplace | Microsoft Docs
description: Konfigurera teknisk information fält till en Power BI-appen för Microsoft AppSource Marketplace.
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
ms.openlocfilehash: d96ef2fd318d6164e1b7dfc5c4b72d6957af0f3e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744442"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI-appar teknisk informationsfliken

Den **teknisk information** fliken den **nytt erbjudande** sidan kan du ge Power BI-installationsprogrammet paketet URL och eventuella ytterligare information som krävs för det nya erbjudandet verifiering.  För denna första version är är alla Power BI-appar kostnadsfria, hämtas från AppSource utan extra kostnad. Därför kommer du inte att kunna definiera alla lager som enheter (SKU: er) för den här erbjudandetypen.

![Teknisk informationsfliken](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Teknisk information om fält 

I den **teknisk information** fliken måste du ange följande fält.  En tillagda asterisk (*) för fältet etiketten visar att det krävs.

|        Fält          |  Beskrivning                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Installationsprogrammet URL**     | Adressen som genererats av Power BI när du publicerar appen och skicka det till produktion.  Mer information om hur du skapar URL: en finns i [publicera service-appar i Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Anvisningar för verifiering**  |  Valfri text som instruktioner (max 3000 tecken) för Microsoft-verifieringsteamet Aktivitetsanteckningar konfigurerar, ansluter och testa din app, inklusive: vanliga konfigurationsinställningar, testkonton eller parametrar som kan användas för att testa alternativet ”Anslut Data” osv. Den här informationen kommer endast att visas för verifieringsteamet och används endast för verifiering.  |
| **Den här appen har skapats som ett innehållspaket för Power BI?** | Det här är för närvarande ett fält som används internt. Lämna värdet satt till dess standardvärde, `No`, i annat fall ändrar det här fältet till `Yes` kan inverka negativt på publicering.  |  
|  |  |


## <a name="next-steps"></a>Nästa steg

I nästa [Storefront information](./cpp-storefront-details-tab.md) fliken du tillhandahåller marknadsföring och juridisk information för din app.

