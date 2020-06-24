---
title: Felsöka anslutnings problem – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om fel sökning av anslutnings problem med Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/27/2020
ms.author: spelluru
ms.openlocfilehash: 4111a71200ec9bffdfed37c926635754c868f71e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84726726"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>Felsöka anslutnings problem – Azure Event Hubs
Det finns olika orsaker till att klient program inte kan ansluta till en Event Hub. De anslutnings problem som du upplever kan vara permanenta eller tillfälliga. Om problemet inträffar hela tiden (permanent) kanske du vill kontrol lera anslutnings strängen, din organisations brand Väggs inställningar, inställningar för IP-brandvägg, nätverks säkerhets inställningar (tjänst slut punkter, privata slut punkter osv.). Vid tillfälliga problem kan du uppgradera till den senaste versionen av SDK, köra kommandon för att kontrol lera ignorerade paket och hämta nätverks spår för att felsöka problemen. 

Den här artikeln innehåller tips för fel sökning av anslutnings problem med Azure Event Hubs. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>Felsök problem med permanent anslutning
Om programmet inte kan ansluta till händelsehubben alls följer du stegen i det här avsnittet för att felsöka problemet. 

### <a name="check-if-there-is-a-service-outage"></a>Kontrol lera om det finns ett tjänst avbrott
Sök efter avbrott för Azure Event Hubs-tjänsten på [status webbplatsen för Azure-tjänsten](https://azure.microsoft.com/status/).

### <a name="verify-the-connection-string"></a>Verifiera anslutnings strängen 
Kontrol lera att anslutnings strängen som du använder är korrekt. Se [Hämta anslutnings sträng](event-hubs-get-connection-string.md) för att hämta anslutnings strängen med hjälp av Azure Portal, CLI eller PowerShell. 

För Kafka-klienter kontrollerar du att producer.config-eller consumer.config-filerna är korrekt konfigurerade. Mer information finns i [skicka och ta emot meddelanden med Kafka i Event Hubs](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs).

### <a name="check-if-the-ports-required-to-communicate-with-event-hubs-are-blocked-by-organizations-firewall"></a>Kontrol lera om portarna som krävs för att kommunicera med Event Hubs blockeras av organisationens brand vägg
Kontrol lera att portarna som används vid kommunikation med Azure Event Hubs inte är blockerade i din organisations brand vägg. Se följande tabell för utgående portar som du måste öppna för att kommunicera med Azure Event Hubs. 

| Protokoll | Portar | Information | 
| -------- | ----- | ------- | 
| AMQP | 5671 och 5672 | Se [AMQP-protokoll guide](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Se [använda Event Hubs från Kafka-program](event-hubs-for-kafka-ecosystem-overview.md)

Här är ett exempel kommando som kontrollerar om 5671-porten är blockerad.

```powershell
tnc <yournamespacename>.servicebus.windows.net -port 5671
```

I Linux:

```shell
telnet <yournamespacename>.servicebus.windows.net 5671
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Kontrol lera att IP-adresser tillåts i företags brand väggen
När du arbetar med Azure måste du ibland tillåta vissa IP-adressintervall eller URL: er i företagets brand vägg eller proxy för att få åtkomst till alla Azure-tjänster som du använder eller försöker använda. Kontrol lera att trafiken tillåts på IP-adresser som används av Event Hubs. För IP-adresser som används av Azure Event Hubs: se [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519) och [service tag-EventHub](network-security.md#service-tags).

Kontrol lera också att IP-adressen för ditt namn område är tillåten. Följ dessa steg om du vill hitta rätt IP-adresser som tillåts för dina anslutningar:

1. Kör följande kommando från en kommando tolk: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anteckna IP-adressen som returnerades i `Non-authoritative answer` . Den enda tid det skulle ändra är om du återställer namn området på ett annat kluster.

Om du använder zon redundans för ditt namn område måste du utföra några ytterligare steg: 

1. Först kör du nslookup i namn området.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anteckna namnet i avsnittet **icke-auktoritativt svar** , vilket är i något av följande format: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Kör nslookup för var och en med suffix S1, S2 och S3 för att hämta IP-adresserna för alla tre instanser som körs i tre tillgänglighets zoner. 

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>Kontrol lera om programmet måste köras i ett särskilt undernät i ett virtuellt nätverk
Bekräfta att ditt program körs i ett virtuellt nätverk under nät som har åtkomst till namn området. Om det inte är det kör du programmet i under nätet som har åtkomst till namn området eller lägger till IP-adressen för den dator där programmet körs till [IP-brandväggen](event-hubs-ip-filtering.md). 

När du skapar en tjänst slut punkt för ett virtuellt nätverk för ett namn område för Event Hub accepterar namn området enbart trafik från det undernät som är kopplat till tjänstens slut punkt. Det finns ett undantag för det här beteendet. Du kan lägga till vissa IP-adresser i IP-brandväggen för att ge åtkomst till den offentliga slut punkten för Händelsehubben. Mer information finns i [nätverks tjänstens slut punkter](event-hubs-service-endpoints.md).

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>Kontrol lera inställningarna för IP-brandväggen för ditt namn område
Kontrol lera att IP-adressen för den dator där programmet körs inte har blockerats av IP-brandväggen.  

Som standard är Event Hubs-namnrymder tillgängliga från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adress intervall i CIDR-notation [(Classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

IP-brandväggens regler tillämpas på Event Hubs namn områdes nivå. Reglerna gäller därför för alla anslutningar från klienter som använder ett protokoll som stöds. Eventuella anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel på Event Hubs namn området nekas som obehörig. Svaret innehåller ingen IP-regel. IP filter regler tillämpas i ordning och den första regeln som matchar IP-adressen avgör vilken åtgärd som godkänns eller nekas.

Mer information finns i [Konfigurera IP-brandväggens regler för ett Azure Event Hubs-namnområde](event-hubs-ip-filtering.md). Information om hur du kontrollerar om det finns IP-filtrering, ett virtuellt nätverk eller problem med certifikat kedjan finns i [Felsöka nätverksrelaterade problem](#troubleshoot-network-related-issues).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Hitta IP-adresserna som blockeras av IP-brandväggen
Aktivera diagnostikloggar för [Event Hubs händelser för virtuella nätverks anslutningar](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) genom att följa anvisningarna i [Aktivera diagnostikloggar](event-hubs-diagnostic-logs.md#enable-diagnostic-logs). IP-adressen för den anslutning som nekas visas.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>Kontrol lera om namn området kan nås enbart med en privat slut punkt
Om Event Hubs namn området är konfigurerat att vara tillgängligt enbart via privat slut punkt kontrollerar du att klient programmet har åtkomst till namn området över den privata slut punkten. 

[Azure Private Link service](../private-link/private-link-overview.md) ger dig åtkomst till Azure Event Hubs över en **privat slut punkt** i det virtuella nätverket. En privat slut punkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute-eller VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta nivån av granularitet i åtkomst kontroll.

Mer information finns i [Konfigurera privata slut punkter](private-link-service.md). 

### <a name="troubleshoot-network-related-issues"></a>Felsöka nätverksrelaterade problem
Följ dessa steg om du vill felsöka nätverksrelaterade problem med Event Hubs: 

Bläddra till eller [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Det hjälper till med att kontrol lera om du har problem med IP-filtrering eller problem med virtuella nätverk eller certifikat kedjan (vanligt vis när du använder Java SDK).

Ett exempel på ett **slutfört meddelande**:

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

Ett exempel på fel **meddelande**:

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>Felsök problem med tillfälliga anslutningar
Om du upplever tillfälliga anslutnings problem går du igenom följande avsnitt för fel söknings tips. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>Använd den senaste versionen av klient-SDK: n
Några av de tillfälliga anslutnings problemen kan ha åtgärd ATS i senare versioner av SDK än vad du använder. Se till att du använder den senaste versionen av klient-SDK: er i dina program. SDK: er förbättras kontinuerligt med nya/uppdaterade funktioner och fel korrigeringar, så testa alltid med det senaste paketet. Läs viktig information om vilka problem som har åtgärd ATS och funktioner som har lagts till/uppdaterats. 

Information om klient-SDK: er finns i artikeln om [Azure Event Hubs-klient-SDK](sdks.md) : er. 

### <a name="run-the-command-to-check-dropped-packets"></a>Kör kommandot för att kontrol lera ignorerade paket
När det finns tillfälliga anslutnings problem kör du följande kommando för att kontrol lera om det finns några paket som ignoreras. Med det här kommandot görs ett försök att upprätta 25 olika TCP-anslutningar varje sekund med tjänsten. Sedan kan du kontrol lera hur många av dem som har lyckats/misslyckats och även se TCP-anslutningssträngen. Du kan hämta `psping` verktyget härifrån. [here](/sysinternals/downloads/psping)

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
Du kan använda motsvarande kommandon om du använder andra verktyg som `tnc` , `ping` och så vidare. 

Få en nätverks spårning om föregående steg inte hjälper och analyseras med verktyg som [wireshark](https://www.wireshark.org/). Kontakta [Microsoft Support](https://support.microsoft.com/) om det behövs. 

### <a name="service-upgradesrestarts"></a>Tjänst uppgraderingar/omstarter
Tillfälliga anslutnings problem kan uppstå på grund av uppgraderingar och omstarter av Server dels tjänsten. När de inträffar kan följande inträffa: 

- Det kan finnas en minskning av inkommande meddelanden/begär Anden.
- Logg filen kan innehålla fel meddelanden.
- Programmen kan vara frånkopplade från tjänsten under några sekunder.
- Begär Anden kan begränsas tillfälligt.

Om program koden använder SDK är principen för återförsök redan inbyggd och aktiv. Programmet återansluter utan betydande påverkan på programmet/arbets flödet. Annars försöker du ansluta till tjänsten igen efter några minuter för att se om problemen försvinner. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* [Felsöka problem med autentisering och auktorisering](troubleshoot-authentication-authorization.md)
