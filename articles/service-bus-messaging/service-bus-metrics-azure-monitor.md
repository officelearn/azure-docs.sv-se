---
title: Azure Service Bus-mått i Azure Monitor| Microsoft-dokument
description: I den här artikeln beskrivs hur du använder Azure Monitor för att övervaka Service Bus-entiteter (köer, ämnen och prenumerationer).
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 227dfaff211eb60c5c2b25b5c76ecc82b6ce3edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240793"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Service Bus-mått i Azure Monitor

Service Bus-mått ger dig tillståndet för resurser i din Azure-prenumeration. Med en omfattande uppsättning måttdata kan du bedöma den övergripande hälsotillståndet för servicebussresurserna, inte bara på namnområdesnivå, utan även på entitetsnivå. Denna statistik kan vara viktig eftersom de hjälper dig att övervaka tillståndet för Service Bus. Mått kan också hjälpa till att felsöka problem med rotorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor tillhandahåller enhetliga användargränssnitt för övervakning av olika Azure-tjänster. Mer information finns [i Övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och hämta Azure [Monitor-mått med .NET-exempel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) på GitHub.

> [!IMPORTANT]
> När det inte har funnits någon interaktion med en entitet på 2 timmar börjar måtten visa "0" som ett värde tills entiteten inte längre är inaktiv.

## <a name="access-metrics"></a>Åtkomstmått

Azure Monitor innehåller flera sätt att komma åt mått. Du kan antingen komma åt mått via [Azure-portalen](https://portal.azure.com)eller använda AZURE Monitor API:er (REST och .NET) och analyslösningar som Azure Monitor-loggar och eventhubbar. Mer information finns [i Mått i Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarnas data. Om du behöver behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Det här värdet är konfigurerat i [diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomstmått i portalen

Du kan övervaka mått över tid i [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Om du vill göra det markerar du namnområdet och klickar sedan på **Mått**. Om du vill visa mått som filtrerats till entitetens omfattning markerar du entiteten och klickar sedan på **Mått**.

![][2]

För mått som stöder dimensioner måste du filtrera med önskat dimensionsvärde.

## <a name="billing"></a>Fakturering

Mått och aviseringar på Azure Monitor debiteras per avisering. Dessa avgifter bör vara tillgängliga på portalen när aviseringen är inställd och innan den sparas. 

Ytterligare lösningar som ger ut mätdata faktureras direkt av dessa lösningar. Du debiteras till exempel av Azure Storage om du arkiverar måttdata till ett Azure Storage-konto. Du faktureras också av Log Analytics om du strömmar statistikdata till Log Analytics för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi inaktuella flera mått när de flyttas under ett annat namn. Detta kan kräva att du uppdaterar dina referenser. Mått som markerats med nyckelordet "föråldrad" stöds inte framöver.

Alla måttvärden skickas till Azure Monitor varje minut. Tidsgranulariteten definierar det tidsintervall för vilket måttvärden presenteras. Tidsintervallet som stöds för alla servicebussmått är 1 minut.

## <a name="request-metrics"></a>Begär mått

Räknar antalet begäranden om data- och hanteringsåtgärder.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande begäranden| Antalet begäranden som görs till servicebussen under en angiven period. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Lyckade förfrågningar|Antalet lyckade begäranden som gjorts till servicebussen under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Serverfel|Antalet begäranden som inte har bearbetats på grund av ett fel i servicebusstjänsten under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Användarfel (se följande underavsnitt)|Antalet begäranden som inte har bearbetats på grund av användarfel under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Begränsade begäranden|Antalet begäranden som begränsades eftersom användningen har överskridits.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|

### <a name="user-errors"></a>Användarfel

Följande två typer av fel klassificeras som användarfel:

1. Fel på klientsidan (i HTTP som skulle vara 400 fel).
2. Fel som uppstår vid bearbetning av meddelanden, till exempel [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Mått på meddelanden

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden|Antalet händelser eller meddelanden som skickas till Service Bus under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
|Utgående meddelanden|Antalet händelser eller meddelanden som tagits emot från Service Bus under en angiven period.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|
| Meddelanden| Antal meddelanden i en kö/ett ämne. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/> Dimension: EntityName |
| ActiveMessages| Antal aktiva meddelanden i en kö/ett ämne. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/> Dimension: EntityName |
| Meddelanden med obeskrivna meddelanden| Antal meddelanden med obeställda meddelanden i en kö/ett ämne. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde <br/>Dimension: EntityName |
| Schemalagda meddelanden| Antal schemalagda meddelanden i en kö/ett ämne. <br/><br/> Enhet: Antal <br/> Aggregeringstyp: Medelvärde  <br/> Dimension: EntityName |

> [!NOTE]
> Värden för följande mått är point-in-time-värden. Inkommande meddelanden som förbrukades omedelbart efter den tidpunkten kanske inte återspeglas i dessa mått. 
> - Meddelanden
> - Aktiva meddelanden 
> - Meddelanden med obeskrivna meddelanden 
> - Schemalagda meddelanden 

## <a name="connection-metrics"></a>Anslutningsmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|ActiveConnections (Aktiva Anslutning)|Antalet aktiva anslutningar på ett namnområde och på en entitet.<br/><br/> Enhet: Antal <br/> Aggregeringstyp: Totalt <br/> Dimension: EntityName|

## <a name="resource-usage-metrics"></a>Mått för resursanvändning

> [!NOTE] 
> Följande mått är endast tillgängliga med **premiumnivån.** 
> 
> De viktiga mått som ska övervakas för eventuella avbrott för ett namnområde på premiumnivå är: **CPU-användning per namnområde** och **minnesstorlek per namnområde**. [Ställ in aviseringar](../azure-monitor/platform/alerts-metric.md) för dessa mått med Hjälp av Azure Monitor.
> 
> Det andra måttet som du kan övervaka är: **begränsade begäranden**. Det bör inte vara ett problem men så länge namnområdet stannar inom dess minne, CPU och förmedlade anslutningar gränser. Mer information finns [i Begränsning på Azure Service Bus Premium-nivå](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|CPU-användning per namnområde|Den procentuella CPU-användningen av namnområdet.<br/><br/> Enhet: Procent <br/> Aggregeringstyp: Maximal <br/> Dimension: EntityName|
|Minnesstorleksanvändning per namnområde|Den procentuella minnesanvändningen för namnområdet.<br/><br/> Enhet: Procent <br/> Aggregeringstyp: Maximal <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Måttdimensioner

Azure Service Bus stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i måtten. Om du inte lägger till dimensioner anges mått på namnområdesnivå. 

|Dimensionsnamn|Beskrivning|
| ------------------- | ----------------- |
|Entityname| Service Bus stöder meddelandeentiteter under namnområdet.|

## <a name="set-up-alerts-on-metrics"></a>Ställa in aviseringar för mått

1. På fliken **Mått** på sidan **Service Bus-namnområde** väljer du **Konfigurera aviseringar**. 

    ![Sidan Mått - Menyn Konfigurera aviseringar](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Välj alternativet **Välj mål** och gör följande åtgärder på sidan Välj **en resurs:** 
    1. Välj **Service Bus-namnområden** för fältet **Filter efter resurstyp.** 
    2. Välj din prenumeration för fältet **Filter efter prenumeration.**
    3. Välj **namnområdet för servicebuss** i listan. 
    4. Välj **Done** (Klar). 
    
        ![Välj namnområde](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Välj **Lägg till villkor**och gör följande åtgärder på sidan Konfigurera **signallogik:**
    1. Välj **mått** för **signaltyp**. 
    2. Välj en signal. Till exempel: **Servicefel**. 

        ![Välj serverfel](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Välj **Större än** för **Villkor**.
    2. Välj **Summa** för **tidsaggregering**. 
    3. Ange **5** för **tröskelvärde**. 
    4. Välj **Done** (Klar).    

        ![Ange villkor](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. På sidan **Skapa regel** expanderar du **Definiera aviseringsinformation**och gör följande åtgärder:
    1. Ange ett **namn** på aviseringen. 
    2. Ange en **beskrivning** av aviseringen.
    3. Välj **allvarlighetsgrad** för aviseringen. 

        ![Aviseringsinformation](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. På sidan **Skapa regel** expanderar du **Definiera åtgärdsgrupp,** väljer **Ny åtgärdsgrupp**och utför följande åtgärder på **sidan Lägg till åtgärdsgrupp**. 
    1. Ange ett namn på åtgärdsgruppen.
    2. Ange ett kort namn för åtgärdsgruppen. 
    3. Välj din prenumeration. 
    4. Välj en resursgrupp. 
    5. För den här genomgången anger du **Skicka e-post** för **ÅTGÄRDSNAMN**.
    6. Välj **E-post/SMS/Push/Voice** för **ÅTGÄRDSTYP**. 
    7. Välj **Redigera information**. 
    8. Gör följande åtgärder på sidan **E-post/SMS/Push/Voice:**
        1. Välj **E-post**. 
        2. Skriv **e-postadressen**. 
        3. Välj **OK**.

            ![Aviseringsinformation](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. På sidan **Lägg till åtgärdsgrupp** väljer du **OK**. 
1. På sidan **Skapa regel** väljer du **Skapa aviseringsregel**. 

    ![Knappen Skapa varningsregel](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Nästa steg

Se [översikten över Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


