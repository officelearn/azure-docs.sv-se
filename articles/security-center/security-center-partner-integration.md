---
title: Integrera säkerhetslösningar i Azure Security Center | Microsoft Docs
description: Läs hur Azure Security Center integrerar med partners för att förbättra den övergripande säkerheten för dina Azure-resurser.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: f7a1eccd76313c5b3bc74a5b5ebdbcd202ca6841
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435757"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrera säkerhetslösningar i Azure Security Center
Det här dokumentet hjälper dig att hantera säkerhetslösningar som redan är anslutna till Azure Security Center och lägga till nya.

> [!NOTE]
> En delmängd av säkerhetslösningar har dragits tillbaka den 31 juli 2019. Mer information och alternativa tjänster finns i [Funktioner för Pensionering av Säkerhetscenter (juli 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrerade Azure-säkerhetslösningar
Med Security Center är det enkelt att aktivera integrerade säkerhetslösningar i Azure. Fördelarna innefattar:

- **Förenklad distribution**: Security Center erbjuder effektiviserad etablering av integrerade partnerlösningar. För lösningar som antimalware och sårbarhetsbedömning kan Security Center etablera agenten på dina virtuella datorer. För brandväggsenheter kan Security Center ta hand om en stor del av den nätverkskonfiguration som krävs.
- **Integrerade identifieringar**: Säkerhetshändelser från partnerlösningar samlas in automatiskt, aggregeras och visas som en del av Säkerhetscenter-aviseringar och incidenter. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Enhetlig övervakning och hantering av hälsa**: Kunderna kan använda integrerade hälsohändelser för att snabbt övervaka alla partnerlösningar. Grundläggande hantering finns tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.

För närvarande inkluderar integrerade säkerhetslösningar sårbarhetsbedömning av [Qualys](https://www.qualys.com/public-cloud/#azure) och [Rapid7](https://www.rapid7.com/products/insightvm/) och Microsoft Application Gateway Web Application Firewall.

> [!NOTE]
> Security Center installerar inte Log Analytics-agenten på partnervirvliga enheter eftersom de flesta säkerhetsleverantörer förbjuder externa agenter som körs på sina enheter.
>
>

## <a name="how-security-solutions-are-integrated"></a>Så här integreras säkerhetslösningar
Azure-säkerhetslösningar som distribueras från Security Center ansluts automatiskt. Du kan också ansluta andra säkerhetsdatakällor, inklusive datorer som körs lokalt eller i andra moln.

![Integrerings av partnerlösningar](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Hantera integrerade Azure-säkerhetslösningar och andra datakällor

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).

2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center – Översikt** öppnas.

3. På menyn i Security Center väljer du **Säkerhetslösningar**.

   ![Security Center – Översikt](./media/security-center-partner-integration/overview.png)

I **Säkerhetslösningar**kan du se hälsotillståndet för integrerade Azure-säkerhetslösningar och köra grundläggande hanteringsuppgifter.

### <a name="connected-solutions"></a>Anslutna lösningar

Avsnittet **Anslutna lösningar** innehåller säkerhetslösningar som för närvarande är anslutna till Security Center. Det visar också hälsostatus för varje lösning.  

![Anslutna lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Status för en partnerlösning kan vara:

* Friska (gröna) - inga hälsofrågor.
* Ohälsosamma (röda) - det finns en hälsofråga som kräver omedelbar uppmärksamhet.
* Hälsofrågor (orange) - lösningen har slutat rapportera sin hälsa.
* Inte rapporterats (grå) - lösningen har inte rapporterat något ännu och inga hälsodata är tillgängliga. Statusen för en lösning kan inte rapporteras om den har anslutits nyligen och fortfarande distribueras.

> [!NOTE]
> Om hälsostatusdata inte är tillgängliga visar Security Center datum och tid för den senaste händelsen som togs emot för att ange om lösningen rapporterar eller inte. Om inga hälsodata är tillgängliga och inga aviseringar har tagits emot inom de senaste 14 dagarna anger Security Center att lösningen är fel eller inte rapporterar.
>
>

1. Välj **VISA** för ytterligare information och alternativ, till exempel:

   - **Lösningskonsol**. Öppnar hanteringsupplevelsen för den här lösningen.
   - **Länka VM**. Öppnar sidan Länkprogram. Här kan du ansluta resurser till partnerlösningen.
   - **Ta bort lösning**.
   - **Konfigurera**.

   ![Information om partnerlösning](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Identifierade lösningar

Security Center identifierar automatiskt säkerhetslösningar som körs i Azure men inte är anslutna till Security Center och visar lösningarna i avsnittet **Identifierade lösningar.** Dessa lösningar omfattar Azure-lösningar, till exempel [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)och partnerlösningar.

> [!NOTE]
> Standardnivån av Security Center krävs på prenumerationsnivån för funktionen identifierade lösningar. Se [Priser](security-center-pricing.md) om du vill veta mer om prisnivåerna.
>
>

Välj **CONNECT** under en lösning som kan integreras med Security Center och meddelas om säkerhetsvarningar.

### <a name="add-data-sources"></a>Lägg till datakällor

Avsnittet **Lägg till datakällor** innehåller andra tillgängliga datakällor som kan anslutas. Om du vill få anvisningar om att lägga till data från någon av dessa källor klickar du på **LÄGG TILL**.

![Datakällor](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exportera data till ett SIEM

> [!NOTE]
> Mer information om en enklare metod (för närvarande i förhandsversion) för export av data till ett SIEM finns i [Exportera säkerhetsvarningar och rekommendationer (förhandsversion).](continuous-export.md) Den nya metoden använder inte Aktivitetsloggen som intermediator och möjliggör direkt export från Security Center till Event Hubs (och sedan vidare till din SIEM), stöder den också export av säkerhetsrekommendationer.


Du kan konfigurera dina SIEMs eller andra övervakningsverktyg för att ta emot Azure Security Center-händelser.

Alla händelser från Azure Security Center publiceras i Azure Monitors Azure [Activity-logg](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Azure Monitor använder [en konsoliderad pipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) för att strömma data till en eventnav där den sedan kan hämtas in i ditt övervakningsverktyg.

I nästa avsnitt beskrivs hur du kan konfigurera data som ska strömmas till en händelsehubb. Stegen förutsätter att du redan har Azure Security Center konfigurerat i din Azure-prenumeration.

### <a name="high-level-overview"></a>Översikt på hög nivå

![Översikt på hög nivå](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Vad är Azure-säkerhetsdata som exponeras för SIEM?

I den här versionen exponerar vi [säkerhetsaviseringarna.](../security-center/security-center-managing-and-responding-alerts.md) I kommande versioner kommer vi att berika datauppsättningen med säkerhetsrekommendationer.

### <a name="how-to-set-up-the-pipeline"></a>Så här ställer du in pipelinen

#### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Innan du börjar [skapar du ett namnområde för händelsehubbar](../event-hubs/event-hubs-create.md) – målet för alla övervakningsdata.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Strömma Azure-aktivitetsloggen till eventhubbar

Se följande artikel [strömma aktivitetslogg till Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Installera en PARTNER-SIEM-kontakt 

Genom att dirigera dina övervakningsdata till en eventhubb med Azure Monitor kan du enkelt integrera med partner-SIEM- och övervakningsverktyg.

Se följande artikel för listan över [SIEMs som stöds](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

### <a name="example-for-querying-data"></a>Exempel för att fråga data 

Här är några Splunk frågor som du kan använda för att hämta varningsdata:

| **Beskrivning av fråga** | **Söka i data** |
|----|----|
| All Alerts| index=huvudsakliga Microsoft.Säkerhet/platser/aviseringar|
| Summera antalet åtgärder efter deras namn| index=huvudsakliga sourcetype="amal:security" \| \| tabelloperationName statistik antal av operationName|
| Hämta information om aviseringar: Tid, Namn, Tillstånd, ID och Prenumeration | index=huvudtabelltid för Microsoft.Security/locations/alerts, \| \_properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Nästa steg

Den här artikeln berättade om hur man integrerar partnerlösningar i Security Center. Mer information om Security Center finns i följande artikel:

* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.