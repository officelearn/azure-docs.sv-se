---
title: Azure Service Bus mått i Azure Monitor | Microsoft Docs
description: Använd Azure Monitor för att övervaka Service Bus entiteter
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: 6d25bdf6ff8e790466f3a28e3b6043e347d74198
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261860"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Service Bus mått i Azure Monitor

Service Bus mått ger dig tillstånd för resurser i din Azure-prenumeration. Med en omfattande uppsättning Mät data kan du utvärdera den övergripande hälsan för dina Service Bus-resurser, inte bara på namn områdes nivå, utan även på enhets nivå. Den här statistiken kan vara viktig när de hjälper dig att övervaka status för Service Bus. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor innehåller enhetligt användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [hämta Azure Monitor-mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) i GitHub.

> [!IMPORTANT]
> När det inte har varit någon interaktion med en entitet i 2 timmar börjar måtten Visa "0" som ett värde tills entiteten inte längre är inaktiv.

## <a name="access-metrics"></a>Åtkomst-mått

Azure Monitor innehåller flera sätt att åtkomst mått. Du kan antingen komma åt mått via [Azure Portal](https://portal.azure.com)eller använda Azure Monitor-API: er (rest och .net) och analys lösningar som Azure Monitor loggar och Event Hubs. Mer information finns i [mått i Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna data. Om du vill behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Det här värdet konfigureras i [diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst-mått i portal

Du kan övervaka mått med tiden i den [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Det gör du genom att välja ditt namn område och sedan klicka på **mått**. Om du vill visa mått som filtrerats till omfånget för entiteten väljer du entiteten och klickar sedan på **mått**.

![][2]

För mått som stöder dimensioner måste du filtrera med det önskade dimension svärdet.

## <a name="billing"></a>Fakturering

Mått och aviseringar på Azure Monitor debiteras per avisering. Dessa avgifter bör vara tillgängliga på portalen när aviseringen är konfigurerad och innan den sparas. 

Ytterligare lösningar som inhämtar mått data debiteras direkt av dessa lösningar. Exempelvis debiteras du per Azure Storage om du arkiverar måttdata till ett Azure Storage-konto. Du debiteras också av Log Analytics om du strömmar mått data till Log Analytics för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi avvecklar flera mått när de flyttas under ett annat namn. Detta kräver att du uppdaterar dina referenser. Mått som markerats med nyckelordet ”inaktuell” stöds inte framöver.

Alla mått-värden skickas till Azure Monitor varje minut. Tidskornighet definierar tidsintervallet som mått visas. Det tidsintervall som stöds för alla Service Bus mått är 1 minut.

## <a name="request-metrics"></a>Begäran-mått

Räknar antalet förfrågningar om data och hantering av åtgärder.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande förfrågningar| Antalet begär Anden som gjorts till tjänsten Service Bus under en angiven period. <br/><br/> Processor Count <br/> Sammansättnings typ: Totalt <br/> Dimension EntityName|
|Slutförda förfrågningar|Antalet lyckade förfrågningar som gjorts till tjänsten Service Bus under en angiven period.<br/><br/> Processor Count <br/> Sammansättnings typ: Totalt <br/> Dimension EntityName|
|Serverfel|Antalet begär Anden som inte behandlats på grund av ett fel i tjänsten Service Bus under en angiven period.<br/><br/> Processor Count <br/> Sammansättnings typ: Totalt <br/> Dimension EntityName|
|Användar fel (se följande underavsnitt)|Antal begäranden som inte bearbetas på grund av användarfel under en angiven period.<br/><br/> Processor Count <br/> Sammansättnings typ: Totalt <br/> Dimension EntityName|
|Begränsade begär Anden|Antalet begär Anden som har begränsats eftersom användningen överskreds.<br/><br/> Processor Count <br/> Sammansättnings typ: Totalt <br/> Dimension EntityName|

### <a name="user-errors"></a>Användar fel

Följande två typer av fel klassificeras som användar fel:

1. Fel på klient sidan (i HTTP som är 400-fel).
2. Fel som inträffar under bearbetning av meddelanden, till exempel [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Meddelande-mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden|Antalet händelser eller meddelanden som skickats till Service Bus under en angiven period.<br/><br/> Processor Count <br/> Sammansättnings typ: Totalt <br/> Dimension EntityName|
|Utgående meddelanden|Antalet händelser eller meddelanden som har tagits emot från Service Bus under en angiven period.<br/><br/> Processor Count <br/> Sammansättnings typ: Totalt <br/> Dimension EntityName|
| Meddelanden| Antal meddelanden i en kö/ett ämne. <br/><br/> Processor Count <br/> Sammansättnings typ: Average <br/> Dimension EntityName |
| ActiveMessages| Antal aktiva meddelanden i en kö/ett ämne. <br/><br/> Processor Count <br/> Sammansättnings typ: Average <br/> Dimension EntityName |
| Meddelanden med obeställbara meddelanden| Antal meddelanden om obeställbara meddelanden i en kö/ett ämne. <br/><br/> Processor Count <br/> Sammansättnings typ: Average <br/>Dimension EntityName |
| Schemalagda meddelanden| Antal schemalagda meddelanden i en kö/ett ämne. <br/><br/> Processor Count <br/> Sammansättnings typ: Average  <br/> Dimension EntityName |

## <a name="connection-metrics"></a>Anslutningsmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|ActiveConnections|Antal aktiva anslutningar för ett namnområde och på en entitet.<br/><br/> Processor Count <br/> Sammansättnings typ: Totalt <br/> Dimension EntityName|

## <a name="resource-usage-metrics"></a>Användnings statistik för resursanvändningen

> [!NOTE] 
> Följande mått är bara tillgängliga med **Premium** nivån. 

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|CPU-användning per namnrymd|Procent andel CPU-användning för namn området.<br/><br/> Processor Percent <br/> Sammansättnings typ: Maximal <br/> Dimension EntityName|
|Minnes storleks användning per namnrymd|Den procentuella minnes användningen för namn området.<br/><br/> Processor Percent <br/> Sammansättnings typ: Maximal <br/> Dimension EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Service Bus stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i dina mått. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivå. 

|Dimensions namn|Beskrivning|
| ------------------- | ----------------- |
|EntityName| Service Bus stöder meddelande enheter under namn området.|

## <a name="set-up-alerts-on-metrics"></a>Konfigurera aviseringar för mått

1. På fliken **mått** på sidan **Service Bus namn område** väljer du **Konfigurera aviseringar**. 

    ![Sidan mått – konfigurera aviseringar-menyn](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Välj alternativet **Välj mål** och utför följande åtgärder på sidan **Välj en resurs** : 
    1. Välj **Service Bus namnrum** för fältet **Filtrera efter resurs typ** . 
    2. Välj din prenumeration för fältet **Filtrera efter prenumeration** .
    3. Välj **Service Bus-namnområdet** i listan. 
    4. Välj **Done** (Klar). 
    
        ![Välj namnområde](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Välj **Lägg till kriterier**och utför följande åtgärder på sidan **Konfigurera signal logik** :
    1. Välj **mått** för **signal typ**. 
    2. Välj en signal. Exempel: **Tjänst fel**. 

        ![Välj server fel](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Välj **större än** för **villkor**.
    2. Välj **Total** för **tids agg regering**. 
    3. Ange **5** som **tröskelvärde**. 
    4. Välj **Done** (Klar).    

        ![Ange villkor](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. På sidan **Skapa regel** expanderar du **definiera aviserings information**och utför följande åtgärder:
    1. Ange ett **namn** för aviseringen. 
    2. Ange en **Beskrivning** av aviseringen.
    3. Välj **allvarlighets grad** för aviseringen. 

        ![Aviseringsinformation](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. På sidan **Skapa regel** expanderar du **definiera åtgärds grupp**, väljer **ny åtgärds grupp**och utför följande åtgärder på **sidan Lägg till åtgärds grupp**. 
    1. Ange ett namn på åtgärds gruppen.
    2. Ange ett kort namn för åtgärds gruppen. 
    3. Välj din prenumeration. 
    4. Välj en resursgrupp. 
    5. I den här genom gången anger du **skicka e-post** som **Åtgärds namn**.
    6. Välj **e-post/SMS/push/röst** som **Åtgärds typ**. 
    7. Välj **Redigera information**. 
    8. På sidan **e-post/SMS/push/Voice** gör du följande:
        1. Välj **e-post**. 
        2. Ange **e-postadressen**. 
        3. Välj **OK**.

            ![Aviseringsinformation](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. På sidan **Lägg till åtgärds grupp** väljer du **OK**. 
1. På sidan **Skapa regel** väljer du **skapa aviserings regel**. 

    ![Knappen Skapa aviserings regel](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Nästa steg

Se [Azure Monitor översikt](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


