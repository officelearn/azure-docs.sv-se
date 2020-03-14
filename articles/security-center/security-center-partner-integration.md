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
ms.openlocfilehash: 23a00c766dbb38853c57c91e7f59ec364390c44b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245387"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrera säkerhetslösningar i Azure Security Center
Det här dokumentet hjälper dig att hantera säkerhetslösningar som redan är anslutna till Azure Security Center och lägga till nya.

> [!NOTE]
> En delmängd säkerhets lösningar har dragits tillbaka den 31 juli 2019. Mer information och alternativa tjänster finns i [dra tillbaka Security Center funktioner (2019 juli)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrerade Azure-säkerhetslösningar
Med Security Center är det enkelt att aktivera integrerade säkerhetslösningar i Azure. Fördelarna innefattar:

- **Förenklad distribution**: Security Center erbjuder effektiviserad etablering av integrerade partnerlösningar. För lösningar som program mot skadlig kod och sårbarhets bedömning kan Security Center etablera agenten på dina virtuella datorer. För brand Väggs apparater kan Security Center ta hand om en stor del av nätverks konfigurationen som krävs.
- **Integrerade identifieringar**: Säkerhetshändelser från partnerlösningar samlas in, aggregeras och visas automatiskt som en del av aviseringarna och incidenterna i Security Center. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Enhetlig övervakning och hantering av hälsa**: Kunderna kan använda integrerade hälsohändelser för att snabbt övervaka alla partnerlösningar. Grundläggande hantering finns tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.

För närvarande omfattar integrerade säkerhetslösningar sårbarhets bedömning av [Qualys](https://www.qualys.com/public-cloud/#azure) och [Rapid7](https://www.rapid7.com/products/insightvm/) och Microsoft Application Gateway brand vägg för webbaserade program.

> [!NOTE]
> Security Center installerar inte Microsoft Monitoring Agent på virtuella partner enheter eftersom de flesta säkerhets leverantörer förhindrar att externa agenter körs på sina enheter.
>
>

## <a name="how-security-solutions-are-integrated"></a>Så här integreras säkerhetslösningar
Azure-säkerhetslösningar som distribueras från Security Center ansluts automatiskt. Du kan också ansluta andra säkerhets data källor, inklusive datorer som körs lokalt eller i andra moln.

![Integrerings av partnerlösningar](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Hantera integrerade Azure-säkerhetslösningar och andra datakällor

1. Logga in på [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center – Översikt** öppnas.

3. På menyn i Security Center väljer du **Säkerhetslösningar**.

   ![Security Center – Översikt](./media/security-center-partner-integration/overview.png)

I **säkerhetslösningar**kan du se hälsan hos integrerade Azure-säkerhetslösningar och köra grundläggande hanterings uppgifter.

### <a name="connected-solutions"></a>Anslutna lösningar

Avsnittet **anslutna lösningar** innehåller säkerhetslösningar som för närvarande är anslutna till Security Center. Den visar också hälso status för varje lösning.  

![Anslutna lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Status för en partner lösning kan vara:

* Felfritt (grönt) – inga hälso problem.
* Ej felfri (röd) – det finns ett hälso problem som kräver omedelbar uppmärksamhet.
* Hälso problem (orange) – lösningen har slutat rapportera dess hälsa.
* Inte rapporterat (grått) – lösningen har inte rapporterat något än och inga hälso data är tillgängliga. Statusen för en lösning kan vara avrapporterad om den var ansluten nyligen och fortfarande distribueras.

> [!NOTE]
> Om hälso status data inte är tillgängliga visar Security Center datum och tid för den senaste händelsen som togs emot för att indikera om lösningen är rapporterad eller inte. Om inga hälso data är tillgängliga och inga aviseringar togs emot under de senaste 14 dagarna, visar Security Center att lösningen inte är felfri eller rapporterar.
>
>

1. Välj **Visa** för ytterligare information och alternativ, till exempel:

   - **Lösnings konsol**. Öppnar hanterings upplevelsen för den här lösningen.
   - **Länka virtuell dator**. Öppnar sidan länka program. Här kan du ansluta resurser till partnerlösningen.
   - **Ta bort lösning**.
   - **Konfigurera**.

   ![Partnerlösningsinformation](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Identifierade lösningar

Security Center identifierar automatiskt säkerhetslösningar som körs i Azure men inte är anslutna till Security Center och visar lösningarna i avsnittet **identifierade lösningar** . Dessa lösningar omfattar Azure-lösningar som [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)och partner lösningar.

> [!NOTE]
> Standardnivån av Security Center krävs på prenumerationsnivån för funktionen identifierade lösningar. Se [prissättning](security-center-pricing.md) för att lära dig mer om pris nivåerna.
>
>

Välj **Anslut** under en lösning för att integrera med Security Center och bli informerad om säkerhets aviseringar.

### <a name="add-data-sources"></a>Lägg till datakällor

Avsnittet **Lägg till datakällor** innehåller andra tillgängliga datakällor som kan anslutas. Om du vill få anvisningar om att lägga till data från någon av dessa källor klickar du på **LÄGG TILL**.

![Datakällor](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exportera data till en SIEM

> [!NOTE]
> Information om en enklare metod (för närvarande i för hands version) för att exportera data till en SIEM finns i [Exportera säkerhets aviseringar och rekommendationer (för hands version)](continuous-export.md). Den nya metoden använder inte aktivitets loggen som en Intermediator och tillåter direkt export från Security Center till Event Hubs (och sedan till din SIEM) stöder även export av säkerhets rekommendationer.


Du kan konfigurera Siem eller andra övervaknings verktyg för att ta emot Azure Security Center händelser.

Alla händelser från Azure Security Center publiceras i Azure Monitor s [aktivitets logg](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)i Azure. Azure Monitor använder [en konsol IDE rad pipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) för att strömma data till en händelsehubben där den sedan kan hämtas till övervaknings verktyget.

I nästa avsnitt beskrivs hur du kan konfigurera data så att de strömmas till en händelsehubben. Stegen förutsätter att du redan har Azure Security Center konfigurerat i din Azure-prenumeration.

### <a name="high-level-overview"></a>Översikt på hög nivå

![Översikt på hög nivå](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Vad är Azures säkerhets data exponerade för SIEM?

I den här versionen exponeras [säkerhets aviseringarna.](../security-center/security-center-managing-and-responding-alerts.md) I kommande versioner kommer vi att utöka data uppsättningen med säkerhets rekommendationer.

### <a name="how-to-set-up-the-pipeline"></a>Så här konfigurerar du pipelinen

#### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Innan du börjar [skapar du en Event Hubs namnrymd](../event-hubs/event-hubs-create.md) – målet för alla dina övervaknings data.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Strömma Azure-aktivitets loggen till Event Hubs

Se följande artikel [Ströms aktivitets logg för att Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Installera en partner SIEM-anslutning 

Genom att vidarebefordra dina övervaknings data till en Event Hub med Azure Monitor kan du enkelt integrera med partner SIEM och övervaknings verktyg.

I följande artikel finns en lista över [Siem som stöds](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

### <a name="example-for-querying-data"></a>Exempel för att fråga efter data 

Här följer några Splunk-frågor som du kan använda för att hämta aviserings data:

| **Beskrivning av fråga** | **Fråga** |
|----|----|
| Alla aviseringar| index = main Microsoft. Security/locations/Alerts|
| Sammanfatta antalet åtgärder med deras namn| index = main SourceType = "Amal: säkerhet" \| tabell operationName \| statistik antal av operationName|
| Hämta aviserings information: tid, namn, tillstånd, ID och prenumeration | index = main Microsoft. Security/locations/Alerts \| tabell \_tid, egenskaper. eventName, State, Properties. operationId, am_subscriptionId |


## <a name="next-steps"></a>Nästa steg

Den här artikeln berättade om hur man integrerar partnerlösningar i Security Center. Mer information om Security Center finns i följande artikel:

* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.