---
title: Diagnostikloggar för Azure SignalR service
description: Lär dig hur du konfigurerar diagnostikloggar för Azure SignalR service och hur du använder den för att felsöka.
author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 33d9a338e12fa4b3d2449f0c5b0576895364c3cf
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750262"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Diagnostikloggar för Azure SignalR service

I den här självstudien beskrivs vad är diagnostikloggar för Azure SignalR-tjänsten och hur du konfigurerar diagnostikloggar och hur du felsöker med diagnostikloggar.

## <a name="prerequisites"></a>Krav
Om du vill aktivera diagnostikloggar måste du lagra dina loggdata någonstans. I den här självstudien används Azure Storage och Log Analytics.

* [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md) – behåller diagnostikloggar för princip granskning, statisk analys eller säkerhets kopiering.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) – ett flexibelt loggs öknings-och analys verktyg som möjliggör analys av obehandlade loggar som genereras av en Azure-resurs.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Konfigurera diagnostikloggar för en Azure SignalR-tjänst

Du kan visa diagnostikloggar för Azure SignalR-tjänsten. Dessa loggar ger en mer omfattande vy över anslutningen till din Azure SignalR-tjänstinstans. Diagnostikloggar innehåller detaljerad information om varje anslutning. Till exempel grundläggande information (användar-ID, anslutnings-ID och transport typ och så vidare) och händelse information (Anslut, koppla från och Avbryt händelse och så vidare) för anslutningen. Diagnostikloggar kan användas för problem identifiering, anslutnings spårning och analys.

### <a name="enable-diagnostic-logs"></a>Aktivera diagnostikloggar

Diagnostikloggar är inaktiverade som standard. Följ dessa steg om du vill aktivera diagnostikloggar:

