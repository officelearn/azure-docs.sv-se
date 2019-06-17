---
title: Integrera säkerhetslösningar i Azure Security Center | Microsoft Docs
description: Läs hur Azure Security Center integrerar med partners för att förbättra den övergripande säkerheten för dina Azure-resurser.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: d94567800a9fd020784c9cb07b2c6824cd032509
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064278"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrera säkerhetslösningar i Azure Security Center
Det här dokumentet hjälper dig att hantera säkerhetslösningar som redan är anslutna till Azure Security Center och lägga till nya.

> [!NOTE]
> En delmängd av säkerhetslösningar tas ur bruk 31 juli 2019. Mer information och alternativa tjänster finns i [tillbakadragning av Security Center-funktioner (juli 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrerade Azure-säkerhetslösningar
Med Security Center är det enkelt att aktivera integrerade säkerhetslösningar i Azure. Fördelarna innefattar:

- **Förenklad distribution**: Security Center erbjuder effektiviserad etablering av integrerade partnerlösningar. Security Center kan etablera den nödvändiga agenten på dina virtuella datorer för lösningar som program mot skadlig kod och utvärdering av säkerhetsrisker. Security Center kan även hantera många nätverkskonfigurationer som krävs för brandväggsinstallationer.
- **Integrerade identifieringar**: Säkerhetshändelser från partnerlösningar samlas in, aggregeras och visas automatiskt som en del av aviseringarna och incidenterna i Security Center. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Enhetlig hälsoövervakning och hantering**: Kunder kan använda integrerade hälsohändelser för att få en snabbövervakning av alla partnerlösningar. Grundläggande hantering finns tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.

För närvarande integrerade säkerhetslösningar omfattar sårbarhetsbedömning av [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) och [Rapid7](https://www.rapid7.com/products/insightvm/) och Microsoft Application Gateway waf.

> [!NOTE]
> Security Center installerar inte Microsoft Monitoring Agent på virtuella partnerenheter eftersom de flesta säkerhetsleverantörer inte tillåter att externa agenter körs på deras enhet.
>
>

## <a name="how-security-solutions-are-integrated"></a>Så här integreras säkerhetslösningar
Azure-säkerhetslösningar som distribueras från Security Center ansluts automatiskt. Du kan också ansluta andra datakällor för säkerhet, inklusive datorer som körs lokalt eller i andra moln.

![Integrerings av partnerlösningar](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Hantera integrerade Azure-säkerhetslösningar och andra datakällor

1. Logga in på [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center – Översikt** öppnas.

3. På menyn i Security Center väljer du **Säkerhetslösningar**.

   ![Security Center – Översikt](./media/security-center-partner-integration/overview.png)

Under **Säkerhetslösningar** kan du se information om hälsostatus för integrerade Azure-säkerhetslösningar och utföra grundläggande hanteringsåtgärder. Du kan även ansluta andra typer av datakällor för säkerhet, t.ex. Azure Active Directory Identity Protection-aviseringar och brandväggsloggar i Common Event Format (CEF).

### <a name="connected-solutions"></a>Anslutna lösningar

Avsnittet **Anslutna lösningar** innehåller säkerhetslösningar som för närvarande är anslutna till Security Center samt information om hälsostatusen för varje lösning.  

![Anslutna lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Status för en partnerlösning kan vara:

* Felfritt (grönt) - det finns inga problem.
* Inte väl skyddad (röd): Det finns säkerhetsproblem som måste åtgärdas omedelbart.
* Problem med hälsotillstånd (orange) - lösningen har slutat rapportera dess hälsa.
* Inga rapporter (grå) - lösningen har inte rapporterat något ännu, status för en lösning kan vara orapporterad om den nyligen har anslutits och fortfarande pågår eller inga health-data är tillgängliga.

> [!NOTE]
> Om hälsodata status inte är tillgänglig visar Security Center datum och tid för senaste händelsen togs emot för att indikera om lösningen rapporterar eller inte. Om inga health-data är tillgängliga och inga aviseringar tas emot inom de senaste 14 dagarna, anger Security Center att lösningen är defekt eller inte reporting.
>
>

1. Välj **visa** för ytterligare information och alternativ, vilket inkluderar:

   - **Lösningskonsol**. Öppnar hanteringsupplevelse för den här lösningen.
   - **Länka VM**. Öppnas bladet länka program. Här kan du ansluta resurser till partnerlösningen.
   - **Ta bort lösningen**.
   - **Konfigurera**.

   ![Partnerlösningsinformation](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Identifierade lösningar

Security Center identifierar automatiskt säkerhetslösningar som körs i Azure men som inte är anslutna till Security Center. Lösningarna visas i avsnittet **Identifierade lösningar**. Detta omfattar Azure-lösningar som [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) samt partnerlösningar.

> [!NOTE]
> Standardnivån av Security Center krävs på prenumerationsnivån för funktionen identifierade lösningar. Mer information om prisalternativen för Security finns i [Priser](security-center-pricing.md).
>
>

Välj **ANSLUT** under en lösning om du vill integrera den med Security Center och få säkerhetsaviseringar.

![Identifierade lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center identifierar också lösningar som distribueras i prenumerationen och som kan vidarebefordra CEF-loggar (Common Event Format). Läs om hur du [ansluter en säkerhetslösning](quick-security-solutions.md) som använder CEF-loggar till Security Center.

### <a name="add-data-sources"></a>Lägg till datakällor

Avsnittet **Lägg till datakällor** innehåller andra tillgängliga datakällor som kan anslutas. Om du vill få anvisningar om att lägga till data från någon av dessa källor klickar du på **LÄGG TILL**.

![Datakällor](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exporterar data till en SIEM

Bearbetade händelser som genereras av Azure Security Center publiceras till Azure [aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), någon av loggen skriver tillgängliga i Azure Monitor. Azure Monitor erbjuder en konsoliderad pipeline för routning någon av dina övervakade data i ett SIEM-verktyg. Detta görs genom att dessa data till en Händelsehubb där det kan sedan hämtas för direktuppspelning i ett partner-verktyg.

Den här pipe använder den [Azure Monitoring enkel rörledning](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) för att få åtkomst till övervakningsdata från Azure-miljön. På så sätt kan du enkelt konfigurera Siem och övervakningsverktyg för att använda data.

I nästa avsnitt beskrivs hur du kan konfigurera data strömmas till en händelsehubb. Förutsätter vi att du redan har konfigurerats i din Azure-prenumeration för Azure Security Center.

Översikt på hög nivå

![Översikt på hög nivå](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Vad är Azure-säkerhetsdata som är exponerade för SIEM?

I den här versionen vi exponerar den [säkerhetsaviseringar.](../security-center/security-center-managing-and-responding-alerts.md) Vi kommer berika datauppsättningen med säkerhetsrekommendationer i kommande versioner.

### <a name="how-to-setup-the-pipeline"></a>Så här konfigurerar du pipelinen

#### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Innan du börjar måste du [skapa ett namnområde för Event Hubs](../event-hubs/event-hubs-create.md). Den här namnområde och en Händelsehubb är målet för alla dina övervakningsdata.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Stream Azure-aktivitetsloggen till Event Hubs

Se följande artikel [strömma aktivitetsloggen till Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Installera en partneranslutningsapp 

Routning övervakningsdata till en Event Hub med Azure Monitor kan du lätt kan integrera med partner SIEM och övervakningsverktyg.

Finns på följande länk för att se en lista över [SIEMs som stöds](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="example-for-querying-data"></a>Exempel för att fråga efter data 

Här är några Splunk frågor som du kan använda för att hämta aviseringsdata:

| **Beskrivning av fråga** | **Fråga** |
|----|----|
| Alla aviseringar| index = huvudsakliga Microsoft.Security/locations/alerts|
| Sammanfatta antalet åtgärder efter deras namn| index = huvudsakliga sourcetype = ”amal: säkerhet” \| tabell operationName \| stats antal efter operationName|
| Få aviseringar information: Tid, namn, status, ID och prenumeration | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Nästa steg

Den här artikeln berättade om hur man integrerar partnerlösningar i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om användningen av Security Center.
* [Azure säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
