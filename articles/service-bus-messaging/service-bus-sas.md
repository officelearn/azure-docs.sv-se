---
title: "Azure Service Bus-autentisering med signaturer för delad åtkomst | Microsoft Docs"
description: "Översikt över Service Bus-autentisering med hjälp av signaturer för delad åtkomst översikt, information om SAS-autentisering med Azure Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: a2760072acb7c62204759f3ec0d3cb9899460f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-authentication-with-shared-access-signatures"></a>Service Bus-autentisering med signaturer för delad åtkomst

*Signaturer för delad åtkomst* (SAS) är den primära säkerhetsmekanism för Service Bus-meddelanden. Den här artikeln beskrivs SAS, hur de fungerar och hur du använder dem i ett plattformsoberoende sätt.

SAS-autentisering gör det möjligt för program att autentisera till Service Bus med hjälp av en åtkomstnyckel som konfigurerats på namnområdet eller meddelandeentitet (kö eller ett ämne) som specifika rättigheter associeras. Du kan sedan använda denna nyckel för att generera en SAS-token som klienter i sin tur kan använda för att autentisera till Service Bus.

Stöd för SAS-autentisering ingår i Azure SDK version 2.0 och senare.

## <a name="overview-of-sas"></a>Översikt över SAS

Signaturer för delad åtkomst är en autentiseringsmekanism baserat på SHA-256 säker hashvärden eller URI: er. SAS är en mycket kraftfull mekanism som används av alla Service Bus-tjänster. Faktiska används SAS består av två komponenter: en *delad åtkomstprincip* och en *signatur för delad åtkomst* (kallas ofta för en *token*).

SAS-autentisering i Service Bus omfattar konfiguration av en krypteringsnyckel med associerade behörigheter på en Service Bus-resurs. Klienter begär åtkomst till Service Bus-resurser genom att presentera en SAS-token. Denna token består av resurs-URI som används och en utgången signeras med den konfigurerade nyckeln.

Du kan konfigurera auktoriseringsregler för signatur för delad åtkomst på Service Bus [vidarebefordrar](service-bus-fundamentals-hybrid-solutions.md#relays), [köer](service-bus-fundamentals-hybrid-solutions.md#queues), och [avsnitt](service-bus-fundamentals-hybrid-solutions.md#topics).

SAS-autentisering används följande element:

* [Auktoriseringsregeln för delad åtkomst](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule): en 256-bitars kryptografiska primärnyckel i Base64-representation, ett valfritt sekundärnyckeln och ett nyckelnamn och associerade behörigheter (en samling *lyssna*, *skicka*, eller *hantera* rättigheter).
* [Delad Åtkomstsignatur](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) token: genereras med hjälp av HMAC-SHA256 resurssträngen, som består av URI för den resurs som används och en utgången med den kryptografiska nyckeln. Signaturen och andra element som beskrivs i följande avsnitt formateras till en sträng för att bilda SAS-token.

## <a name="shared-access-policy"></a>Princip för delad åtkomst

Ett viktigt att förstå om SAS är att det börjar med en princip. För varje princip du bestämmer dig för tre uppgifter: **namn**, **omfång**, och **behörigheter**. Den **namn** är precis som; ett unikt namn i detta scope. Omfånget är så enkelt: det är URI: N för den aktuella resursen. För en Service Bus-namnrymd omfånget är det fullständigt kvalificerade domännamnet (FQDN) som `https://<yournamespace>.servicebus.windows.net/`.

Behörigheterna för en princip är i stort sett självförklarande:

* Skicka
* Lyssna
* Hantera

När du har skapat principen tilldelas en *primärnyckel* och en *sekundärnyckeln*. Dessa är kryptografiskt starka nycklar. Inte förlorar dem eller läcka ut de - de alltid är tillgängliga i den [Azure-portalen][Azure portal]. Du kan använda någon av de genererade nycklarna och återskapa dem när som helst. Men om du återskapa eller ändra den primära nyckeln i princip blir alla signaturer för delad åtkomst som skapats från den ogiltiga.

När du skapar ett namnområde för Service Bus, skapas automatiskt en princip för hela namnområdet som kallas **RootManageSharedAccessKey**, och den här principen har alla behörigheter. Du inte logga in som **rot**, så att inte använda den här principen om det finns en mycket bra anledning. Du kan skapa ytterligare principer i den **konfigurera** för namnområdet i portalen. Det är viktigt att Observera att en enda träd i Service Bus (namnområdet, kön, etc.) bara kan ha upp till 12 principer som är kopplade till den.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfiguration för autentisering med signatur för delad åtkomst
Du kan konfigurera den [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regeln på Service Bus-namnområden, köer och ämnen. Konfigurera en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) på en Service Bus prenumeration stöds inte för närvarande, men du kan använda regler som konfigurerats på ett namnområde eller avsnittet få säker åtkomst till prenumerationer. En fungerande exempel som visar den här proceduren finns i [autentisering med delad signatur åtkomst (SAS) med Service Bus prenumerationer](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) exempel.

Maximalt 12 regler kan konfigureras på Service Bus-namnområde, kö eller i avsnittet. Regler som har konfigurerats på ett Service Bus-namnområde som gäller för alla entiteter i namnutrymmet.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

I den här bilden är det *manageRuleNS*, *sendRuleNS*, och *listenRuleNS* auktoriseringsregler gäller både kön F1 och avsnittet T1, medan *listenRuleQ*  och *sendRuleQ* gäller endast för kön F1 och *sendRuleT* gäller enbart för avsnittet T1.

De viktigaste parametrarna för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) är följande:

