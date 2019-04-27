---
title: Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsyta | Microsoft Docs
description: Du kan använda Azure-aktivitetsloggar lösningen för att analysera och söka i Azure-aktivitetsloggen i alla dina Azure-prenumerationer.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: magoedte
ms.openlocfilehash: 4476bb0a5a343fd43ce5ed70cf0e493d0ccae0e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741301"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsyta i Azure Monitor

![Symbolen för Azure-aktivitetsloggar](./media/collect-activity-logs/activity-log-analytics.png)

Activity Log Analytics-lösningen hjälper dig att analysera och söka i [Azure-aktivitetsloggen](activity-logs-overview.md) över alla dina Azure-prenumerationer. Azure-aktivitetsloggen är en logg som ger insikt i de åtgärder som utförts på resurser i dina prenumerationer. Aktivitetsloggen kallades tidigare *granskningsloggar* eller *Driftloggar* eftersom den rapporterar händelser för dina prenumerationer.

Med aktivitetsloggen kan du fastställa den *vad*, *som*, och *när* för alla skrivåtgärder (PUT, POST, ta bort) för resurser i din prenumeration. Du kan också förstå statusen för åtgärder och andra relevanta egenskaper. Aktivitetsloggen inkluderar inte läsåtgärder (GET) eller åtgärder för resurser som använder den klassiska distributionsmodellen.

När du ansluter din Azure-aktivitetsloggar till Log Analytics-arbetsytan kan du:

- Analysera aktivitetsloggar med fördefinierade vyer
- Analysera och Sök aktivitetsloggar från flera Azure-prenumerationer
- Behåll aktivitetsloggar längre än 90 dagar<sup>1</sup>
- Korrelera aktivitetsloggar med andra Azure-plattformen och programdata
- Se driftaktiviteter aggregeras efter status
- Visa trender för aktiviteter som sker på var och en av dina Azure-tjänster
- Rapport om auktorisering ändringar på alla dina Azure-resurser
- Identifiera eller hälsoproblem som påverkar dina resurser
- Använd Log Search för att jämföra användaraktiviteter, automatisk skalning åtgärder, auktorisering ändringar och tjänstehälsa till andra loggar eller mätvärden från miljön

<sup>1</sup>som standard Azure Monitor håller din Azure-aktivitetsloggar i Log Analytics-arbetsytan i 90 dagar, även om du är på den kostnadsfria nivån. Eller, om du har en inställning för kvarhållning av arbetsyta mindre än 90 dagar. Om din arbetsyta har kvarhållning är längre än 90 dagar, behålls aktivitetsloggarna baserat på kvarhållningsperioden för din arbetsyta.

Log Analytics-arbetsytan samlar in aktivitetsloggar som är kostnadsfritt och lagrar loggarna i 90 dagar utan kostnad. Om du sparar loggar längre än 90 dagar, debiteras du datakvarhållning för data som lagras längre än 90 dagar.

När du är på den kostnadsfria prisnivån gäller inte aktivitetsloggar till din dagliga dataförbrukning.

## <a name="connected-sources"></a>Anslutna källor

Till skillnad från de flesta Azure Monitor-lösningar data samlas inte in aktivitetsloggar av agenter. Alla data som används av lösningen kommer direkt från Azure.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| [Windows-agenter](agent-windows.md) | Nej | Lösningen samlar inte in information från Windows-agenter. |
| [Linux-agenter](../learn/quick-collect-linux-computer.md) | Nej | Lösningen samlar inte in information från Linux-agenter. |
| [System Center Operations Manager-hanteringsgrupp](om-agents.md) | Nej | Lösningen samlar inte in information från agenter som rapporterar till en Operations Manager-hanteringsgrupp. |
| [Azure Storage-konto](collect-azure-metrics-logs.md) | Nej | Lösningen samlar inte in information från Azure storage. |

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en Azure-prenumeration för att komma åt information från Azure-aktivitetsloggen.

Lösningen kräver också att följande två resursprovidrar registreras i din prenumeration:

