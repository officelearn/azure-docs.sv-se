---
title: Azure Service Bus åtkomstkontroll med signaturer för delad åtkomst | Microsoft Docs
description: Översikt över Service Bus åtkomstkontroll med signaturer för delad åtkomst – översikt, information om SAS-auktorisering med Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2018
ms.author: spelluru
ms.openlocfilehash: d75f8654a33d670f107508b9e91661b1a7719755
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408563"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Service Bus åtkomstkontroll med signaturer för delad åtkomst

*Signaturer för delad åtkomst* (SAS) är den primära säkerhetsmekanism för Service Bus-meddelanden. Den här artikeln beskriver SAS, hur de fungerar och hur du använder dem i ett plattformsoberoende sätt.

SAS skyddar åtkomst till Service Bus baserat på regler. De är konfigurerade på ett namnområde eller en meddelandeentitet (relay, kön eller ämnet). En auktoriseringsregel har ett namn, kopplade till specifika rättigheter, och har ett par kryptografiska nycklar. Du kan använda regelns namn och nyckel via Service Bus SDK eller i din egen kod för att generera en SAS-token. En klient kan sedan skicka token till Service Bus bevisar auktorisering för den begärda åtgärden.

## <a name="overview-of-sas"></a>Översikt över SAS

Signaturer för delad åtkomst är en mekanism för anspråksbaserad auktorisering med enkel token. Med hjälp av SAS, skickas nycklar aldrig för anslutningen. Nycklar används kryptografiskt logga information som senare kan verifieras av tjänsten. SAS kan användas liknar ett användarnamn och lösenord schema där klienten är omedelbar tillgång ett regelnamn för auktorisering och en motsvarande nyckel. SAS kan också användas liknar en federerad säkerhetsmodell där klienten tar emot en tidsbegränsad och signerade åtkomst-token från en säkerhetstokentjänst utan att någonsin kommer till tillgång till signering nyckeln.

