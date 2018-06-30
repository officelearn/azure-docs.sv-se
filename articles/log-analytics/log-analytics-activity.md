---
title: Samla in och analysera Azure aktivitetsloggar i Log Analytics | Microsoft Docs
description: Du kan använda Azure aktivitetsloggar lösningen för att analysera och söka Azure aktivitetsloggen i alla dina Azure-prenumerationer.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 0b05dc17fc7ba567bf633c25a080fbf56903935c
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130334"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics"></a>Samla in och analysera Azure aktivitetsloggar i logganalys

![Azure aktivitetsloggar symbol](./media/log-analytics-activity/activity-log-analytics.png)

Aktiviteten logganalys-lösningen hjälper dig att analysera och söka i [Azure aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) över alla dina Azure-prenumerationer. Azure-aktivitetsloggen är en logg som ger insikter om de åtgärder som utförs på resurser i dina prenumerationer. Aktivitetsloggen kallades tidigare *granskningsloggar* eller *Arbetsloggarna* eftersom den rapporterar händelser för dina prenumerationer.

Använder aktivitetsloggen, du kan fastställa den *vad*, *som*, och *när* för någon skrivåtgärder (PUT, POST, ta bort) för resurser i din prenumeration. Du kan också förstå statusen för åtgärder och andra relevanta egenskaper. Aktivitetsloggen omfattar inte läsåtgärder (GET) eller åtgärder för resurser som använder den klassiska distributionsmodellen.

När du ansluter din Azure aktivitetsloggar till logganalys kan du:

- Analysera aktivitetsloggar med fördefinierade vyer
- Analysera och Sök- och loggar från Azure-prenumerationer
- Behåll aktivitetsloggar mer än 90 dagar<sup>1</sup>
- Korrelera aktivitetsloggar med andra Azure-plattformen och programdata
- Se driften aggregeras efter status
- Visa trender för aktiviteter som händer på varje Azure-tjänster
- Rapport om auktorisering ändringar på alla dina Azure-resurser
- Identifiera eller problem påverkar dina resurser
- Använda Log-sökning för att korrelera användaraktiviteter, Autoskala operations, auktorisering ändringar och tjänstens hälsa för andra loggar eller ett mått från din miljö

<sup>1</sup>som standard logganalys sparas din Azure aktivitetsloggar i 90 dagar, även om du är på den kostnadsfria nivån. Eller, om du har en inställning för kvarhållning av arbetsytan mindre än 90 dagar. Om ditt arbetsområde har kvarhållning är längre än 90 dagar, behålls aktivitetsloggarna baserat på kvarhållningsperioden för din arbetsyta.

Logganalys samlar in kostnadsfritt aktivitetsloggar och lagrar loggarna för 90 dagar utan kostnad. Om du sparar loggar under längre tid än 90 dagar, kommer du betalar avgifter för kvarhållning av data för de data som har lagrats längre än 90 dagar.

När du är på den kostnadsfria prisnivån gäller aktivitetsloggar inte för dagliga data användning.

## <a name="connected-sources"></a>Anslutna källor

Till skillnad från de flesta andra logganalys-lösningar kan inte samlas in för aktivitetsloggar av agenter. Alla data som används av lösningen kommer direkt från Azure.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Windows-agenter](log-analytics-windows-agent.md) | Nej | Lösningen samlar inte in information från Windows-agenter. |
| [Linux-agenter](log-analytics-linux-agents.md) | Nej | Lösningen samlar inte in information från Linux-agenter. |
| [SCOM-hanteringsgrupp](log-analytics-om-agents.md) | Nej | Lösningen samlar inte in information från agenter i en ansluten SCOM-hanteringsgrupp. |
| [Azure Storage-konto](log-analytics-azure-storage.md) | Nej | Lösningen samlar inte in information från Azure storage. |

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en Azure-prenumeration för att komma åt Azure aktivitet logginformation.