1.  Klicka på **diagnostikinställningar**under **övervakning**i [Azure Portal](https://portal.azure.com).

    ![Fönster navigering till diagnostikinställningar](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1.  Klicka sedan på **Lägg till diagnostisk inställning**.

    ![Lägg till diagnostikloggar](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1.  Ange det mål för arkiv som du vill använda. För närvarande stöder vi **arkivering till ett lagrings konto** och **skickar till Log Analytics**.

1. Välj de loggar som du vill arkivera.

    ![Fönstret Diagnostikinställningar](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1.  Spara de nya diagnostikinställningarna för.

Nya inställningarna träder i kraft i cirka 10 minuter. Efter det loggar visas i den konfigurerade mål för arkivering, i den **diagnostikloggar** fönstret.

Mer information om hur du konfigurerar diagnostik finns i den [översikt över Azure diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md).

### <a name="diagnostic-logs-categories"></a>Diagnostikloggar kategorier

Azure SignalR service fångar in diagnostikloggar i en kategori:

* **Alla loggar**: spåra anslutningar som ansluter till Azure SignalR-tjänsten. Loggarna ger information om anslutning/från koppling, autentisering och begränsning. Mer information finns i nästa avsnitt.

### <a name="archive-to-a-storage-account"></a>Arkivera till ett lagringskonto

Loggar lagras i det lagrings konto som har kon figurer ATS i fönstret **diagnostikloggar** . En behållare med namnet `insights-logs-alllogs` skapas automatiskt för att lagra diagnostikloggar. I behållaren lagras loggar i filen `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`. I princip kombineras sökvägen med `resource ID` och `Date Time`. Loggfilerna delas av `hour`. Därför är minuterna alltid `m=00`.

Alla loggar lagras i JavaScript Object Notation (JSON)-format. Varje post innehåller strängfält som använder formatet som beskrivs i följande avsnitt.

Arkiv loggens JSON-strängar innehåller element som anges i följande tabeller:

**Formatering**

Namn | Beskrivning
------- | -------
time | Händelse tid för logg
level | Logga händelse nivå
resourceId | Resurs-ID för Azure SignalR-tjänsten
location | Plats för Azure SignalR-tjänsten
category | Kategori för logg händelsen
operationName | Åtgärds namn för händelsen
callerIpAddress | IP-adress för servern/klienten
properties | Detaljerade egenskaper relaterade till den här logg händelsen. Mer information finns i tabellen egenskaper nedan

**Egenskaps tabell**

Namn | Beskrivning
------- | -------
typ | Typ av logg händelse. För närvarande ger vi information om anslutningen till Azure SignalR-tjänsten. Endast `ConnectivityLogs` typ är tillgänglig
samling | Samling av logg händelsen. Tillåtna värden är: `Connection`, `Authorization` och `Throttling`
connectionId | Anslutningens identitet
transportType | Anslutningens transport typ. Tillåtna värden är: `Websockets` \| `ServerSentEvents` \| `LongPolling`
connectionType | Typ av anslutning. Tillåtna värden är: `Server` \| `Client`. `Server`: anslutning från Server Sidan; `Client`: anslutning från klient Sidan
userId | Användarens identitet
meddelande | Detaljerat meddelande om logg händelse

Följande kod är ett exempel på en arkivera loggen JSON-sträng:

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

### <a name="archive-logs-schema-for-log-analytics"></a>Schema för Arkiv logg för Log Analytics

Följ dessa steg om du vill visa diagnostikloggar:

1. Klicka på `Logs` i mål Log Analytics.

    ![Meny alternativ för Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Ange `SignalRServiceDiagnosticLogs` och välj tidsintervall för att köra diagnostiska loggar. För avancerade frågor, se [Kom igång med Log Analytics i Azure Monitor](../azure-monitor/log-query/get-started-portal.md)

    ![Fråga logg i Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Arkivera logg kolumner innehåller element som anges i följande tabell:

Namn | Beskrivning
------- | ------- 
TimeGenerated | Händelse tid för logg
Samling | Samling av logg händelsen. Tillåtna värden är: `Connection`, `Authorization` och `Throttling`
OperationName | Åtgärds namn för händelsen
Location | Plats för Azure SignalR-tjänsten
Nivå | Logga händelse nivå
CallerIpAddress | IP-adress för servern/klienten
Meddelande | Detaljerat meddelande om logg händelse
UserID | Användarens identitet
ConnectionId | Anslutningens identitet
ConnectionType | Typ av anslutning. Tillåtna värden är: `Server` \| `Client`. `Server`: anslutning från Server Sidan; `Client`: anslutning från klient Sidan
TransportType | Anslutningens transport typ. Tillåtna värden är: `Websockets` \| `ServerSentEvents` \| `LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Fel sökning med diagnostikloggar

Om du vill felsöka för Azure SignalR-tjänsten kan du aktivera Server-/klient Side loggar för att fånga fel. Azure SignalR-tjänsten exponerar diagnostikloggar, men du kan också aktivera loggar för tjänst sidan.

När du stöter på en oväntad växande eller tappad situation kan du använda diagnostikloggar för fel sökning.

Vanliga problem är ofta om anslutningarnas oväntade antal ändringar, anslutningarna når anslutnings gränser och ett auktoriseringsfel. Se nästa avsnitt om hur du felsöker.

#### <a name="unexpected-connection-number-changes"></a>Oväntade anslutnings nummer ändringar

##### <a name="unexpected-connection-dropping"></a>Oväntad anslutning tappas

Om du stöter på oväntade anslutningar ska du först aktivera loggar i tjänst-, server-och klient sidor.

Om anslutningen kopplas från, kommer diagnostikloggar att registrera den här från kopplings händelsen, du ser `ConnectionAborted` eller `ConnectionEnded` i `operationName`.

Skillnaden mellan `ConnectionAborted` och `ConnectionEnded` är att `ConnectionEnded` är en förväntad från koppling som utlöses av klienten eller Server sidan. `ConnectionAborted` är vanligt vis en oväntad händelse för att släppa anslutningen, och avbrotts orsak ges i `message`.

Avbrotts orsakerna visas i följande tabell:

Orsak | Beskrivning
------- | ------- 
Antalet anslutningar når gränsen | Antalet anslutningar når gränsen för den aktuella pris nivån. Överväg att skala upp tjänst enhet
Program servern stängde anslutningen | App Server utlöser avbrottet. Det kan betraktas som ett förväntat avbrott
Timeout för anslutnings-ping | Det beror vanligt vis på nätverks problem. Överväg att kontrol lera appens Server tillgänglighet från Internet
Laddar om tjänsten, återanslut | Azure SignalR-tjänsten läses in på nytt. Azure-Signaleraren stöder automatisk åter anslutning, du kan vänta tills du har anslutit om eller manuellt återansluta till Azure Signaling-tjänsten
Tillfälligt fel i intern server | Ett tillfälligt fel uppstår i Azure SignalR-tjänsten, ska återställas automatiskt
Server anslutningen har släppts | Server anslutning uppkommer med ett okänt fel, Överväg att själv felsöka med tjänst/Server/klient Side loggar först. Försök att utesluta grundläggande problem (t. ex. nätverks problem, problem med App Server-sidan och så vidare). Om problemet inte är löst kan du kontakta oss för ytterligare hjälp. Mer information finns i avsnittet [få hjälp](#get-help) . 

##### <a name="unexpected-connection-growing"></a>Oväntad växande anslutning

Det första du behöver göra är att filtrera bort extra anslutningar för att felsöka om oväntad anslutnings tillväxt. Du kan lägga till ett unikt test användar-ID till din test klient anslutning. Kontrol lera den i med diagnostikloggar, om du ser fler än en klient anslutning har samma test användar-ID eller IP-adress, är det sannolikt att klient sidan skapar och upprättar fler anslutningar än förväntat. Kontrol lera din klient sida.

#### <a name="authorization-failure"></a>Auktoriseringen misslyckades

Om du får 401 obehörigt returnerat för klient begär Anden, kontrol lera dina diagnostikloggar. Om du stöter på `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`innebär det att alla mål grupper i din åtkomsttoken är ogiltiga. Försök att använda de giltiga mål grupperna som föreslås i loggen.


#### <a name="throttling"></a>Begränsning

Om du upptäcker att du inte kan upprätta signal klient anslutningar till Azure SignalR-tjänsten, kontrollerar du diagnostikloggar. Om du stöter på `Connection count reaches limit` i Diagnostic-loggen upprättar du för många anslutningar till signal tjänsten, som når gränsen för antal anslutningar. Överväg att skala upp signal tjänsten. Om du stöter på `Message count reaches limit` i diagnostikloggar innebär det att du använder den kostnads fria nivån och använder upp meddelande kvoten. Om du vill skicka fler meddelanden bör du överväga att ändra signal tjänsten till standard nivån för att skicka ytterligare meddelanden. Mer information finns i [priser för Azure SignalR service](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Få hjälp

Vi rekommenderar att du först felsöker dig själv. De flesta problem orsakas av problem med app server eller nätverket. Följ [fel söknings guiden med diagnostisk logg](#troubleshooting-with-diagnostic-logs) och [grundläggande fel](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) söknings guide för att hitta rotor saken.
Om problemet fortfarande inte kan lösas kan du öppna ett ärende i GitHub eller skapa en biljett i Azure Portal.
Använda
1. Tidsintervall cirka 30 minuter när problemet uppstår
2. Resurs-ID för Azure SignalR-tjänsten
3. Problem information, så som det är möjligt: till exempel appserver inte att skicka meddelanden, klient anslutningarna och så vidare
4. Loggar som samlats in från Server/klient sida och annat material som kan vara användbart
5. Valfritt Återskapnings-kod

> Obs! Om du öppnar problem i GitHub bör du behålla känslig information (t. ex. resurs-ID, Server/klient loggar) privat, bara skicka till medlemmar i Microsoft-organisation privat.  