SAS-autentisering i Service Bus har konfigurerats med med namnet [auktoriseringsregler för delad åtkomst](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) med associerade åtkomsträttigheter och ett par med primära och sekundära kryptografiska nycklar. Nycklarna är 256-bitars värden i Base64-representation. Du kan konfigurera regler på namnområdesnivå på Service Bus [vidarebefordrar](service-bus-fundamentals-hybrid-solutions.md#relays), [köer](service-bus-fundamentals-hybrid-solutions.md#queues), och [ämnen](service-bus-fundamentals-hybrid-solutions.md#topics).

Den [signatur för delad åtkomst](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) token innehåller namnet på den valda auktoriseringsregeln URI för den resurs som ska användas, en slutar att gälla direkt, och en kryptografisk HMAC-SHA256-signatur beräknas över dessa fält med hjälp av primärt eller sekundära kryptografisk nyckel för den valda auktoriseringsregeln.

## <a name="shared-access-authorization-policies"></a>Auktoriseringsprinciper för delad åtkomst

Varje Service Bus-namnområde och varje Service Bus-entiteten har du en princip för delad åtkomst-auktorisering som består av regler. Principen på namnområdesnivå gäller för alla entiteter i namnområdet, oavsett deras enskilda principkonfigurationen.

För varje princip auktoriseringsregeln, besluta om tre uppgifter: **namn**, **omfång**, och **rights**. Den **namn** är bara att; ett unikt namn inom detta omfång. Omfånget är enkelt: det är URI: N för resursen i fråga. För ett Service Bus-namnområde omfånget är det fullständigt kvalificerade domännamnet (FQDN), till exempel `https://<yournamespace>.servicebus.windows.net/`.

De rättigheter som principregeln kan vara en kombination av:

* Skicka - ger rätt att skicka meddelanden till entiteten
* Lyssna - ger rätt att lyssna (relä) eller ta emot (kö, prenumerationer) och alla relaterade meddelandehantering
* ”Hantera” – ger rätt att hantera topologin för namnområdet, inklusive att skapa och ta bort entiteter

Rättigheten ”Hantera” omfattar rättigheter ”skicka” och ”ta emot”.

En princip för namnområde eller entiteten kan innehålla upp till 12 delad åtkomst auktoriseringsreglerna, för att tillhandahålla utrymme för tre regeluppsättningar, var och en som täcker grundläggande rättigheter och kombinationen av skicka och att den lyssnar. Den här gränsen understrykningar som lagrar SAS-princip är inte avsedd att vara en användare eller kontoarkiv för tjänsten. Om programmet behöver för att bevilja åtkomst till Service Bus baserat på användar- eller tjänstidentiteter, bör det implementerar en säkerhetstokentjänst som utfärdar SAS-token efter en autentisering och kontroll.

En auktoriseringsregel är tilldelad en *primärnyckel* och en *sekundärnyckel*. Det här är kryptografiskt starka nycklar. Inte förlora dem eller läcka dem – de alltid är tillgängliga i den [Azure-portalen][Azure portal]. Du kan använda någon av de genererade nycklarna och du kan återskapa dem när som helst. Om du återskapa eller ändra en nyckel i principen för utfärdade alla tidigare token baserat på nyckeln blir omedelbart ogiltig. Pågående anslutningar som skapats utifrån dessa token kommer dock fortsätta att fungera tills token upphör att gälla.

När du skapar ett Service Bus-namnområde, en regel med namnet **RootManageSharedAccessKey** skapas automatiskt för namnområdet. Den här principen har hantera behörigheter för hela namnområdet. Vi rekommenderar att du ska hantera den här regeln som en administrativ **rot** och inte använda det i ditt program. Du kan skapa ytterligare hanteringsprincipregler (MPR) i den **konfigurera** fliken för namnområde i portalen via Powershell eller Azure CLI.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfiguration för autentisering med signatur för delad åtkomst

Du kan konfigurera den [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regel i Service Bus-namnområden, köer eller ämnen. Konfigurera en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) på en Service Bus prenumeration stöds inte för närvarande, men du kan använda regler som konfigureras i ett namnområde eller avsnittet för att skydda åtkomsten till prenumerationer. Ett fungerande exempel som illustrerar den här proceduren, finns det [autentisering med signatur för delad åtkomst (SAS) med Service Bus-prenumerationer](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) exemplet.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

I den här bilden i *manageRuleNS*, *sendRuleNS*, och *listenRuleNS* auktoriseringsregler gäller både kön Q1 och avsnittet T1, medan *listenRuleQ*  och *sendRuleQ* gäller endast för kön Q1 och *sendRuleT* gäller enbart för avsnittet T1.

## <a name="generate-a-shared-access-signature-token"></a>Skapa en signatur för delad åtkomst-token

Alla klienter som har åtkomst till namnet på ett regelnamn för auktorisering och en av dess Signeringsnycklar kan generera en SAS-token. Token skapas genom att utforma en sträng i följande format:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** – Token förfaller direkt. Heltal som återspeglar sekunder sedan epok `00:00:00 UTC` på 1 januari 1970 (UNIX epoch) när token upphör att gälla.
* **`skn`** – Namnet på regel för auktorisering.
* **`sr`** -URI för den resursen ifråga.
* **`sig`** -Signatur.

Den `signature-string` beräknas SHA-256-hash över resurs-URI (**omfång** enligt beskrivningen i föregående avsnitt) och strängrepresentation token upphör att gälla direkt, avgränsade med CRLF.

Hash-beräkningen liknar följande pseudokod och returnerar en 256-bitars/32-bytes hash-värde.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token innehåller icke-hash-värden så att mottagaren kan omberäkna hash med samma parametrar, verifiera att utfärdaren är en giltig signeringsnyckel tillgång. 

Resurs-URI är den fullständiga URI för Service Bus-resurs som åtkomst begärs. Till exempel `http://<namespace>.servicebus.windows.net/<entityPath>` eller `sb://<namespace>.servicebus.windows.net/<entityPath>`, det vill säga `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. URI: N måste vara [procentkodad](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Auktoriseringsregeln för delad åtkomst som används för att signera måste konfigureras på enheten som anges av den här URI: N eller genom att ett hierarkisk överordnat objekt. Till exempel `http://contoso.servicebus.windows.net/contosoTopics/T1` eller `http://contoso.servicebus.windows.net` i föregående exempel.

En SAS-token är giltig för alla resurser som föregås av den `<resourceURI>` används i den `signature-string`.

## <a name="regenerating-keys"></a>Återskapar nycklar

Vi rekommenderar att du regelbundet återskapar nycklarna som används i den [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt. De primära och sekundära nyckeln fack finns så att du kan rotera nycklar gradvis. Om programmet använder vanligtvis den primära nyckeln, kan du kopiera den primära nyckeln till det sekundära nyckeln facket och endast sedan återskapa den primära nyckeln. Ny primärnyckelvärdet kan sedan konfigureras i klientprogram som ha fortsatt åtkomst med hjälp av den gamla primära nyckeln på den sekundära platsen. När alla klienter har uppdaterats kan återskapa du den sekundära nyckeln för att slutligen Dra tillbaka den gamla primära nyckeln.

Om du vet eller misstänker att en nyckel har komprometterats och du behöver återkalla nycklarna kan du återskapa både den [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) och [sekundär nyckel](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) av en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), ersätta dem med nya nycklar. Den här proceduren upphäver alla token som signerats med de gamla nycklarna.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Autentisering med delad Åtkomstsignatur med Service Bus

De scenarier som beskrivs nedan omfattar konfiguration av auktoriseringsregler, generering av SAS-token och klientautentisering.

För en fullständig fungerande exempel på ett Service Bus-program som visar konfiguration och använder SAS auktorisering finns i [autentisering med signatur för delad åtkomst med Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Ett relaterade exempel som illustrerar användningen av SAS-auktoriseringsregler som konfigureras i namnområden eller avsnitt för att skydda Service Bus-prenumerationer finns här: [autentisering med signatur för delad åtkomst (SAS) med Service Bus-prenumerationer](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Åtkomstregler för auktorisering för delad åtkomst på en entitet

Med Service Bus .NET Framework-bibliotek, kan du komma åt en [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektet konfigureras i ett Service Bus-kö eller ämne via den [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) samling i motsvarande [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) eller [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

Följande kod visar hur du lägger till regler för en kö.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Använda auktorisering med signatur för delad åtkomst

Program med hjälp av Azure .NET SDK med Service Bus .NET-biblioteken kan använda SAS-auktorisering via den [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) klass. Följande kod visar användningen av Tokenleverantören för att skicka meddelanden till en Service Bus-kö. Alternativ till den användning som visas här, och du kan även skicka en tidigare utfärdade token till standardmetoden tokenleverantör.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Du kan också använda Tokenleverantören direkt för att utfärda token ska skickas till andra klienter. 

Anslutningssträngar kan innehålla ett Regelnamn (*SharedAccessKeyName*) och regeln nyckel (*SharedAccessKey*) eller en tidigare utfärdade token (*SharedAccessSignature*). När de befinner sig i anslutningssträngen som skickades till konstruktorn eller standardmetod som tar emot en anslutningssträng, skapas och fylls i automatiskt den SAS-Tokenleverantören.

Observera att om du vill använda SAS-auktorisering med Service Bus-reläer, kan du använda SAS-nycklar som konfigurerats på Service Bus-namnområdet. Om du uttryckligen skapar ett relä på namnområdet ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) med en [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) objekt kan du kan ange SAS-regler för relay. Om du vill använda SAS-auktorisering med Service Bus-prenumerationer, kan du använda SAS-nycklar som konfigurerats på ett Service Bus-namnområde eller på ett ämne.

## <a name="use-the-shared-access-signature-at-http-level"></a>Använda signatur för delad åtkomst (på HTTP-nivå)

Nu när du vet hur du skapar signaturer för delad åtkomst för alla entiteter i Service Bus är du redo att utföra en HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Kom ihåg att det här fungerar för allt. Du kan skapa SAS för en kö, ett ämne eller en prenumeration.

Om du ger en avsändare eller klienten en SAS-token kan de har inte nyckeln direkt och de kan inte ångra hash om du vill ha den. Därför måste har du kontroll över vad de kan komma åt, och hur länge. En viktig sak att komma ihåg är att om du ändrar den primära nyckeln i principen, ogiltigförklaras alla signaturer för delad åtkomst som skapats från den.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Använda signatur för delad åtkomst (på AMQP nivå)

I föregående avsnitt såg du hur du använder SAS-token med en HTTP POST-begäran för att skicka data till Service Bus. Du kan komma åt Service Bus med hjälp av AMQP Advanced Message Queuing Protocol () som är det prioriterade protokollet som ska användas av prestandaskäl, i många scenarier som du vet. SAS-token användningen med AMQP beskrivs i dokumentet [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) det vill säga i utkastet sedan 2013 men väl stöds av Azure i dag.

Innan du börjar skicka data till Service Bus, måste utgivaren skicka SAS-token i en AMQP-meddelande till en väldefinierad AMQP-nod med namnet **$cbs** (du kan se det som en ”särskilda” kö som används av tjänsten för att skaffa och verifiera alla SAS token). Utgivare måste ange den **ReplyTo** fältet i AMQP-meddelande, vilket noden där tjänsten svarar till utgivaren med resultatet från tokenvalidering (en enkel begäran/svar-mönster mellan utgivare och service ). Den här svars-noden har skapats ”i farten”, pratar om ”skapas dynamiskt fjärrnoden” enligt AMQP 1.0-specifikationen. När du har kontrollerat att SAS-token är giltig, utgivaren gå framåt och börja skicka data till tjänsten.

Följande steg visar hur du skickar SAS-token med AMQP-protokollet med den [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) biblioteket. Detta är användbart om du inte kan använda de officiella Service Bus SDK (till exempel på WinRT, .net Compact Framework, .net Framework Micro och Mono) utveckla i C\#. Naturligtvis kan det här biblioteket är användbart för att förstå hur anspråksbaserade fungerar på AMQP-nivå som du såg hur det fungerar på HTTP-nivå (med en HTTP POST-begäran och den SAS-token som skickas i rubriken ”Authorization”). Om du inte behöver sådan djupa kunskaper om AMQP, du kan använda de officiella Service Bus SDK med .net Framework-program, vilket gör det åt dig.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

Den `PutCbsToken()` metoden tar emot den *anslutning* (AMQP-anslutning-klassinstans som tillhandahålls av den [AMQP .NET Lite biblioteket](https://github.com/Azure/amqpnetlite)) som representerar TCP-anslutningen till tjänsten och *sasToken* parameter som är SAS-token för att skicka. 

> [!NOTE]
> Det är viktigt att anslutningen har skapats med **SASL-autentiseringsmekanismen inställd på anonym** (och inte standard OFORMATERAD med användarnamn och lösenord som användes när du inte behöver skicka SAS-token).
> 
> 

Sedan skapas två AMQP länkar för att skicka SAS-token och ta emot svaret (tokenvalidering-resultat) från tjänsten.

AMQP-meddelandet innehåller en uppsättning egenskaper och mer än en enkel meddelande. SAS-token är brödtexten i meddelandet (med dess konstruktorn). Den **”ReplyTo”** egenskapen till namnet på noden för att ta emot valideringsresultat på länken mottagare (du kan ändra namnet om du vill och den kommer att skapas dynamiskt av tjänsten). De senaste tre program/anpassade egenskaperna som används av tjänsten som anger vilken typ av åtgärd som den har att köra. Enligt beskrivningen i CBS draft specifikationen, de måste vara den **Åtgärdsnamnet** (”put-token”), den **typ av token** (i det här fallet en `servicebus.windows.net:sastoken`), och **”namn” publik** som token gäller (hela entitet).

När du har skickat SAS-token på länken avsändaren, måste utgivaren läsa svar på länken mottagare. Svaret är en enkel AMQP-meddelande med en programegenskap med namnet **”statuskoden”** som kan innehålla samma värden som en HTTP-statuskod.

## <a name="rights-required-for-service-bus-operations"></a>Behörighet som krävs för Service Bus-åtgärder

I följande tabell visas de behörigheter som krävs för olika åtgärder på Service Bus-resurser.

| Åtgärd | Anspråk som krävs | Anspråk omfång |
| --- | --- | --- |
| **Namespace** | | |
| Konfigurera auktoriseringsregeln för ett namnområde |Hantera |Alla adresser för namnområde |
| **Tjänstregister** | | |
| Räkna upp principer som privat |Hantera |Alla adresser för namnområde |
| Tar emot ett namnområde |Lyssna |Alla adresser för namnområde |
| Skicka meddelanden till en lyssnare på ett namnområde |Skicka |Alla adresser för namnområde |
| **kön** | | |
| Skapa en kö |Hantera |Alla adresser för namnområde |
| Ta bort en kö |Hantera |En giltig kö-adress |
| Räkna upp köer |Hantera |$ Resurser/köer |
| Hämta köbeskrivningen |Hantera |En giltig kö-adress |
| Konfigurera auktoriseringsregeln för en kö |Hantera |En giltig kö-adress |
| Skicka in till kön |Skicka |En giltig kö-adress |
| Ta emot meddelanden från en kö |Lyssna |En giltig kö-adress |
| Lämna eller Slutför meddelanden när du har fått meddelandet i peek-lock-läge |Lyssna |En giltig kö-adress |
| Skjut upp ett meddelande för senare hämtning |Lyssna |En giltig kö-adress |
| Obeställbara ett meddelande |Lyssna |En giltig kö-adress |
| Hämta parametrarnas tillstånd som associeras med en message queue-session |Lyssna |En giltig kö-adress |
| Ställa in tillståndet som är associerade med en message queue-session |Lyssna |En giltig kö-adress |
| Schemalägga ett meddelande för senare leverans. till exempel [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Lyssna | En giltig kö-adress
| **Avsnittet** | | |
| Skapa ett ämne |Hantera |Alla adresser för namnområde |
| Ta bort ett ämne |Hantera |En giltig avsnittet adress |
| Räkna upp avsnitt |Hantera |$ Resurser/ämnen |
| Hämta avsnittet beskrivning |Hantera |En giltig avsnittet adress |
| Konfigurera auktoriseringsregeln för ett ämne |Hantera |En giltig avsnittet adress |
| Skicka till ämnet |Skicka |En giltig avsnittet adress |
| **Prenumeration** | | |
| Skapa en prenumeration |Hantera |Alla adresser för namnområde |
| Ta bort prenumeration |Hantera |../myTopic/subscriptions/mySubscription |
| Räkna upp prenumerationer |Hantera |..myTopic-prenumerationer |
| Prenumeration beskrivning av Erhåll |Hantera |../myTopic/subscriptions/mySubscription |
| Lämna eller Slutför meddelanden när du har fått meddelandet i peek-lock-läge |Lyssna |../myTopic/subscriptions/mySubscription |
| Skjut upp ett meddelande för senare hämtning |Lyssna |../myTopic/subscriptions/mySubscription |
| Obeställbara ett meddelande |Lyssna |../myTopic/subscriptions/mySubscription |
| Hämta parametrarnas tillstånd som associeras med ett session-avsnittet |Lyssna |../myTopic/subscriptions/mySubscription |
| Ställa in tillståndet som är associerade med ett session-avsnittet |Lyssna |../myTopic/subscriptions/mySubscription |
| **Regler** | | |
| Skapa en regel |Hantera |../myTopic/subscriptions/mySubscription |
| Ta bort en regel |Hantera |../myTopic/subscriptions/mySubscription |
| Räkna upp regler |Hantera eller lyssna |../myTopic/subscriptions/mySubscription/rules 

## <a name="next-steps"></a>Nästa steg

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Använd Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com