---
title: Diagnostikloggar för Azure SignalR-tjänsten
description: Lär dig hur du konfigurerar diagnostikloggar för Azure SignalR-tjänsten och hur du använder den för att felsöka själv.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 72f57ba4bbbbde07f6d26edc88c158f301ebe2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536742"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Diagnostikloggar för Azure SignalR-tjänsten

Den här självstudien beskriver vad som är diagnostikloggar för Azure SignalR-tjänsten och hur du konfigurerar diagnostikloggar och hur du felsöker med diagnostikloggar.

## <a name="prerequisites"></a>Krav
Om du vill aktivera diagnostikloggar måste du lagra loggdata någonstans. Den här självstudien använder Azure Storage och Log Analytics.

* [Azure storage](../azure-monitor/platform/resource-logs-collect-storage.md) - Behåller diagnostikloggar för principgranskning, statisk analys eller säkerhetskopiering.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) - Ett flexibelt loggsöknings- och analysverktyg som möjliggör analys av råloggar som genereras av en Azure-resurs.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Konfigurera diagnostikloggar för en Azure SignalR-tjänst

Du kan visa diagnostikloggar för Azure SignalR-tjänst. Dessa loggar ger en rikare vy över anslutningen till din Azure SignalR-tjänstinstans. Diagnostikloggarna ger detaljerad information om varje anslutning. Till exempel grundläggande information (användar-ID, anslutnings-ID och transporttyp och så vidare) och händelseinformation (anslut, koppla från och avbryta händelse och så vidare) av anslutningen. Diagnostikloggar kan användas för ärendeidentifiering, anslutningsspårning och analys.

### <a name="enable-diagnostic-logs"></a>Aktivera diagnostikloggar

Diagnostikloggar är inaktiverade som standard. Så här aktiverar du diagnostikloggar:

1. Klicka på **Diagnostikinställningar**under **Övervakning**i [Azure-portalen](https://portal.azure.com).

    ![Navigering i fönster till diagnostikinställningar](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Klicka sedan på **Lägg till diagnostikinställning**.

    ![Lägg till diagnostikloggar](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Ange önskat arkivmål. För närvarande stöder vi **Arkiv till ett lagringskonto** och **Skicka till Log Analytics**.

1. Markera de loggar som du vill arkivera.

    ![Fönstret Diagnostikinställningar](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Spara de nya diagnostikinställningarna.

Nya inställningar träder i kraft om cirka 10 minuter. Därefter visas loggar i det konfigurerade arkiveringsmålet i fönstret **Diagnostikloggar.**

Mer information om hur du konfigurerar diagnostik finns i [översikten över Diagnostikloggar i Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="diagnostic-logs-categories"></a>Kategorier för diagnostikloggar

Azure SignalR Service samlar in diagnostikloggar i en kategori:

* **Alla loggar:** Spåra anslutningar som ansluter till Azure SignalR-tjänsten. Loggarna innehåller information om anslutning/frånkoppling, autentisering och begränsning. Mer information finns i nästa avsnitt.

### <a name="archive-to-a-storage-account"></a>Arkivera till ett lagringskonto

Loggar lagras i lagringskontot som konfigurerats i fönstret **Diagnostikloggar.** En behållare `insights-logs-alllogs` med namnet skapas automatiskt för att lagra diagnostikloggar. Inuti behållaren lagras loggar i `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`filen . I grund och botten `resource ID` är `Date Time`vägen kombineras med och . Loggfilerna delas `hour`av . Därför är `m=00`protokollet alltid .

Alla loggar lagras i JSON-format (JavaScript Object Notation). Varje post har strängfält som använder det format som beskrivs i följande avsnitt.

JSON-strängar med arkivlogger innehåller element som anges i följande tabeller:

**Format**

Namn | Beskrivning
------- | -------
time | Logga händelsetid
nivå | Logga händelsenivå
resourceId | Resurs-ID för din Azure SignalR-tjänst
location | Plats för din Azure SignalR-tjänst
category | Kategori för logghändelsen
operationName | Åtgärdens namn på händelsen
callerIpAddress | IP-adressen till servern/klienten
properties | Detaljerade egenskaper relaterade till den här logghändelsen. Mer information finns i egenskapstabellen nedan

**Egenskapstabell**

Namn | Beskrivning
------- | -------
typ | Typ av logghändelse. För närvarande tillhandahåller vi information om anslutning till Azure SignalR-tjänsten. Endast `ConnectivityLogs` typ är tillgänglig
samling | Insamling av logghändelsen. Tillåtna värden `Connection` `Authorization` är: och`Throttling`
connectionId | Anslutningens identitet
transportTyp | Transporttyp för anslutningen. Tillåtna värden `Websockets` \| `ServerSentEvents` \| är:`LongPolling`
connectionType (anslutningstyp) | Typ av anslutning. Tillåtna värden `Server` \| `Client`är: . `Server`: Anslutning från serversidan; `Client`: anslutning från klientsidan
userId | Användarens identitet
meddelande | Detaljerat meddelande om logghändelse

Följande kod är ett exempel på en JSON-sträng för arkivloggen:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Arkivloggschema för Log Analytics

Så här visar du diagnostikloggar:

1. Klicka `Logs` i din mållogganalys.

    ![Menyalternativ för Logganalys](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Ange `SignalRServiceDiagnosticLogs` och välj tidsintervall för att fråga efter diagnostikloggar. Avancerad fråga finns [i Komma igång med Logganalys i Azure Monitor](../azure-monitor/log-query/get-started-portal.md)

    ![Frågelogg i Logganalys](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Arkivloggkolumner innehåller element som visas i följande tabell:

Namn | Beskrivning
------- | ------- 
TimeGenerated | Logga händelsetid
Samling | Insamling av logghändelsen. Tillåtna värden `Connection` `Authorization` är: och`Throttling`
OperationName | Åtgärdens namn på händelsen
Location | Plats för din Azure SignalR-tjänst
Nivå | Logga händelsenivå
UppringarenSadress | IP-adressen till servern/klienten
Meddelande | Detaljerat meddelande om logghändelse
UserId | Användarens identitet
ConnectionId (på)-anslutning) | Anslutningens identitet
ConnectionType (Anslutningstyp) | Typ av anslutning. Tillåtna värden `Server` \| `Client`är: . `Server`: Anslutning från serversidan; `Client`: anslutning från klientsidan
TransportTyp | Transporttyp för anslutningen. Tillåtna värden `Websockets` \| `ServerSentEvents` \| är:`LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Felsökning med diagnostikloggar

Om du vill felsöka för Azure SignalR-tjänsten kan du aktivera server-/klientloggar för att fånga fel. För närvarande exponerar Azure SignalR-tjänsten diagnostikloggar, du kan också aktivera loggar för tjänstsidan.

När du stöter på en oväntad tillväxt- eller fallande situation för anslutning kan du dra nytta av diagnostikloggar för felsökning.

Typiska problem handlar ofta om anslutningars oväntade kvantitetsändringar, anslutningar når anslutningsgränser och auktoriseringsfel. Se nästa avsnitt om felsökning.

#### <a name="unexpected-connection-number-changes"></a>Oväntade ändringar av anslutningsnummer

##### <a name="unexpected-connection-dropping"></a>Oväntad anslutning sjunker

Om du stöter på oväntade anslutningar släppa, först aktivera loggar in service, server och klient sidor.

Om en anslutning kopplas från registrerar diagnostikloggarna den här `ConnectionAborted` `ConnectionEnded` frånkopplingshändelsen, visas eller i `operationName`.

Skillnaden mellan `ConnectionAborted` `ConnectionEnded` och `ConnectionEnded` är det en förväntad frånkoppling som utlöses av klient- eller serversidan. Medan `ConnectionAborted` det vanligtvis är en oväntad anslutningsavstringshändelse, och avbruten orsak kommer att anges i `message`.

Avbrutna orsaker visas i följande tabell:

Orsak | Beskrivning
------- | ------- 
Antalet anslutningar når gränsen | Antalet anslutningar når gränsen för din nuvarande prisnivå. Överväg att skala upp serviceenheten
Programservern stängde anslutningen | Appserver utlöser aborten. Det kan betraktas som en förväntad abort
Timeout för anslutnings ping | Vanligtvis orsakas det av nätverksproblem. Överväg att kontrollera appserverns tillgänglighet från internet
Omlastning av tjänsten, återanslut | Azure SignalR-tjänsten laddar om. Azure SignalR stöder automatisk återanslutning, du kan vänta tills återansluten eller manuellt återansluta till Azure SignalR Service
Internt fel på servertransgående | Tillfälliga fel uppstår i Azure SignalR Service, bör återställas automatiskt
Serveranslutningen har tagits bort | Serveranslutningen sjunker med okänt fel, överväg självfelsökning med service/server/klientsidan först. Försök att utesluta grundläggande problem (t.ex. nätverksproblem, problem på appserversidan och så vidare). Om problemet inte är löst kontaktar du oss för ytterligare hjälp. Mer information finns i Avsnittet [Hämta hjälp.](#get-help) 

##### <a name="unexpected-connection-growing"></a>Oväntad anslutning växer

För att felsöka om oväntad anslutning växer, det första du behöver göra är att filtrera bort de extra anslutningarna. Du kan lägga till unikt testanvändar-ID i testklientanslutningen. Kontrollera sedan in den med diagnostikloggar, om du ser mer än en klientanslutningar har samma testanvändar-ID eller IP, är det troligt att klientsidan skapar och upprättar fler anslutningar än förväntan. Kolla din klientsida.

#### <a name="authorization-failure"></a>Auktoriseringen misslyckades

Om du får 401 Obehöriga returnerade för klientbegäranden kontrollerar du dina diagnostikloggar. Om du `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`stöter på betyder det att alla målgrupper i din åtkomsttoken är ogiltiga. Försök att använda de giltiga målgrupper som föreslås i loggen.


#### <a name="throttling"></a>Begränsning

Om du upptäcker att du inte kan upprätta SignalR-klientanslutningar till Azure SignalR-tjänsten kontrollerar du dina diagnostikloggar. Om du `Connection count reaches limit` stöter på i diagnostikloggen upprättar du för många anslutningar till SignalR-tjänsten, som når gränsen för anslutningsantal. Överväg att skala upp din SignalR-tjänst. Om du `Message count reaches limit` stöter på i diagnostikloggen betyder det att du använder den kostnadsfria nivån och att du använder kvoten för meddelanden. Om du vill skicka fler meddelanden kan du överväga att ändra SignalR-tjänsten till standardnivå för att skicka ytterligare meddelanden. Mer information finns i [Azure SignalR Service Pricing](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Få hjälp

Vi rekommenderar att du felsöker själv först. De flesta problem orsakas av appserver eller nätverksproblem. Följ [felsökningsguiden med diagnostiklogg](#troubleshooting-with-diagnostic-logs) och [grundläggande felsökningsguide](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) för att hitta orsaken.
Om problemet fortfarande inte kan lösas bör du överväga att öppna ett problem i GitHub eller skapa biljett i Azure Portal.
Ge:
1. Tidsintervall ca 30 minuter när problemet uppstår
2. Azure SignalR-tjänstens resurs-ID
3. Probleminformation, så specifik som möjligt: Appserver skickar till exempel inte meddelanden, klientanslutningen sjunker och så vidare
4. Loggar som samlas in från server-/klientsidan och annat material som kan vara användbart
5. [Valfritt] Repro-kod

> [!NOTE]
> Om du öppnar problemet i GitHub ska du hålla känslig information (till exempel resurs-ID, server-/klientloggar) privat, skicka endast till medlemmar i Microsoft-organisationen privat.