1. Microsoft.OperationalInsights
2. Microsoft.OperationsManagement

Läs hur du registrerar eller kontrollera att de är registrerade i [Azure resursproviders och resurstyper](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="configuration"></a>Konfiguration

Utför följande steg för att konfigurera Activity Log Analytics-lösningen för dina arbetsytor.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

2. Aktivera Aktivitetslogganalys från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) eller använd processen som beskrivs i [Lägg till Log Analytics-lösningar från lösningsgalleriet](../insights/solutions.md).

2. Konfigurera aktivitetsloggar för att gå till Log Analytics-arbetsytan.
    1. Välj din arbetsyta i Azure-portalen och klicka sedan på **Azure-aktivitetsloggen**.
    2. Klicka på prenumerationens namn för varje prenumeration.  
        
        ![Lägg till prenumeration](./media/collect-activity-logs/add-subscription.png)
    
    3. I den *SubscriptionName* bladet klickar du på **Connect**.  
    
        ![Anslut prenumeration](./media/collect-activity-logs/subscription-connect.png)

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till Activity Log Analytics-lösningen till din arbetsyta i **Azure-aktivitetsloggar** panel har lagts till din instrumentpanel. Den här panelen visar antal Azure aktivitetsposter för Azure-prenumerationer som lösningen har åtkomst till.

![Azure-aktivitetsloggar panel](./media/collect-activity-logs/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Visa aktivitetsloggar för Azure

Klicka på den **Azure-aktivitetsloggar** panelen för att öppna den **Azure-aktivitetsloggar** instrumentpanelen. Instrumentpanelen innehåller bladen i följande tabell. Varje blad visar en lista med upp till tio objekt som matchar bladets kriterier för angivet omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster genom att klicka på **Se alla** längst ned på bladet eller genom att klicka på bladrubriken.

Aktivitetsloggdata visas bara *när* du har konfigurerat din aktivitetsloggar för att öppna lösningen, så du inte kan visa data innan dess.

| Bladet | Beskrivning |
| --- | --- |
| Azure-Aktivitetsloggposter | Visar ett liggande diagram över de Azure-aktivitetsloggar loggpost poster summor för datumintervall som du har valt och visar en lista över de översta 10 aktivitet anropare. Klicka på diagrammet för att köra en loggsökning för <code>AzureActivity</code>. Klicka på en anroparen objekt om du vill köra en loggsökning som returnerar alla aktivitetsloggposter för objektet. |
| Aktivitetsloggar efter Status | Visar ett ringdiagram för Azure log aktivitetsstatus för datumintervall som du har valt. Visar en lista också en lista över de översta tio status-posterna. Klicka på diagrammet för att köra en loggsökning för <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Klicka på en statusobjekt om du vill köra en loggsökning som returnerar alla aktivitetsloggposter för status för posten. |
| Aktivitetsloggar efter resurs | Visar det totala antalet resurser med aktivitetsloggar och visar en lista över de översta tio resurser med post antalet för varje resurs. Klicka på den totala område för att köra en loggsökning för <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, som visar alla Azure-resurser tillgängliga för lösningen. Klicka på en resurs för att köra en loggsökning som returnerar alla aktivitetsposter för den resursen. |
| Aktivitetsloggar av Resursprovidern | Visar det totala antalet resursprovidrar som producerar aktivitet loggar och visar en lista över tio främsta. Klicka på den totala område för att köra en loggsökning för <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, som visar alla leverantörer av Azure-resurs. Klicka på en resursprovider för att köra en loggsökning som returnerar alla aktivitetsposter för providern. |

![Instrumentpanelen för Azure-aktivitetsloggar](./media/collect-activity-logs/activity-log-dash.png)

## <a name="next-steps"></a>Nästa steg

- Skapa en [avisering](../platform/alerts-metric.md) när en specifik aktivitet inträffar.
- Använd [Loggsökning](../log-query/log-query-overview.md) att visa detaljerad information från din aktivitetsloggar.
