---
title: Visa status för marketplace-erbjudanden | Azure Marketplace
description: Visa status för erbjudanden på Azure och AppSource marknadsplatser med partnerportalen i molnet
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: b8113552edf9dd2f886b5752b2ebc69afc0fda08
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942420"
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

Sista exempel status för ett Azure-program visar ett kritiskt problem för Microsoft-granskning.  Den innehåller en hypertextlänk till VSTS-objektet som innehåller detaljerad information om problemet granskning.  Mer information finns i [publicera Azure-erbjudande för programmet](cpp-publish-offer.md).

![Fliken status för Azure-app som visar granska problemet](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Nästa steg

Om du vill korrigera återstående frågor eller uppdatera inställningarna för erbjudandet, måste du [uppdatera ett erbjudande](./cpp-update-offer.md). 