| Parameter | Beskrivning |
| --- | --- |
| *Nyckelnamn* |En sträng som beskriver auktoriseringsregeln. |
| *PrimaryKey* |En base64-kodad 256-bitars primär nyckel för signering och verifiera SAS-token. |
| *Sekundär nyckel* |En base64-kodad 256-bitars sekundär nyckel för signering och verifiera SAS-token. |
| *AccessRights* |En lista över åtkomsträttigheter som beviljats av auktoriseringsregeln. Dessa rättigheter kan vara en samling av lyssna, skicka och hantera rättigheter. |

När ett namnområde för Service Bus etableras en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), med [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) inställd på **RootManageSharedAccessKey**, skapas som standard.

## <a name="generate-a-shared-access-signature-token"></a>Generera en signatur för delad åtkomst (token)

Principen är inte åtkomsttoken för Service Bus. Det är det objekt som den åtkomst-token genereras - med antingen den primära och sekundära nycklarna. Alla klienter som har åtkomst till Signeringsnycklar som anges i auktoriseringsregeln för delad åtkomst kan generera SAS-token. Token som genereras av noggrant utforma en sträng i följande format:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Där `signature-string` är SHA-256-hash för omfånget för token (**omfång** enligt beskrivningen i föregående avsnitt) med en CRLF läggs och en förfallotiden (i sekunder sedan epok: `00:00:00 UTC` på 1 januari 1970). 

> [!NOTE]
> För att undvika en kort förfallotid för token, rekommenderas du att koda utgången tidsvärde som minst en 32-bitars heltal eller helst en lång (64-bitars) heltal.  
> 
> 

Hash-algoritmen liknar följande pseudokolumner kod och returnerar 32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Icke-hash-värden är i den **SharedAccessSignature** sträng så att mottagaren kan beräkna hash med samma parametrar för att kontrollera att returnera samma resultat. URI: N anger scope och nyckelnamnet identifierar principen som ska användas för att beräkna hash. Detta är viktigt ur säkerhetssynpunkt. Om signaturen inte matchar den som beräknar mottagaren (Service Bus), nekas åtkomst. Nu kan du vara säker på att avsändaren hade åtkomst till nyckeln och bör beviljas rättigheter anges i principen.

Observera att du ska använda kodade resurs-URI för den här åtgärden. Resurs-URI är fullständiga URI: N för Service Bus-resurs som åtkomst begärs. Till exempel `http://<namespace>.servicebus.windows.net/<entityPath>` eller `sb://<namespace>.servicebus.windows.net/<entityPath>`, dvs `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

Delad åtkomst auktoriseringsregeln användas för signering måste konfigureras på enheten som anges av den här URI: N eller av en av dess hierarkiska överordnade. Till exempel `http://contoso.servicebus.windows.net/contosoTopics/T1` eller `http://contoso.servicebus.windows.net` i föregående exempel.

En SAS-token är giltig för alla resurser under den `<resourceURI>` används i den `signature-string`.

Den [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) i SAS-token refererar till den **keyName** för delad åtkomst auktoriseringsregeln som används för att generera token.

