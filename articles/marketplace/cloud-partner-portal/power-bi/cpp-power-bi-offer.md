---
title: Power BI-appen offer – Azure Marketplace | Microsoft Docs
description: Hur du publicerar en Power BI-appen till Microsoft AppSource-marknadsplatsen.
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
ms.date: 03/27/2019
ms.author: pbutlerm
ms.openlocfilehash: f18a1b05e5a38a79945d8df6706dd2147d6b43df
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009724"
---
# <a name="power-bi-app-offer"></a>Erbjudande för Power BI-appen

|              |                                |
|--------------|--------------------------------|
| Den här artikeln förklarar hur du publicerar en Power BI-App till Microsofts [AppSource-marknadsplatsen](https://appsource.microsoft.com/).  En Power BI-app-paket anpassningsbara Power BI-innehåll, inklusive datauppsättningar, rapporter och instrumentpaneler. Du kan distribuera appen till andra Power BI-klienter via AppSource, utföra justeringar och anpassningar som tillåts av utvecklaren och ansluter den till dina egna data. | ![Power BI icon](./media/powerbi-icon.png) |


Den här artikeln är uppdelad i tre delar:

-   [Krav för](./cpp-prerequisites.md). De tekniska krav och affärskrav för att skapa och publicera ett erbjudande med Power BI-appen.
-   [Skapa en Power BI-appen erbjudande](./cpp-create-offer.md). Så här skapar du en Power BI-appen erbjudandet post med hjälp av den [Cloud Partner Portal](https://cloudpartner.azure.com).
-   [Publicera ett erbjudande med Power BI-appen](./cpp-publish-offer.md). Hur du skickar in ett nytt erbjudande till AppSource för publicering och hur du uppdaterar ett befintligt erbjudande.


## <a name="publishing-steps"></a>Publicera steg

Här är de övergripande stegen för att publicera ett erbjudande med Power BI-appen:

![Power BI-appen erbjuder publishing steg](media/publishing-steps.png)

Här är Power BI-appen erbjudandet publiceringsprocessen:

1. Skapa en App i Power BI. Den här åtgärden genererar en paketet installationen URL, som representerar den primära tekniska tillgången till erbjudandet. Främja också testpaket till Förproduktion just nu. Mer information finns i [vad är Power BI-mall appar?](https://docs.microsoft.com/power-bi/service-template-apps-overview). 
2. Samla in eller skapa erbjudandets marknadsföringsmaterial, inklusive: officiellt namn, beskrivning, logotyper, osv. 
3. Samla in eller skapa erbjudandets juridiska krav och stöder dokument: *användningsvillkoren*, *sekretesspolicy*, *Supportpolicy*, Användarhjälp och så vidare.
4. Skapa erbjudandet: använda Cloud Partner Portal för att konfigurera information om erbjudandet, inklusive erbjudande-beskrivning, marknadsföringsmaterial, juridisk information, supportinformation och tillgångsspecifikationer.  När erbjudandet anges helt, skicka in för publicering.
5. Övervaka publiceringsprocessen i partnerportalen i molnet.  Det här steget AppSource onboarding-teamet testar, validerar och certifierar att ditt program. 
6. När appen har certifierats, granska i dess testmiljö och frigör den. 
7. Power BI-appen visas på AppSource (det ”går live”).
8. Flytta upp paketet Förproduktion till produktion i Power BI. Mer information finns i [hantera app mallversion](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).


## <a name="next-steps"></a>Nästa steg

Innan du skapar erbjudandet Power BI-appen och publicera den på AppSource, måste du uppfylla de [krav](./cpp-prerequisites.md) för att publicera en Power BI-App på AppSource.
