---
title: Azure Event Grid säkerhet och autentisering
description: I den här artikeln beskrivs olika sätt att autentisera åtkomst till dina Event Grid-resurser (WebHook, prenumerationer, anpassade ämnen)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532401"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Autentisera åtkomst till resurser för händelserutnät

Azure Event Grid har tre typer av autentisering:

- Leverans av WebHook-evenemang
- Prenumerationer på händelser
- Anpassad ämnespublicering

## <a name="webhook-event-delivery"></a>Leverans av WebHook-evenemang

Webhooks är ett av många sätt att ta emot händelser från Azure Event Grid. När en ny händelse är klar, posts Event Grid-tjänsten en HTTP-begäran till den konfigurerade slutpunkten med händelsen i begärandetexten.

Precis som många andra tjänster som stöder webhooks kräver Event Grid att du ska bevisa äganderätten till webhook-slutpunkten innan den börjar leverera händelser till den slutpunkten. Det här kravet förhindrar att en obehörig användare översvämmar slutpunkten med händelser. När du använder någon av de tre Azure-tjänsterna som anges nedan hanterar Azure-infrastrukturen automatiskt den här valideringen:

- Azure Logic-appar med [Event Grid Connector](https://docs.microsoft.com/connectors/azureeventgrid/)
- Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure-funktioner med [utlösare för händelserutnät](../azure-functions/functions-bindings-event-grid.md)

Om du använder någon annan typ av slutpunkt, till exempel en HTTP-utlösare baserad Azure-funktion, måste slutpunktskoden delta i ett verifieringskakslag med Event Grid. Event Grid stöder två sätt att validera prenumerationen.

1. **Synkron handslag:** Vid tidpunkten för skapandet av händelseprenumeration skickar Event Grid en prenumerationsverifieringshändelse till slutpunkten. Schemat för den här händelsen liknar alla andra eventrutnätshändelse. Datadelen av den `validationCode` här händelsen innehåller en egenskap. Programmet verifierar att valideringsbegäran är för en förväntad händelseprenumeration och returnerar valideringskoden i svarssynkront. Den här handskakningsmekanismen stöds i alla Event Grid-versioner.

2. **Asynkront handslag**: I vissa fall kan du inte returnera ValidationCode i svar synkront. Om du till exempel använder en tjänst [`Zapier`](https://zapier.com) från tredje part (som eller [IFTTT](https://ifttt.com/)) kan du inte programmatiskt svara med verifieringskoden.

   Från och med version 2018-05-01-preview stöder Event Grid ett manuellt verifieringshandslag. Om du skapar en händelseprenumeration med en SDK eller ett verktyg som använder API-version 2018-05-01-förhandsversion eller senare skickar Event Grid en `validationUrl` egenskap i datadelen av prenumerationsverifieringshändelsen. Om du vill slutföra handskakningen hittar du webbadressen i händelsedata och gör en GET-begäran till den. Du kan använda antingen en REST-klient eller din webbläsare.

   Den angivna webbadressen är giltig i **5 minuter**. Under den tiden är `AwaitingManualAction`etableringstillståndet för händelseprenumerationen . Om du inte slutför den manuella valideringen inom 5 `Failed`minuter är etableringstillståndet inställt på . Du måste skapa händelseprenumerationen igen innan du startar den manuella valideringen.

   Den här autentiseringsmekanismen kräver också att webhook-slutpunkten returnerar en HTTP-statuskod på 200 så att den vet att POST för valideringshändelsen accepterades innan den kan placeras i manuellt valideringsläge. Med andra ord, om slutpunkten returnerar 200 men inte returnerar tillbaka ett valideringssvar synkront, övergå läget till det manuella valideringsläget. Om det finns en GET på validerings-URL:en inom 5 minuter anses valideringshandskakningen vara lyckad.

> [!NOTE]
> Det går inte att använda självsignerade certifikat för validering. Använd ett signerat certifikat från en certifikatutfärdarcertifikatutfärdning i stället.

### <a name="validation-details"></a>Information om validering

- När händelseprenumeration skapas/uppdateras bokför Event Grid en prenumerationsverifieringshändelse till målslutpunkten.
- Händelsen innehåller ett huvudvärde "aeg-event-type: SubscriptionValidation".
- Händelsetexten har samma schema som andra Event Grid-händelser.
- Egenskapen eventType för `Microsoft.EventGrid.SubscriptionValidationEvent`händelsen är .
- Egenskapen data för händelsen `validationCode` innehåller en egenskap med en slumpmässigt genererad sträng. Till exempel "validationCode: acb13...".
- Händelsedata innehåller också `validationUrl` en egenskap med en URL för att manuellt validera prenumerationen.
- Matrisen innehåller bara verifieringshändelsen. Andra händelser skickas i en separat begäran när du har upprepat valideringskoden.
- EventGrid DataPlane SDK:er har klasser som motsvarar händelsedata för prenumerationsverifiering och prenumerationsvalidering.

Ett exempel på SubscriptionValidationEvent visas i följande exempel:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Om du vill bevisa att slutpunktsägarskapet visar att valideringskoden finns i egenskapen validationResponse, vilket visas i följande exempel:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Du måste returnera statuskoden för HTTP 200 OK-svar. HTTP 202 Accepterad känns inte igen som ett giltigt verifieringssvar för Event Grid-prenumeration. Http-begäran måste slutföras inom 30 sekunder. Om åtgärden inte slutförs inom 30 sekunder avbryts åtgärden och den kan återanpassas efter 5 sekunder. Om alla försök misslyckas behandlas det som ett handskakningsfel för validering.

Du kan också validera prenumerationen manuellt genom att skicka en GET-begäran till validerings-URL:en. Händelseprenumerationen förblir i ett väntande tillstånd tills den har validerats. Validerings-url:en använder port 553. Om brandväggsreglerna blockerar port 553 kan reglerna behöva uppdateras för att lyckas med manuellt handslag.

Ett exempel på hur du hanterar handskakningen för prenumerationsvalidering finns i ett [C#-exempel](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="troubleshooting-eventsubsciption-validation"></a>Felsökning av validering av eventSubsciption

Om du ser ett felmeddelande som "Försök att validera den angivna slutpunkten\/https: /your-endpoint-here failed" visas under händelseprenumerationen. För mer information,\/besök https: /aka.ms/esvalidation", indikerar det att det finns ett fel i valideringskakningen. Lös det här felet genom att kontrollera följande aspekter:

- Gör ett HTTP-INLÄGG till din webhook url med ett [exempel SubscriptionValidationEvent](#validation-details) begäran brödtext med Postman eller curl eller liknande verktyg.
- Om din webhook implementerar handskakningsmekanism för synkron validering kontrollerar du att ValidationCode returneras som en del av svaret.
- Om din webhook implementerar asynkron valideringshandskakningsmekanism kontrollerar du att http-post returnerar 200 OK.
- Om din webhook returnerar 403 (Förbjudet) i svaret kontrollerar du om din webhook ligger bakom en Azure Application Gateway- eller Web Application Firewall. Om så är fallet måste du inaktivera dessa brandväggsregler och göra ett HTTP-INLÄGG igen:

  920300 (Begär saknas en Acceptera Header, kan vi fixa detta)

  942430 (Begränsad AVVIKELSEIDENTIFIERING AV SQL-tecken (args): # av specialtecken som överskridits (12))

  920230 (kodning av flera webbadresser har identifierats)

  942130 (SQL Injection Attack: SQL Tautology Upptäckt.)

  931130 (Möjlig RFI-attack (Remote File Inclusion) = referens/länk utanför domänen)

### <a name="event-delivery-security"></a>Säkerhet för händelseleverans

#### <a name="azure-ad"></a>Azure AD

Du kan skydda din webhook-slutpunkt genom att använda Azure Active Directory för att autentisera och auktorisera Event Grid för att publicera händelser till dina slutpunkter. Du måste skapa ett Azure Active Directory-program, skapa en roll- och tjänstprincip i ditt program som auktoriserar Event Grid och konfigurera händelseprenumerationen för att använda Azure AD-programmet. [Lär dig hur du konfigurerar AAD med Event Grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Frågeparametrar

Du kan skydda slutpunkten webhook genom att lägga till frågeparametrar i webhook-URL:en när du skapar en händelseprenumeration. Ange att en av dessa frågeparametrar ska vara en hemlighet, till exempel en [åtkomsttoken](https://en.wikipedia.org/wiki/Access_token). Webhooken kan använda hemligheten för att känna igen händelsen kommer från Event Grid med giltiga behörigheter. Event Grid kommer att innehålla dessa frågeparametrar i varje händelseleverans till webhooken.

När du redigerar händelseprenumerationen visas inte frågeparametrarna eller returneras om inte parametern [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) används i Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Slutligen är det viktigt att notera att Azure Event Grid endast stöder HTTPS webhook-slutpunkter.

## <a name="event-subscription"></a>Prenumeration på evenemang

Om du vill prenumerera på en händelse måste du bevisa att du har åtkomst till händelsekällan och hanteraren. Bevisa att du äger en WebHook täcktes i föregående avsnitt. Om du använder en händelsehanterare som inte är en WebHook (till exempel en händelsenav eller kölagring) behöver du skrivåtkomst till den resursen. Den här behörighetskontrollen förhindrar att en obehörig användare skickar händelser till din resurs.

Du måste ha behörigheten **Microsoft.EventGrid/EventSubscriptions/Write** för den resurs som är händelsekällan. Du behöver den här behörigheten eftersom du skriver en ny prenumeration i resursens omfattning. Den nödvändiga resursen skiljer sig beroende på om du prenumererar på ett systemämne eller ett anpassat ämne. Båda typerna beskrivs i det här avsnittet.

### <a name="system-topics-azure-service-publishers"></a>Systemavsnitt (Azure-tjänstutgivare)

För systemavsnitt behöver du behörighet att skriva en ny händelseprenumeration i omfattningen av resursen som publicerar händelsen. Resursens format är:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Om du till exempel vill prenumerera på en händelse på ett lagringskonto med namnet **myacct**behöver du behörigheten Microsoft.EventGrid/EventSubscriptions/Write på:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Anpassade ämnen

För anpassade ämnen behöver du behörighet att skriva en ny händelseprenumeration i händelserutnätets avsnitt. Resursens format är:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Om du till exempel vill prenumerera på ett anpassat ämne med namnet **mytopic**behöver du behörigheten Microsoft.EventGrid/EventSubscriptions/Write på:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Anpassad ämnespublicering

Anpassade avsnitt använder antingen SAS (Shared Access Signature) eller nyckelautentisering. Vi rekommenderar SAS, men nyckelautentisering ger enkel programmering och är kompatibel med många befintliga webhook-utgivare.

Du kan inkludera autentiseringsvärdet i HTTP-huvudet. För SAS använder du **aeg-sas-token** för huvudvärdet. För nyckelautentisering använder du **aeg-sas-key** för huvudvärdet.

### <a name="key-authentication"></a>Nyckelautentisering

Nyckelautentisering är den enklaste formen av autentisering. Använd formatet:`aeg-sas-key: <your key>`

Du kan till exempel skicka en nyckel med:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-token

SAS-token för Event Grid inkluderar resursen, en förfallotid och en signatur. Formatet för SAS-token `r={resource}&e={expiration}&s={signature}`är: .

Resursen är sökvägen till det händelserutnätsämne som du skickar händelser till. En giltig resurssökväg är `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`till exempel: . Mer om du vill visa alla API-versioner som stöds finns i [Microsoft.EventGrid-resurstyper](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Du skapar signaturen från en nyckel.

Ett giltigt **aeg-sas-token-värde** är till exempel:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

I följande exempel skapas en SAS-token som kan användas med Händelserutnät:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="encryption-at-rest"></a>Vilande kryptering

Alla händelser eller data som skrivs till disk av Event Grid-tjänsten krypteras av en Microsoft-hanterad nyckel som säkerställer att den krypteras i vila. Dessutom är den maximala tidsperioden som händelser eller data lagras 24 timmar i anslutning till principen För återförsök i [händelserutnätet](delivery-and-retry.md). Event Grid tar automatiskt bort alla händelser eller data efter 24 timmar, eller händelsetid för att leva, beroende på vilket som är lägst.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Endpoint-validering med CloudEvents v1.0
Om du redan är bekant med Event Grid kan du vara medveten om Event Grids handslag för slutpunktsverifiering för att förhindra missbruk. CloudEvents v1.0 implementerar sin egen [semantik för missbruksskydd](security-authentication.md#webhook-event-delivery) med hjälp av HTTP OPTIONS-metoden. Du kan läsa mer om det [här](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). När du använder CloudEvents-schemat för utdata använder Event Grid med CloudEvents v1.0-missbruksskydd i stället för händelserutnätsverifieringshändelsemekanismen.

## <a name="next-steps"></a>Nästa steg

- En introduktion till Händelserutnätet finns i [Om händelserutnätet](overview.md)
