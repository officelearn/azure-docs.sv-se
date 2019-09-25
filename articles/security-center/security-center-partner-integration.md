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
ms.openlocfilehash: ce8aa0229d9a9b873a489209065ada588adbffff
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257747"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrera säkerhetslösningar i Azure Security Center
Det här dokumentet hjälper dig att hantera säkerhetslösningar som redan är anslutna till Azure Security Center och lägga till nya.

> [!NOTE]
> En delmängd säkerhets lösningar har dragits tillbaka den 31 juli 2019. Mer information och alternativa tjänster finns i [dra tillbaka Security Center funktioner (2019 juli)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrerade Azure-säkerhetslösningar
Med Security Center är det enkelt att aktivera integrerade säkerhetslösningar i Azure. Fördelarna innefattar:

- **Förenklad distribution**: Security Center erbjuder strömlinjeformad etablering av integrerade partner lösningar. Security Center kan etablera den nödvändiga agenten på dina virtuella datorer för lösningar som program mot skadlig kod och utvärdering av säkerhetsrisker. Security Center kan även hantera många nätverkskonfigurationer som krävs för brandväggsinstallationer.
- **Integrerade identifieringar**: Säkerhetshändelser från partnerlösningar samlas in, aggregeras och visas automatiskt som en del av aviseringarna och incidenterna i Security Center. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Enhetlig hälso övervakning och hantering**: Kunder kan använda integrerade hälsohändelser för att få en snabbövervakning av alla partnerlösningar. Grundläggande hantering finns tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.

För närvarande omfattar integrerade säkerhetslösningar sårbarhets bedömning av [Qualys](https://www.qualys.com/public-cloud/#azure) och [Rapid7](https://www.rapid7.com/products/insightvm/) och Microsoft Application Gateway brand vägg för webbaserade program.

> [!NOTE]
> Security Center installerar inte Microsoft Monitoring Agent på virtuella partnerenheter eftersom de flesta säkerhetsleverantörer inte tillåter att externa agenter körs på deras enhet.
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

Under **Säkerhetslösningar** kan du se information om hälsostatus för integrerade Azure-säkerhetslösningar och utföra grundläggande hanteringsåtgärder.

### <a name="connected-solutions"></a>Anslutna lösningar

Avsnittet **Anslutna lösningar** innehåller säkerhetslösningar som för närvarande är anslutna till Security Center samt information om hälsostatusen för varje lösning.  

![Anslutna lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Status för en partner lösning kan vara:

* Felfri (grön) – det finns inget hälso problem.
* Inte väl skyddad (röd): Det finns säkerhetsproblem som måste åtgärdas omedelbart.
* Hälso problem (orange) – lösningen har slutat rapportera dess hälsa.
* Inte rapporterat (grått) – lösningen har inte rapporterat något än, en lösnings status kan vara avrapporterad om den nyligen har varit ansluten och fortfarande distribueras eller inga hälso data är tillgängliga.

> [!NOTE]
> Om hälso status data inte är tillgängliga visar Security Center datum och tid för den senaste händelsen som togs emot för att indikera om lösningen är rapporterad eller inte. Om inga hälso data är tillgängliga och inga aviseringar tas emot inom de senaste 14 dagarna, visar Security Center att lösningen inte är felfri eller rapporterar.
>
>

1. Välj **Visa** för ytterligare information och alternativ, vilket innefattar:

   - **Lösnings konsol**. Öppnar hanterings upplevelsen för den här lösningen.
   - **Länka virtuell dator**. Öppnar bladet länka program. Här kan du ansluta resurser till partnerlösningen.
   - **Ta bort lösning**.
   - **Konfigurera**.

   ![Partnerlösningsinformation](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Upptäckta lösningar

Security Center identifierar automatiskt säkerhetslösningar som körs i Azure men som inte är anslutna till Security Center. Lösningarna visas i avsnittet **Identifierade lösningar**. Detta omfattar Azure-lösningar som [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) samt partnerlösningar.

> [!NOTE]
> Standardnivån av Security Center krävs på prenumerationsnivån för funktionen identifierade lösningar. Mer information om prisalternativen för Security finns i [Priser](security-center-pricing.md).
>
>

Välj **ANSLUT** under en lösning om du vill integrera den med Security Center och få säkerhetsaviseringar.

![Upptäckta lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

### <a name="add-data-sources"></a>Lägg till datakällor

Avsnittet **Lägg till datakällor** innehåller andra tillgängliga datakällor som kan anslutas. Om du vill få anvisningar om att lägga till data från någon av dessa källor klickar du på **LÄGG TILL**.

![Datakällor](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exportera data till en SIEM

Bearbetade händelser som genereras av Azure Security Center publiceras i Azure [aktivitets loggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), en av de logg typer som är tillgängliga via Azure Monitor. Azure Monitor erbjuder en konsol IDE rad pipeline för att vidarebefordra dina övervaknings data till ett SIEM-verktyg. Detta görs genom att strömma data till en Händelsehubben där den sedan kan hämtas till ett partner verktyg.

Denna pipe använder [Azure Monitoring Single pipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) för att få åtkomst till övervaknings data från din Azure-miljö. På så sätt kan du enkelt konfigurera Siem och övervaknings verktyg för att använda data.

I nästa avsnitt beskrivs hur du kan konfigurera data så att de strömmas till en händelsehubben. Stegen förutsätter att du redan har Azure Security Center konfigurerat i din Azure-prenumeration.

Översikt på hög nivå

![Översikt på hög nivå](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Vad är Azures säkerhets data exponerade för SIEM?

I den här versionen exponerar vi [säkerhets aviseringarna.](../security-center/security-center-managing-and-responding-alerts.md) I kommande versioner kommer vi att utöka data uppsättningen med säkerhets rekommendationer.

### <a name="how-to-setup-the-pipeline"></a>Konfigurera pipelinen

#### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Innan du börjar måste du [skapa ett Event Hubs-namnområde](../event-hubs/event-hubs-create.md). Det här namn området och Händelsehubben är målet för alla dina övervaknings data.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Strömma Azure-aktivitets loggen till Event Hubs

Se följande artikel [Ströms aktivitets logg för att Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Installera en partner SIEM-anslutning 

Genom att vidarebefordra dina övervaknings data till en Event Hub med Azure Monitor kan du enkelt integrera med partner SIEM och övervaknings verktyg.

Se följande länk om du vill se en lista över [Siem som stöds](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>Exempel för att fråga efter data 

Här är några Splunk-frågor som du kan använda för att hämta aviserings data:

| **Beskrivning av fråga** | **Fråga** |
|----|----|
| Alla aviseringar| index = main Microsoft. Security/locations/Alerts|
| Sammanfatta antalet åtgärder med deras namn| index = main SourceType = "Amal: säkerhet" \| Table operationName \| stats antal per operationName|
| Hämta aviserings information: Tid, namn, tillstånd, ID och prenumeration | index = main Microsoft. Security/locations/ \| Alerts Table \_Time, Properties. EventName, State, Properties. operationId, am_subscriptionId |


## <a name="next-steps"></a>Nästa steg

Den här artikeln berättade om hur man integrerar partnerlösningar i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om användningen av Security Center.
* [Azure säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