Den *URL-kodade resourceURI* måste vara samma som den URI som används i strängen till logga under beräkningen av signaturen. Det bör vara [procent-kodade](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Vi rekommenderar att du regelbundet återskapar nycklarna som används i den [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt. Program som normalt används den [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) att generera en SAS-token. När du återskapar nycklarna, bör du ersätta den [sekundär nyckel](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) med den gamla primärt nyckeln och skapa en ny nyckel som den nya primärnyckeln. Detta gör att du kan fortsätta att använda token för auktorisering som har utfärdats med den gamla primära nyckeln och som ännu inte har gått ut.

Om en nyckel manipuleras och du behöver återkalla nycklarna kan du återskapa både den [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) och [sekundär nyckel](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) av en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)genom att byta ut dem med nya nycklar. Den här proceduren upphäver alla token som signerats med de gamla nycklarna.

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>Hur du använder autentisering med signatur för delad åtkomst med Service Bus

Följande scenarier är konfigurationen av regler för auktorisering, generering av SAS-token och klientauktorisering.

För en fullt fungerande exempel på ett Service Bus-program som visar konfiguration och använder SAS auktorisering finns [autentisering med signatur för delad åtkomst med Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Ett relaterade exempel som visar hur SAS-auktoriseringsregler som konfigurerats på namnområden eller avsnitt för att skydda Service Bus-prenumerationer finns här: [med delade signatur åtkomst (SAS)-autentisering med Service Bus prenumerationer](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>Åtkomst till delade åtkomst auktoriseringsregler för ett namnområde

Autentisering med datorcertifikat krävs för åtgärderna i roten för Service Bus-namnrymd. Du måste överföra ett certifikat för din Azure-prenumeration. Följ stegen för att ladda upp ett hanteringscertifikat [här](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate)med hjälp av den [Azure-portalen][Azure portal]. Mer information om Azure-hanteringscertifikat finns i [översikt över Azure certifikat](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Slutpunkten för åtkomst till auktoriseringsregler för delad åtkomst på en Service Bus-namnrymd är följande:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Så här skapar du en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt på en Service Bus-namnrymd, köra en POST-åtgärden på den här slutpunkten med information om regeln serialiserad som JSON eller XML. Exempel:

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

På liknande sätt kan använda en GET-åtgärd på slutpunkten för att läsa auktoriseringsregler som konfigurerats på namnområdet.

Uppdatera eller ta bort en specifik auktoriseringsregeln, Använd följande slutpunkt:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Åtkomst till delade åtkomst auktoriseringsregler för en entitet

Du kan komma åt en [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt som konfigurerats på en Service Bus-kö eller ett ämne via den [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) samling i den motsvarande [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) eller [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

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

Program med Azure .NET SDK med Service Bus .NET-biblioteken kan använda SAS tillstånd via den [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) klass. Följande kod visar hur tokenleverantör för att skicka meddelanden till en Service Bus-kö.

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

Program kan också använda SAS för autentisering med hjälp av en SAS-anslutningssträng i metoder som accepterar anslutningssträngar.

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

Om du ger en avsändare eller en SAS-token-klienten de har inte nyckeln direkt och de kan inte återställa hash för att hämta den. Som sådana har du kontroll över vad de kan komma åt och hur lång tid. En viktig sak att komma ihåg är att om du ändrar den primära nyckeln i princip alla signaturer för delad åtkomst som skapats från den blir ogiltiga.

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
> Det är viktigt att anslutningen har skapats med **SASL autentiseringsmekanism som har angetts till externa** (och inte standard OFORMATERAD med användarnamn och lösenord som används när du inte behöver skicka SAS-token).
> 
> 

Sedan skapas två AMQP länkar för att skicka SAS-token och ta emot svar (token validering resultat) från tjänsten.

AMQP meddelandet innehåller en uppsättning egenskaper och mer än ett enkelt meddelande. SAS-token är brödtexten (med sin konstruktor). Den **”ReplyTo”** egenskap är inställd på nodnamnet för att ta emot validering resultatet på länken mottagaren (du kan ändra dess namn om du vill och den kommer att skapas dynamiskt av tjänsten). De sista tre program/anpassade egenskaperna som används av tjänsten som anger vilken typ av åtgärd som den har att köra. Enligt specifikationen CBS utkast de måste vara den **åtgärdsnamn** (”put-token”), den **typ av token** (i det här fallet en ”servicebus.windows.net:sastoken”), och **”namn” målgruppen** som token gäller (hela entitet).

När du har skickat SAS-token på länken avsändaren läsa utgivaren svaret på länken mottagare. Svaret är ett enkelt AMQP-meddelande med en program-egenskap med namnet **”statuskod”** som kan innehålla samma värden som en HTTP-statuskod.

## <a name="rights-required-for-service-bus-operations"></a>Rättigheter som krävs för Service Bus-åtgärder

I följande tabell visas de behörigheter som krävs för olika åtgärder på Service Bus-resurser.

| Åtgärd | Anspråk krävs | Anspråk omfång |
| --- | --- | --- |
| **Namespace** | | |
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