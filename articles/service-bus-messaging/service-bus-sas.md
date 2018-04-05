---
title: Azure Service Bus-åtkomstkontroll med signaturer för delad åtkomst | Microsoft Docs
description: Översikt över åtkomstkontroll för Service Bus använder signaturer för delad åtkomst översikt, information om SAS-auktorisering med Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: sethm;clemensv
ms.openlocfilehash: 21e9e0a20842e365e40b71ac96888e7cd2056e52
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Service Bus åtkomstkontroll med signaturer för delad åtkomst

*Signaturer för delad åtkomst* (SAS) är den primära säkerhetsmekanism för Service Bus-meddelanden. Den här artikeln beskrivs SAS, hur de fungerar och hur du använder dem i ett plattformsoberoende sätt.

SAS skyddar åtkomsten till Service Bus baserat på regler för auktorisering. De är konfigurerade på ett namnområde eller en meddelandeentitet (relay, kö eller avsnittet). En auktoriseringsregel har ett namn, associeras med specifika rättigheter och har ett par kryptografiska nycklar. Du kan använda regelns namn och nyckel via Service Bus SDK eller i din kod för att generera en SAS-token. En klient kan sedan överföra token till Service Bus för att bevisa auktorisering för den begärda åtgärden.

## <a name="overview-of-sas"></a>Översikt över SAS

Signaturer för delad åtkomst är en mekanism för anspråksbaserad auktorisering med hjälp av enkla token. Med hjälp av SAS, skickas nycklar aldrig på kabeln. Nycklar används för att signera kryptografiskt information som senare kan verifieras av tjänsten. SAS kan användas liknar ett användarnamn och lösenord system där klienten har omedelbar för ett regelnamn för auktorisering och en motsvarande nyckel. SAS kan också användas liknar en federerad säkerhetsmodell där klienten får en tidsbegränsade och signerade åtkomst-token från en säkerhetstokentjänst utan någon gång kommer till tillgång signeringsnyckeln.

