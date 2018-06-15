---
title: ta med fil
description: ta med fil
services: cost-management
author: bandersmsft
ms.service: cost-management
ms.topic: include
ms.date: 04/26/2018
ms.author: banders
manager: dougeby
ms.custom: include file
ms.openlocfilehash: 1b65775ef5ad40ca9e9c1e2c96fe1c2b8d92afdc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32198863"
---
## <a name="view-cost-data"></a>Visa kostnadsdata

Azure Cost Management från Cloudyn ger dig åtkomst till alla dina molnresursdata. I instrumentpanelsrapporterna hittar du både standardrapporter och anpassade rapporter i en flikvy. Följande är exempel på en populär instrumentpanel och en rapport som visar dig kostnadsdata direkt.

![Hanteringspanelen](./media/cost-management-create-account-view-data/mgt-dash.png)

I det här exemplet visar hanteringspanelen konsoliderade kostnader för företaget Contoso i alla molnresurser. Contoso använder Azure, AWS och Google. Instrumentpaneler ger översiktlig information och är snabba sätt att navigera till rapporter.  

Om du är osäker på rapportens syfte på en instrumentpanel hovrar du över **i**-symbolen så visas en förklaring. Klicka på valfri rapport på en instrumentpanel för att visa hela rapporten.

Du kan även visa rapporter i rapportmenyn högst upp i portalen. Vi tar en titt på utgifterna för Contosos Azure-resurs under de senaste 30 dagarna. Klicka på **Cost** (Kostnad) > **Cost Analysis** (Kostnadsanalys) > **Actual Cost Analysis** (Analys av faktiska kostnader). Rensa eventuella värden för taggar, grupper eller filter i rapporten.

![Analys av faktiska kostnader](./media/cost-management-create-account-view-data/actual-cost-01.png)

I det här exemplet är 75 970 USD totalkostnaden och budgeten är 130 000 USD.

Nu ändrar vi rapportformatet och anger grupper och filter för att begränsa resultaten för Azure-kostnader. Ställ in **Date Range** (Datumintervall) på de senaste 30 dagarna. Klicka högst upp till höger på kolumnsymbolen för att formatera som ett stapeldiagram och välj **Provider** under Groups (Grupper). Ställ sedan in ett filter för **Provider** på **Azure**.

![Analys av faktiska kostnader filtrerat](./media/cost-management-create-account-view-data/actual-cost-02.png)

I det här exemplet var totalkostnaden för Azure-resurser 3 839 USD under de senaste 30 dagarna.

Högerklicka på fältet Provider (Azure) och öka detaljnivån till **Resource types** (Resurstyper).

![öka detaljnivå](./media/cost-management-create-account-view-data/actual-cost-03.png)

Följande bild visar kostnaderna för Azure-resurser som Contoso har ådragit sig. Totalsumman var 3 839 USD. I det här exemplet var ungefär hälften av kostnaderna för lokalt redundant lagring och ungefär den andra hälften av kostnaderna var för olika VM-instanser.

![resurstyper](./media/cost-management-create-account-view-data/actual-cost-04.png)

Högerklicka på en resurstyp och välj **Cost Entities** (Kostnadsenheter) för att visa kostnadsenheter och de tjänster som har förbrukat resursen. Tjänsterna VM och Workers i DevOps har förbrukat 486,60 USD och 435,71 i det här exemplet. Totalsumman för båda är 922 USD.

![kostnadsenheter och tjänster](./media/cost-management-create-account-view-data/actual-cost-05.png)

Om du vill se en självstudievideo om att visa dina molnfaktureringsdata kan du se [Analyzing your cloud billing data with Azure Cost Management by Cloudyn](https://youtu.be/G0pvI3iLH-Y) (Analysera molnfaktureringsdata med Azure Cost Management från Cloudyn).
