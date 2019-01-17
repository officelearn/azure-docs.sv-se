---
title: Visa status för Marketplace - erbjudanden, Azure Marketplace | Microsoft Docs
description: Visa status för erbjudanden på Azure och AppSource marknadsplatser med partnerportalen i molnet
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
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
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: d7c2909e59643378e765fa51e2d261cbdc106822
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355755"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Visa publiceringsstatus för Azure Marketplace och AppSource erbjudanden

När du skapar ett erbjudande och särskilt under publiceringsprocessen ska du visa status för ditt erbjudande i partnerportalen i molnet.  Övergripande Publiceringsstatus är tillgänglig i den [ **alla erbjuder** ](../portal-tour/cpp-all-offers-page.md) och [ **godkännanden** ](../portal-tour/cpp-approvals-page.md) sidor i portalen.  En av följande status indikatorerna ska visas för varje erbjudande.  

|            Status              |   Beskrivning                                                           |
|            ------              |   -----------                                                           |
| **-**                          | Erbjudandet har skapats men publiceringsprocessen har inte startat.            |
| **Publicering pågår**        | Erbjudandet fungerar väg genom stegen för publiceringsprocessen.   |
| **Publiceringen misslyckades**             | Ett kritiskt problem har identifierats vid validering eller granskning av Microsoft. |
| **Publicera avbrutna**           | Utgivaren har avbrutit erbjudandet publiceringsprocessen.  Det här tillståndet inte avlistning ett befintligt erbjudande i marketplace. | 
| **Väntar på utgivaren logga ut** | Erbjudandet har granskat av Microsoft och nu väntar på dig en slutlig verifiering av utgivaren. |
| **Delisted**                   | Ett tidigare publicerade erbjudande i marketplace har tagits bort.      | 
|  |  |


## <a name="publishing-status-details"></a>Publicera statusinformation 

Mer information om status på ett erbjudande som genomgår publiceringsprocessen finns i den **Status** fliken den **nytt erbjudande** sidan.  Den här sidan visas alla steg som publicering för den typ av erbjudande.  *Observera att antalet och detaljerade anvisningar om hur ofta skiljer sig åt mellan typer av erbjudanden.*  Den här sidan anges också eventuella utestående problem som skapats av Microsoft-validering och granska steg, som ofta kräver en åtgärd av utgivaren innan publiceringsprocessen kan fortsätta.  Exempelvis följande bild visar den **Status** fliken till en ny virtuell dator. 

![Fliken status för erbjudande för virtuell dator](./media/vm-offer-pub-steps1.png)

I nästa exempel **Status** fliken för en konsult tjänst, som visar ett rapporterat fel i inställningarna för lead-hantering.  Eftersom lead-hantering krävs för konsulttjänster, måste det här felet åtgärdas innan publicering kan fortsätta.

![Fliken status för consulting som visar fel i tjänsten](./media/consulting-service-error.png)

Sista exempel status för ett Azure-program visar ett kritiskt problem för Microsoft-granskning.  Den innehåller en hypertextlänk till VSTS-objektet som innehåller detaljerad information om problemet granskning.  Mer information finns i [publicera Azure-erbjudande för programmet]().

![Fliken status för Azure-app som visar granska problemet](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Nästa steg

Om du vill korrigera återstående frågor eller uppdatera inställningarna för erbjudandet, måste du [uppdatera ett erbjudande](./cpp-update-offer.md). 