SAS-autentisering i Service Bus har konfigurerats med namnet [auktoriseringsregler för delad åtkomst](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) med associerade åtkomsträttigheter och ett par primära och sekundära kryptografiska nycklar. Nycklarna är 256-bitars värden i Base64-representation. Du kan konfigurera regler på namnområdesnivån i Service Bus [vidarebefordrar](service-bus-fundamentals-hybrid-solutions.md#relays), [köer](service-bus-fundamentals-hybrid-solutions.md#queues), och [avsnitt](service-bus-fundamentals-hybrid-solutions.md#topics).

Den [signatur för delad åtkomst](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) token innehåller namnet på den valda auktoriseringsregeln URI för den resurs som ska användas, giltighetstiden snabbmeddelanden, och en kryptografisk HMAC SHA256-signatur som beräknats över dessa fält med hjälp av primär eller sekundär kryptografiska nyckeln för den valda auktoriseringsregeln.

## <a name="shared-access-authorization-policies"></a>Auktoriseringsprinciper för delad åtkomst

Varje Service Bus-namnområde och varje Service Bus-entitet har en delad åtkomst auktoriseringsprincip består av regler. Principen på namnområdesnivån gäller för alla entiteter i namnområdet, oavsett deras enskilda principkonfigurationen.

För varje princip auktoriseringsregeln, besluta om tre uppgifter: **namn**, **omfång**, och **rättigheter**. Den **namn** är precis som; ett unikt namn i detta scope. Omfånget är så enkelt: det är URI: N för den aktuella resursen. För en Service Bus-namnrymd omfånget är det fullständigt kvalificerade domännamnet (FQDN) som `https://<yournamespace>.servicebus.windows.net/`.

De rättigheter som principregeln kan vara en kombination av:

* Skicka - ger rätt att skicka meddelanden till entiteten
* Lyssna - ger rätt att lyssna (relay) eller ta emot (kö, prenumerationer) och alla relaterade meddelandehantering
* ”Hantera” - ger rätt att hantera topologin för namnområdet, inklusive skapa och ta bort enheter

Höger ”hantera” innehåller ”skicka' och 'Ta emot' rättigheter.

En princip för namnområdet eller enhet som kan innehålla upp till 12 delade åtkomst auktoriseringsreglerna, för att ge plats för tre olika regler, varje täcker grundläggande rättigheter och kombinationen av Send och lyssna. Den här gränsen understrykningar som lagrar SAS-principen är inte avsedd att vara en användare eller tjänst kontoarkiv. Om ditt program måste bevilja åtkomst till Service Bus baserat på användaren eller tjänstidentiteter, bör det implementerar en säkerhetstokentjänst som utfärdar SAS-token när en kontroll av autentisering och åtkomst.

En auktoriseringsregel är tilldelad en *primärnyckel* och en *sekundärnyckeln*. Dessa är kryptografiskt starka nycklar. Inte förlorar dem eller läcka ut de - de alltid är tillgängliga i den [Azure-portalen][Azure portal]. Du kan använda någon av de genererade nycklarna och återskapa dem när som helst. Om du återskapa eller ändra en nyckel i princip utfärdade alla tidigare token baserat på nyckeln blir omedelbart ogiltig. Pågående anslutningar som skapats baserat på dessa token fortsätter dock att fungera tills token upphör att gälla.

När du skapar en Service Bus-namnrymd, en regel med namnet **RootManageSharedAccessKey** skapas automatiskt för namnområdet. Den här principen har hantera behörigheter för hela namnområdet. Vi rekommenderar att du hanterar den här regeln som en administrativ **rot** kontot och inte använda den i ditt program. Du kan skapa ytterligare regler i den **konfigurera** för namnområdet i portalen via Powershell eller Azure CLI.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfiguration för autentisering med signatur för delad åtkomst

Du kan konfigurera den [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regeln på Service Bus-namnområden, köer och ämnen. Konfigurera en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) på en Service Bus prenumeration stöds inte för närvarande, men du kan använda regler som konfigurerats på ett namnområde eller avsnittet få säker åtkomst till prenumerationer. En fungerande exempel som visar den här proceduren finns i [autentisering med delad signatur åtkomst (SAS) med Service Bus prenumerationer](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) exempel.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

I den här bilden är det *manageRuleNS*, *sendRuleNS*, och *listenRuleNS* auktoriseringsregler gäller både kön F1 och avsnittet T1, medan *listenRuleQ*  och *sendRuleQ* gäller endast för kön F1 och *sendRuleT* gäller enbart för avsnittet T1.

## <a name="generate-a-shared-access-signature-token"></a>Generera en signatur för delad åtkomst-token

Alla klienter som har åtkomst till namnet på ett regelnamn för auktorisering och en av dess Signeringsnycklar kan generera en SAS-token. Token som skapas genom att utforma en sträng i följande format:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Token upphör att gälla direkt. Heltal reflektion sekunder sedan epok `00:00:00 UTC` på 1 januari 1970 (UNIX epok) när token upphör att gälla.
* **`skn`** -Namnet på regel för auktorisering.
* **`sr`** -URI för den aktuella resursen.
* **`sig`** -Signatur.

Den `signature-string` beräknas hash-algoritmen SHA-256 över resurs-URI (**omfång** enligt beskrivningen i föregående avsnitt) och strängrepresentation token upphör att gälla direkt, avgränsade med CRLF.

Hash-beräkningen liknar följande pseudokolumner kod och returnerar ett hash-256-bitars/32-byte-värde.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token innehåller icke-hash-värden så att mottagaren kan nodernas hash med samma parametrar, verifiera att utgivaren är som har tillgång till en giltig signeringsnyckeln. 

Resurs-URI är fullständiga URI: N för Service Bus-resurs som åtkomst begärs. Till exempel `http://<namespace>.servicebus.windows.net/<entityPath>` eller `sb://<namespace>.servicebus.windows.net/<entityPath>`, dvs `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. URI: N måste vara [procent-kodade](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Delad åtkomst auktoriseringsregeln användas för signering måste konfigureras på enheten som anges av den här URI: N eller av en av dess hierarkiska överordnade. Till exempel `http://contoso.servicebus.windows.net/contosoTopics/T1` eller `http://contoso.servicebus.windows.net` i föregående exempel.

En SAS-token är giltig för alla resurser som föregås av `<resourceURI>` används i den `signature-string`.

## <a name="regenerating-keys"></a>Återskapande av nycklar

Vi rekommenderar att du regelbundet återskapar nycklarna som används i den [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt. De primära och sekundära nycklar kortplatserna finns så att du kan rotera nycklar gradvis. Om programmet använder vanligtvis den primära nyckeln, du kopiera den primära nyckeln till den sekundära nyckeln platsen och endast sedan återskapa den primära nyckeln. Värdet för nya primära nyckeln kan sedan konfigureras i klientprogram som har fortsatt åtkomst med hjälp av den gamla primära nyckeln på sekundär plats. När alla klienter har uppdaterats kan du återskapa den sekundära nyckeln för att slutligen Dra tillbaka den gamla primära nyckeln.

Om du vet eller misstänker att en nyckel manipuleras och du behöver återkalla nycklarna kan du återskapa både den [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) och [sekundär nyckel](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) av en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), ersätta dem med nya nycklar. Den här proceduren upphäver alla token som signerats med de gamla nycklarna.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Autentisering med delad Åtkomstsignatur med Service Bus

Scenarier som beskrivs nedan inkluderar konfigurationen av regler för auktorisering, generering av SAS-token och klientauktorisering.

För en fullt fungerande exempel på ett Service Bus-program som visar konfiguration och använder SAS auktorisering finns [autentisering med signatur för delad åtkomst med Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Ett relaterade exempel som visar hur SAS-auktoriseringsregler som konfigurerats på namnområden eller avsnitt för att skydda Service Bus-prenumerationer finns här: [med delade signatur åtkomst (SAS)-autentisering med Service Bus prenumerationer](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Åtkomst till delade åtkomst auktoriseringsregler för en entitet

Med Service Bus .NET Framework-bibliotek, kan du komma åt en [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt som konfigurerats på en Service Bus-kö eller ett ämne via den [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) samling i motsvarande [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) eller [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

Följande kod visar hur du lägger till auktoriseringsregler för en kö.

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

## <a name="use-shared-access-signature-authorization"></a>Använda signatur för delad åtkomst auktoriseringsregler

Program med Azure .NET SDK med Service Bus .NET-biblioteken kan använda SAS tillstånd via den [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) klass. Följande kod visar hur tokenleverantör för att skicka meddelanden till en Service Bus-kö. Alternativ för användning som visas här, du kan också skicka en tidigare utfärdade token till metoden tokenleverantör fabriken.

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

Du kan också använda tokenleverantör direkt för att utfärda token för att skicka till andra klienter. 

Anslutningssträngar kan innehålla ett namn för regeln (*SharedAccessKeyName*) och regeln nyckel (*SharedAccessKey*) eller en tidigare utfärdade token (*SharedAccessSignature*). När de befinner sig i anslutningssträngen som skickades till konstruktorn eller fabriksmetoden som accepterar en anslutningssträng, skapas och fylls i automatiskt SAS tokenleverantör.

Observera att om du vill använda SAS-auktorisering med Service Bus reläer, kan du använda SAS-nycklar som konfigurerats på Service Bus-namnrymd. Om du skapar ett relä uttryckligen på namnområdet ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) med en [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) objekt du kan ange SAS-regler för att relay. För att använda SAS-auktorisering med Service Bus-prenumerationer måste använda du SAS-nycklar som konfigurerats på en Service Bus-namnrymd eller ett ämne.

## <a name="use-the-shared-access-signature-at-http-level"></a>Använd signatur för delad åtkomst (på http-nivå)

Nu när du vet hur du skapar signaturer för delad åtkomst för alla entiteter i Service Bus är du redo att utföra en HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Kom ihåg att det fungerar för alla. Du kan skapa SAS för kön, ämnet och prenumerationen.

Om du ger en avsändare eller en SAS-token-klienten de har inte nyckeln direkt och de kan inte återställa hash för att hämta den. Som sådana har du kontroll över vad de kan komma åt och hur lång tid. En viktig sak att komma ihåg är att alla signaturer för delad åtkomst som skapats från den segmentstorlekar om du ändrar den primära nyckeln i principen.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Använd signatur för delad åtkomst (på AMQP nivå)

Du har sett hur du använder SAS-token med en HTTP POST-begäran för att skicka data till Service Bus i föregående avsnitt. Du kan komma åt Service Bus med hjälp av de avancerade Message Queuing Protocol (AMQP) som önskat protokoll ska användas av prestandaskäl i många scenarier som du vet. SAS-token användning med AMQP beskrivs i dokumentet [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) som är i utkastet sedan 2013 men väl stöds av Azure idag.

Innan du börjar skicka data till Service Bus, utgivaren måste skicka SAS-token i en AMQP-meddelande till en väldefinierad AMQP nod med namnet **$cbs** (du kan se den som en ”särskilda” kö som används av tjänsten för att hämta och verifiera alla SAS token). Utgivaren måste ange den **ReplyTo** fältet i AMQP-meddelande: det här är den nod där tjänsten svarar på utgivaren med resultatet av verifieringen av token (ett enkelt request/reply-mönster mellan utgivare och tjänsten ). Detta svar noder har skapats ”i farten”, talar om ”skapas dynamiskt fjärrnoden”, enligt beskrivningen i AMQP 1.0-specifikationen. När du har kontrollerat att SAS-token är giltig utgivaren gå framåt och börja skicka data till tjänsten.

Följande steg visar hur du skickar SAS-token med AMQP protokollet den [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) bibliotek. Detta är användbart om du inte använda den officiella Service Bus SDK (till exempel på WinRT .net Compact Framework, .net Framework Micro och Mono) utveckla C\#. Naturligtvis kan det här biblioteket är användbar för att förstå hur anspråksbaserade fungerar på AMQP-nivå som du såg hur det fungerar på HTTP-nivå (med en HTTP POST-begäran och den SAS-token som skickas i rubriken ”tillstånd”). Om du inte behöver dessa djup kunskaper om AMQP, du kan använda den officiella Service Bus SDK med .net Framework-program, vilket gör det åt dig.

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

Den `PutCbsToken()` metoden tar emot den *anslutning* (AMQP anslutning-klassinstans som tillhandahålls av den [AMQP .NET Lite biblioteket](https://github.com/Azure/amqpnetlite)) som representerar TCP-anslutningen till tjänsten och *sasToken* parameter som är SAS-token för att skicka. 

> [!NOTE]
> Det är viktigt att anslutningen har skapats med **SASL autentiseringsmekanism inställd på anonym** (och inte standard OFORMATERAD med användarnamn och lösenord som används när du inte behöver skicka SAS-token).
> 
> 

Sedan skapas två AMQP länkar för att skicka SAS-token och ta emot svar (token validering resultat) från tjänsten.

AMQP meddelandet innehåller en uppsättning egenskaper och mer än ett enkelt meddelande. SAS-token är brödtexten (med sin konstruktor). Den **”ReplyTo”** egenskap är inställd på nodnamnet för att ta emot validering resultatet på länken mottagaren (du kan ändra dess namn om du vill och den kommer att skapas dynamiskt av tjänsten). De sista tre program/anpassade egenskaperna som används av tjänsten som anger vilken typ av åtgärd som den har att köra. Enligt specifikationen CBS utkast de måste vara den **åtgärdsnamn** (”put-token”), den **typ av token** (i det här fallet en `servicebus.windows.net:sastoken`), och **”namn” på målgruppen** som token gäller (hela entitet).

När du har skickat SAS-token på länken avsändaren läsa utgivaren svaret på länken mottagare. Svaret är ett enkelt AMQP-meddelande med en program-egenskap med namnet **”statuskod”** som kan innehålla samma värden som en HTTP-statuskod.

## <a name="rights-required-for-service-bus-operations"></a>Rättigheter som krävs för Service Bus-åtgärder

I följande tabell visas de behörigheter som krävs för olika åtgärder på Service Bus-resurser.

| Åtgärd | Anspråk krävs | Anspråk omfång |
| --- | --- | --- |
| **namnområde** | | |
| Konfigurera auktoriseringsregeln för ett namnområde |Hantera |Alla adresser i namnområdet |
| **Tjänsten registret** | | |
| Räkna upp privata principer |Hantera |Alla adresser i namnområdet |
| Börja lyssna på ett namnområde |Lyssna |Alla adresser i namnområdet |
| Skicka meddelanden till en lyssnare på en namnrymd |Skicka |Alla adresser i namnområdet |
| **Kön** | | |
| Skapa en kö |Hantera |Alla adresser i namnområdet |
| Ta bort en kö |Hantera |En giltig kö-adress |
| Räkna upp köer |Hantera |$ Resurser/köer |
| Hämta en beskrivning av kön |Hantera |En giltig kö-adress |
| Konfigurera auktoriseringsregeln för en kö |Hantera |En giltig kö-adress |
| Skicka in till kön |Skicka |En giltig kö-adress |
| Ta emot meddelanden från en kö |Lyssna |En giltig kö-adress |
| Avbryt eller slutföra meddelanden när du har fått meddelandet i titt låsläge |Lyssna |En giltig kö-adress |
| Skjuta upp ett meddelande för senare hämtning |Lyssna |En giltig kö-adress |
| Systemkön ett meddelande |Lyssna |En giltig kö-adress |
| Hämta status som är kopplade till en message queue-session |Lyssna |En giltig kö-adress |
| Ange tillstånd som associeras med en message queue-session |Lyssna |En giltig kö-adress |
| Schemalägga ett meddelande för senare leverans. till exempel [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Lyssna | En giltig kö-adress
| **Avsnittet** | | |
| Skapa ett ämne |Hantera |Alla adresser i namnområdet |
| Ta bort ett ämne |Hantera |En giltig avsnittet adress |
| Räkna upp avsnitt |Hantera |$ Resurser/ämnen |
| Avsnittet beskrivning av Erhåll |Hantera |En giltig avsnittet adress |
| Konfigurera auktoriseringsregeln för ett ämne |Hantera |En giltig avsnittet adress |
| Skicka till ämnet |Skicka |En giltig avsnittet adress |
| **Prenumeration** | | |
| Skapa en prenumeration |Hantera |Alla adresser i namnområdet |
| Ta bort prenumeration |Hantera |../myTopic/subscriptions/mySubscription |
| Räkna upp prenumerationer |Hantera |..myTopic-prenumerationer |
| Hämta prenumeration beskrivning |Hantera |../myTopic/subscriptions/mySubscription |
| Avbryt eller slutföra meddelanden när du har fått meddelandet i titt låsläge |Lyssna |../myTopic/subscriptions/mySubscription |
| Skjuta upp ett meddelande för senare hämtning |Lyssna |../myTopic/subscriptions/mySubscription |
| Systemkön ett meddelande |Lyssna |../myTopic/subscriptions/mySubscription |
| Hämta status som är associerad med ett session-avsnittet |Lyssna |../myTopic/subscriptions/mySubscription |
| Ange de tillstånd som är associerad med ett session-avsnittet |Lyssna |../myTopic/subscriptions/mySubscription |
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