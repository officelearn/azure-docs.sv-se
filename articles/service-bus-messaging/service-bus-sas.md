---
title: Azure Service Bus-åtkomstkontroll med signaturer för delad åtkomst
description: Översikt över servicebussåtkomstkontroll med hjälp av översikt över signaturer för delad åtkomst, information om SAS-auktorisering med Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: c381d9413c4003bc2ab9a9357ff2769e84d14c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259479"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Åtkomstkontroll för servicebuss med signaturer för delad åtkomst

*SAS (Shared Access Signatures)* är den primära säkerhetsmekanismen för Service Bus-meddelanden. I den här artikeln beskrivs SAS, hur de fungerar och hur du använder dem på ett plattformsoberoende sätt.

SAS-vakter har tillgång till Service Bus baserat på tillståndsregler. De konfigureras antingen på ett namnområde eller en meddelandeentitet (relä, kö eller ämne). En auktoriseringsregel har ett namn, är associerad med specifika rättigheter och har ett par kryptografiska nycklar. Du använder regelns namn och nyckel via Service Bus SDK eller i din egen kod för att generera en SAS-token. En klient kan sedan skicka token till Service Bus för att bevisa auktorisering för den begärda åtgärden.

> [!NOTE]
> Azure Service Bus stöder auktorisera åtkomst till ett servicebussnamnområde och dess entiteter med Azure Active Directory (Azure AD). Att auktorisera användare eller program med OAuth 2.0-token som returneras av Azure AD ger överlägsen säkerhet och användarvänlighet över SIGNATURER för delad åtkomst (SAS). Med Azure AD behöver du inte lagra token i koden och riskera potentiella säkerhetsproblem.
>
> Microsoft rekommenderar att du använder Azure AD med dina Azure Service Bus-program när det är möjligt. Mer information finns i följande artiklar:
> - [Autentisera och auktorisera ett program med Azure Active Directory för åtkomst till Azure Service Bus-entiteter](authenticate-application.md).
> - [Autentisera en hanterad identitet med Azure Active Directory för att komma åt Azure Service Bus-resurser](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Översikt över SAS

Signaturer för delad åtkomst är en anspråksbaserad auktoriseringsmekanism med enkla token. Med HJÄLP AV SAS skickas aldrig nycklar på tråden. Nycklar används för att kryptografiskt signera information som senare kan verifieras av tjänsten. SAS kan användas på samma sätt som ett användarnamns- och lösenordsschema där klienten omedelbart har ett auktoriseringsregelnamn och en matchande nyckel. SAS kan också användas på samma sätt som en federerad säkerhetsmodell, där klienten tar emot en tidsbegränsad och signerad åtkomsttoken från en säkerhetstokentjänst utan att någonsin komma i besittning av signeringsnyckeln.

SAS-autentisering i Service Bus har konfigurerats med [namngivna behörighetsregler för delad åtkomst](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) som har associerade åtkomsträttigheter och ett par primära och sekundära kryptografiska nycklar. Tangenterna är 256-bitarsvärden i Base64-representationen. Du kan konfigurera regler på namnområdesnivå, på Service [Bus-reläer,](../service-bus-relay/relay-what-is-it.md) [köer](service-bus-messaging-overview.md#queues)och [ämnen](service-bus-messaging-overview.md#topics).

Signaturtoken för [delad åtkomst](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) innehåller namnet på den valda auktoriseringsregeln, URI-för den resurs som ska nås, ett utgångsfritt ögonblick och en kryptografisk HMAC-SHA256-signatur som beräknas över dessa fält med hjälp av antingen den primära eller sekundära kryptografiska nyckeln för den valda auktoriseringsregeln.

## <a name="shared-access-authorization-policies"></a>Behörighetsprinciper för delad åtkomst

Varje servicebussnamnområde och varje Service Bus-enhet har en princip för auktorisering av delad åtkomst som består av regler. Principen på namnområdesnivå gäller för alla entiteter i namnområdet, oavsett deras individuella principkonfiguration.

För varje behörighetsprincipregel bestämmer du dig för tre informationsdelar: **namn,** **omfattning**och **rättigheter**. **Namnet** är just det; ett unikt namn inom detta scope. Omfattningen är lätt nog: det är URI för resursen i fråga. För ett Service Bus-namnområde är scopet det fullständigt kvalificerade domännamnet (FQDN), till exempel `https://<yournamespace>.servicebus.windows.net/`.

De rättigheter som följer av policyregeln kan vara en kombination av:

* "Skicka" - Ger rätt att skicka meddelanden till enheten
* "Lyssna" - Ger rätt att lyssna (relä) eller ta emot (kö, prenumerationer) och all relaterad meddelandehantering
* "Hantera" - Ger rätt att hantera namnområdets topologi, inklusive att skapa och ta bort entiteter

Rättigheten Hantera innehåller rättigheterna "Skicka" och "Ta emot".

En namnområdes- eller entitetsprincip kan innehålla upp till 12 regler för auktorisering av delad åtkomst, vilket ger utrymme för tre uppsättningar regler, var och en som täcker de grundläggande rättigheterna och kombinationen av Skicka och lyssna. Den här gränsen understryker att SAS-principarkivet inte är avsett att vara ett användar- eller tjänstkontoarkiv. Om ditt program behöver bevilja åtkomst till Service Bus baserat på användar- eller tjänstidentiteter bör det implementera en säkerhetstokentjänst som utfärdar SAS-token efter en autentiserings- och åtkomstkontroll.

En auktoriseringsregel tilldelas en *primärnyckel* och en *sekundär nyckel*. Dessa är kryptografiskt starka nycklar. Tappa inte bort dem eller läcka dem - de kommer alltid att vara tillgängliga i [Azure-portalen][Azure portal]. Du kan använda någon av de genererade nycklarna och du kan återskapa dem när som helst. Om du återskapar eller ändrar en nyckel i principen blir alla tidigare utfärdade token baserat på nyckeln omedelbart ogiltiga. Pågående anslutningar som skapas baserat på sådana token kommer dock att fortsätta att fungera tills token upphör att gälla.

När du skapar ett servicebussnamnområde skapas en principregel med namnet **RootManageSharedAccessKey** automatiskt för namnområdet. Den här principen har hantera behörigheter för hela namnområdet. Vi rekommenderar att du behandlar den här regeln som ett administrativt **rotkonto** och inte använder den i ditt program. Du kan skapa ytterligare principregler på fliken **Konfigurera** för namnområdet i portalen, via Powershell eller Azure CLI.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfiguration för autentisering av signatur för delad åtkomst

Du kan konfigurera regeln [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) för servicebussnamnområden, köer eller ämnen. Det går inte att konfigurera en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) på en Service Bus-prenumeration, men du kan använda regler som konfigurerats för ett namnområde eller ämne för att skydda åtkomst till prenumerationer. Ett arbetsexempel som illustrerar den här proceduren finns i [autentiseringen Använda SIGNATURE för delad åtkomst (SAS) med Service Bus-prenumerationer.](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)

![Sas](./media/service-bus-sas/service-bus-namespace.png)

I den här figuren gäller *manageRuleNS,* *sendRuleNS*och *listenRuleNS-auktoriseringsregler* för både kö Q1 och ämne T1, medan *listenRuleQ* och *sendRuleQ* gäller endast för kö Q1 och *sendRuleT* gäller endast för ämne T1.

## <a name="generate-a-shared-access-signature-token"></a>Generera en signaturtoken för delad åtkomst

Alla klienter som har åtkomst till namnet på ett auktoriseringsregelnamn och en av dess signeringsnycklar kan generera en SAS-token. Token genereras genom att skapa en sträng i följande format:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`**- Token utgång ögonblick. Heltalsreflekterande sekunder `00:00:00 UTC` sedan epoken den 1 januari 1970 (UNIX-epok) när token upphör att gälla.
* **`skn`**- Tillståndsregelns namn.
* **`sr`**- URI för den resurs som används.
* **`sig`**- Underskrift.

Den `signature-string` är SHA-256 hash beräknas över resursen URI **(omfång** som beskrivs i föregående avsnitt) och strängrepresentation av token utgångsdatum ögonblick, åtskilda av LF.

Hash-beräkningen liknar följande pseudokod och returnerar ett hash-värde på 256 bitar/32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token innehåller icke-hash-värden så att mottagaren kan återkompensera hash-värdet med samma parametrar och kontrollera att utfärdaren har en giltig signeringsnyckel.

Resurs-URI är den fullständiga URI-resursen för servicebussresursen som åtkomsten begärs till. Till `http://<namespace>.servicebus.windows.net/<entityPath>` exempel, `sb://<namespace>.servicebus.windows.net/<entityPath>`eller ; det vill `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`än, . 

**URI:n måste vara [procentkodad](https://msdn.microsoft.com/library/4fkewx0t.aspx).**

Regeln för delad åtkomstauktorisering som används för signering måste konfigureras på den entitet som anges av den här URI:n eller av en av dess hierarkiska överordnade. Till `http://contoso.servicebus.windows.net/contosoTopics/T1` exempel, `http://contoso.servicebus.windows.net` eller i föregående exempel.

En SAS-token är giltig för alla `<resourceURI>` resurser `signature-string`som föregås av den som används i .

## <a name="regenerating-keys"></a>Återskapa nycklar

Vi rekommenderar att du regelbundet återskapar nycklarna som används i [SharedAccessAuthorizationRule-objektet.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) Det finns primära och sekundära nyckelfack så att du kan rotera tangenterna gradvis. Om programmet vanligtvis använder primärnyckeln kan du kopiera primärnyckeln till den sekundära nyckelplatsen och först därefter återskapa primärnyckeln. Det nya primärnyckelvärdet kan sedan konfigureras till klientprogrammen, som har fortsatt åtkomst med den gamla primärnyckeln i den sekundära platsen. När alla klienter har uppdaterats kan du återskapa den sekundära nyckeln för att slutligen dra tillbaka den gamla primärnyckeln.

Om du känner till eller misstänker att en nyckel har komprometterats och du måste återkalla nycklarna kan du återskapa både [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) och [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)och ersätta dem med nya nycklar. Den här proceduren ogiltigförklarar alla token som signerats med de gamla nycklarna.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Autentisering av delad åtkomstsignatur med Service Bus

Scenarierna som beskrivs på följande sätt inkluderar konfiguration av auktoriseringsregler, generering av SAS-token och klientauktorisering.

Ett fullständigt fungerande exempel på ett Service Bus-program som illustrerar konfigurationen och använder SAS-auktorisering finns i [Autentisering av delad åtkomstsignatur med Service Bus](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Ett relaterat exempel som illustrerar användningen av SAS-auktoriseringsregler som konfigurerats på namnområden eller ämnen för att skydda Service Bus-prenumerationer finns här: [Använda SAS-autentisering (Shared Access Signature) med Service Bus-prenumerationer](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Åtkomst till regler för auktorisering av delad åtkomst på en entitet

Med Service Bus .NET Framework-bibliotek kan du komma åt ett [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule-objekt](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) som konfigurerats i en tjänstbusskö eller ett ämne via [samlingen AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) i motsvarande [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) eller [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

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

## <a name="use-shared-access-signature-authorization"></a>Använda auktorisering för delad åtkomstsignatur

Program som använder Azure .NET SDK med Service Bus .NET-biblioteken kan använda SAS-auktorisering via klassen [SharedAccessSignatureTokenProvider.](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) Följande kod illustrerar användningen av tokenprovidern för att skicka meddelanden till en servicebusskö. Alternativ till den användning som visas här, kan du också skicka en tidigare utfärdad token till tokenproviderns fabriksmetod.

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

Du kan också använda tokenprovidern direkt för att utfärda token för att skicka till andra klienter.

Anslutningssträngar kan innehålla ett regelnamn (*SharedAccessKeyName*) och regelnyckel (*SharedAccessKey*) eller en tidigare utfärdad token (*SharedAccessSignature*). När de finns i anslutningssträngen som skickas till en konstruktor eller fabriksmetod som accepterar en anslutningssträng skapas och fylls SAS-tokenprovidern automatiskt och fylls i.

Observera att om du vill använda SAS-auktorisering med Service Bus-reläer kan du använda SAS-nycklar som konfigurerats på servicebussens namnområde. Om du uttryckligen skapar ett relä på namnområdet[(NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) med ett [RelayDescription)](/dotnet/api/microsoft.servicebus.messaging.relaydescription)objekt, kan du ställa in SAS-reglerna bara för det reläet. Om du vill använda SAS-auktorisering med Service Bus-prenumerationer kan du använda SAS-nycklar som konfigurerats på ett servicebussnamnområde eller i ett ämne.

## <a name="use-the-shared-access-signature-at-http-level"></a>Använda signaturen för delad åtkomst (på HTTP-nivå)

Nu när du vet hur du skapar signaturer för delad åtkomst för alla entiteter i Service Bus är du redo att utföra ett HTTP-INLÄGG:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Kom ihåg att detta fungerar för allt. Du kan skapa SAS för en kö, ett ämne eller en prenumeration.

Om du ger en avsändare eller klient en SAS-token har de inte nyckeln direkt och de kan inte vända hash för att hämta den. Som sådan har du kontroll över vad de kan komma åt, och hur länge. En viktig sak att komma ihåg är att om du ändrar primärnyckeln i principen, alla signaturer för delad åtkomst som skapats från den är ogiltiga.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Använda signaturen för delad åtkomst (på AMQP-nivå)

I föregående avsnitt såg du hur du använder SAS-token med en HTTP POST-begäran om att skicka data till Service Bus. Som du vet kan du komma åt Service Bus med hjälp av AMQP (Advanced Message Queuing Protocol) som är det protokoll som föredras av prestandaskäl, i många scenarier. SAS-tokenanvändningen med AMQP beskrivs i dokumentet [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) som är i arbetsutkast sedan 2013 men som stöds väl av Azure idag.

Innan du börjar skicka data till Service Bus måste utgivaren skicka SAS-token i ett AMQP-meddelande till en väldefinierad AMQP-nod med namnet **$cbs** (du kan se den som en "speciell" kö som används av tjänsten för att hämta och validera alla SAS-token). Utgivaren måste ange fältet **ReplyTo** i AMQP-meddelandet. Detta är den nod där tjänsten svarar på utgivaren med resultatet av tokenvalidering (ett enkelt mönster för begäran/svar mellan utgivare och tjänst). Den här svarsnoden skapas "i farten", på tal om "dynamisk skapande av fjärrnod" enligt beskrivningen i AMQP 1.0-specifikationen. När du har kontrollerat att SAS-token är giltig kan utgivaren gå vidare och börja skicka data till tjänsten.

Följande steg visar hur du skickar SAS-token med AMQP-protokollet med hjälp av [AMQP.NET Lite-biblioteket.](https://github.com/Azure/amqpnetlite) Detta är användbart om du inte kan använda den officiella Service Bus SDK (till exempel på WinRT, .NET Compact Framework, .NET Micro Framework och Mono) utvecklas i C\#. Naturligtvis är det här biblioteket användbart för att förstå hur anspråksbaserad säkerhet fungerar på AMQP-nivå, som du såg hur det fungerar på HTTP-nivå (med en HTTP POST-begäran och SAS-token som skickas i "Auktorisering" huvudet). Om du inte behöver så djup kunskap om AMQP kan du använda den officiella Service Bus SDK med .NET Framework-program, som gör det åt dig.

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

Metoden `PutCbsToken()` tar emot *anslutningen* (AMQP-anslutningsklassinstansen enligt [AMQP .NET Lite-biblioteket)](https://github.com/Azure/amqpnetlite)som representerar TCP-anslutningen till tjänsten och parametern *sasToken* som är SAS-token att skicka.

> [!NOTE]
> Det är viktigt att anslutningen skapas med **SASL-autentiseringsmekanism inställd på ANONYMOUS** (och inte standard-PLAIN med användarnamn och lösenord som används när du inte behöver skicka SAS-token).
>
>

Därefter skapar utgivaren två AMQP-länkar för att skicka SAS-token och ta emot svaret (tokenvalideringsresultatet) från tjänsten.

AMQP-meddelandet innehåller en uppsättning egenskaper och mer information än ett enkelt meddelande. SAS-token är meddelandets brödtext (med hjälp av dess konstruktor). Egenskapen **"ReplyTo"** är inställd på nodnamnet för att ta emot valideringsresultatet på mottagarlänken (du kan ändra dess namn om du vill och det skapas dynamiskt av tjänsten). De senaste tre program/anpassade egenskaperna används av tjänsten för att ange vilken typ av åtgärd den har att utföra. Som beskrivs i CBS utkast specifikation, måste de vara **operationens namn** ("put-token"), **typ av token** (i det här fallet a `servicebus.windows.net:sastoken`), och **"namn" för den målgrupp** som token gäller (hela entiteten).

När du har skickat SAS-token på avsändarlänken måste utgivaren läsa svaret på mottagarlänken. Svaret är ett enkelt AMQP-meddelande med en programegenskap med namnet **"statuskod"** som kan innehålla samma värden som en HTTP-statuskod.

## <a name="rights-required-for-service-bus-operations"></a>Rättigheter som krävs för servicebussverksamhet

I följande tabell visas de åtkomsträttigheter som krävs för olika åtgärder på Service Bus-resurser.

| Åtgärd | Anspråk krävs | Anspråksscope |
| --- | --- | --- |
| **Namespace** | | |
| Konfigurera auktoriseringsregel för ett namnområde |Hantera |Vilken namnområdesadress som helst |
| **Tjänstregistret** | | |
| Räkna upp privata policyer |Hantera |Vilken namnområdesadress som helst |
| Börja lyssna på ett namnområde |Lyssna |Vilken namnområdesadress som helst |
| Skicka meddelanden till en lyssnare på ett namnområde |Skicka |Vilken namnområdesadress som helst |
| **Kö** | | |
| Skapa en kö |Hantera |Vilken namnområdesadress som helst |
| Ta bort en kö |Hantera |En giltig köadress |
| Räkna upp köer |Hantera |/$Resources/Köer |
| Hämta köbeskrivningen |Hantera |En giltig köadress |
| Konfigurera auktoriseringsregel för en kö |Hantera |En giltig köadress |
| Skicka in till kön |Skicka |En giltig köadress |
| Ta emot meddelanden från en kö |Lyssna |En giltig köadress |
| Överge eller slutföra meddelanden efter att ha tagit emot meddelandet i peek-lock-läge |Lyssna |En giltig köadress |
| Skjuta upp ett meddelande för senare hämtning |Lyssna |En giltig köadress |
| Deadletter ett meddelande |Lyssna |En giltig köadress |
| Hämta tillståndet som är associerat med en meddelandekösession |Lyssna |En giltig köadress |
| Ange tillståndet som är associerat med en meddelandekösession |Lyssna |En giltig köadress |
| Schemalägg ett meddelande för senare leverans. till exempel [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Lyssna | En giltig köadress
| **Hjälpavsnitt** | | |
| Skapa ett ämne |Hantera |Vilken namnområdesadress som helst |
| Ta bort ett ämne |Hantera |En giltig ämnesadress |
| Räkna upp ämnen |Hantera |/$Resources/Ämnen |
| Hämta ämnesbeskrivningen |Hantera |En giltig ämnesadress |
| Konfigurera auktoriseringsregel för ett ämne |Hantera |En giltig ämnesadress |
| Skicka till ämnet |Skicka |En giltig ämnesadress |
| **Prenumeration** | | |
| Skapa en prenumeration |Hantera |Vilken namnområdesadress som helst |
| Ta bort prenumeration |Hantera |.. /myTopic/Prenumerationer/mySubscription |
| Räkna upp prenumerationer |Hantera |.. /myTopic/Prenumerationer |
| Få prenumerationsbeskrivning |Hantera |.. /myTopic/Prenumerationer/mySubscription |
| Överge eller slutföra meddelanden efter att ha tagit emot meddelandet i peek-lock-läge |Lyssna |.. /myTopic/Prenumerationer/mySubscription |
| Skjuta upp ett meddelande för senare hämtning |Lyssna |.. /myTopic/Prenumerationer/mySubscription |
| Deadletter ett meddelande |Lyssna |.. /myTopic/Prenumerationer/mySubscription |
| Hämta tillståndet som är associerat med en ämnessession |Lyssna |.. /myTopic/Prenumerationer/mySubscription |
| Ange tillståndet som är associerat med en ämnessession |Lyssna |.. /myTopic/Prenumerationer/mySubscription |
| **Regler** | | |
| Skapa en regel |Hantera |.. /myTopic/Prenumerationer/mySubscription |
| Ta bort en regel |Hantera |.. /myTopic/Prenumerationer/mySubscription |
| Räkna upp regler |Hantera eller lyssna |.. /myTopic/Prenumerationer/mySubscription/Regler

## <a name="next-steps"></a>Nästa steg

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Så här använder du servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