## <a name="configuration"></a>Konfiguration

Utför följande steg för att konfigurera aktiviteten logganalys-lösningen för dina arbetsytor.

1. Aktivera Aktivitetslogganalys från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) eller använd processen som beskrivs i [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md).
2. Konfigurera aktivitetsloggar att gå till logganalys-arbetsytan.
    1. Välj din arbetsyta i Azure-portalen och klicka sedan på **Azure-aktivitetsloggen**.
    2. Klicka på prenumerationsnamn för varje prenumeration.  
        ![Lägg till prenumeration](./media/log-analytics-activity/add-subscription.png)
    3. I den *SubscriptionName* bladet, klickar du på **Anslut**.  
        ![Ansluta prenumeration](./media/log-analytics-activity/subscription-connect.png)

Om du lägger till lösningen med OMS-portalen, visas följande sida vid sida. Logga in på Azure-portalen att ansluta en Azure-prenumeration till arbetsytan.  
![utvärdering](./media/log-analytics-activity/tile-performing-assessment.png)

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till aktiviteten logganalys-lösningen till din arbetsyta i **Azure aktivitetsloggar** panel har lagts till översikt över instrumentpanelen. Den här panelen visar antalet Azure aktivitetsposter för Azure-prenumerationer som lösningen har åtkomst till.

![Azure aktivitetsloggar sida vid sida](./media/log-analytics-activity/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Visa Azure aktivitet loggar

Klicka på den **Azure aktivitetsloggar** öppna den **Azure aktivitetsloggar** instrumentpanelen. Instrumentpanelen innehåller bladen i följande tabell. Varje blad visar en lista med upp till tio objekt som matchar bladets kriterier för angivet omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster genom att klicka på **Se alla** längst ned på bladet eller genom att klicka på bladrubriken.

Loggdata för aktivitet visas bara *när* du har konfigurerat din aktivitetsloggar att gå till lösning, så att du kan visa data innan.

| Blad | Beskrivning |
| --- | --- |
| Azure aktivitet loggposter | Visar ett liggande diagram över de Azure aktivitetsloggpost poster summor för intervallet som du har valt och visar en lista över de översta 10 aktivitet anropare. Klicka på diagrammet om du vill köra en logg sökning efter <code>AzureActivity</code>. Klicka på en anroparen objekt om du vill köra en logg sökning returnerar alla aktiviteten loggposter för objektet. |
| Aktivitetsloggar efter Status | Visar ett ringdiagram för Azure log aktivitetsstatus för intervallet som du har valt. Visar en lista också en lista över de översta tio status-posterna. Klicka på diagrammet om du vill köra en logg sökning efter <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Klicka på en statusobjekt om du vill köra en logg sökning returnerar alla aktiviteten loggposter för posten status. |
| Aktivitetsloggar per resurs | Visar det totala antalet resurser med aktivitetsloggar och visar upp tio resurser med posten antalet för varje resurs. Klicka på området totala om du vill köra en logg sökning efter <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, vilket visar alla Azure-resurser tillgängliga för lösningen. Klicka på en resurs för att köra en logg sökning returnera alla aktivitetsposter för den här resursen. |
| Aktivitetsloggar av resursen. | Visar det totala antalet resursproviders som producerar aktivitet loggar och visar en lista över tio främsta. Klicka på området totala om du vill köra en logg sökning efter <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, vilket visar alla Azure-resurs-leverantörer. Klicka på en resursleverantör om du vill köra en logg sökning returnerar alla aktivitetsposter för providern. |

![Azure aktivitetsloggar instrumentpanelen](./media/log-analytics-activity/activity-log-dash.png)

## <a name="next-steps"></a>Nästa steg

- Skapa en [avisering](log-analytics-alerts-creating.md) när en viss aktivitet inträffar.
- Använd [loggen Sök](log-analytics-log-searches.md) att visa detaljerad information från din aktivitetsloggar.
