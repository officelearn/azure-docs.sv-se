---
title: Azure Event Grid säkerhet och autentisering
description: I den här artikeln beskrivs olika sätt att autentisera åtkomst till dina Event Grid-resurser (webhook, prenumerationer, anpassade ämnen)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532401"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Autentisera åtkomst till Event Grid resurser

Azure Event Grid har tre typer av autentisering:

- Leverans av webhook-händelser
- Prenumerationer på händelser
- Anpassad publicering av ämnen

## <a name="webhook-event-delivery"></a>Leverans av webhook-händelser

Webhooks är ett av många sätt att ta emot händelser från Azure Event Grid. När en ny händelse är klar skickar Event Grid tjänsten en HTTP-begäran till den konfigurerade slut punkten med händelsen i begär ande texten.

Precis som många andra tjänster som har stöd för webhookar kräver Event Grid att du förvarar ägarskapet för webhook-slutpunkten innan den börjar leverera händelser till den slut punkten. Detta krav förhindrar att en obehörig användare översvämmar din slut punkt med händelser. När du använder någon av de tre Azure-tjänsterna i listan nedan hanterar Azure-infrastrukturen automatiskt den här verifieringen:

- Azure Logic Apps med [Event Grid koppling](https://docs.microsoft.com/connectors/azureeventgrid/)
- Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions med [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md)

Om du använder någon annan typ av slut punkt, till exempel en HTTP-utlösare baserad Azure-funktion, måste du delta i en validerings hand skakning med Event Grid. Event Grid stöder två sätt att verifiera prenumerationen.

1. **Synkron hand skakning**: när händelse prenumerationen skapas skickar Event Grid en prenumerations validerings händelse till din slut punkt. Schemat för den här händelsen liknar andra Event Grid-händelser. Data delen av den här händelsen innehåller en `validationCode` egenskap. Programmet verifierar att verifierings förfrågan är för en förväntad händelse prenumeration och returnerar verifierings koden i svaret synkront. Den här hand skaknings mekanismen stöds i alla Event Grid-versioner.

2. **Asynkron hand skakning**: i vissa fall kan du inte returnera ValidationCode i svar synkront. Om du till exempel använder en tjänst från tredje part (t [`Zapier`](https://zapier.com) . ex. eller [ifttt](https://ifttt.com/)) kan du inte program mässigt svara med validerings koden.

   Från och med version 2018-05-01 – för hands version har Event Grid stöd för en manuell validerings hand skakning. Om du skapar en händelse prenumeration med ett SDK eller verktyg som använder API-version 2018-05-01-Preview eller senare, skickar Event Grid en `validationUrl` egenskap i data delen av prenumerations verifierings händelsen. Om du vill slutföra hand skakningen letar du reda på URL: en i händelse data och gör en GET-begäran till den. Du kan antingen använda en REST-klient eller din webbläsare.

   Den angivna webb adressen är giltig i **5 minuter**. Under denna tid är `AwaitingManualAction`etablerings statusen för händelse prenumerationen. Om du inte slutför den manuella verifieringen inom 5 minuter anges etablerings statusen till `Failed`. Du måste skapa händelse prenumerationen igen innan du startar den manuella verifieringen.

   Den här autentiseringsmetoden kräver också att webhook-slutpunkten returnerar en HTTP-statuskod på 200 så att den vet att posten för verifierings händelsen accepterades innan den kan placeras i manuellt validerings läge. Med andra ord, om slut punkten returnerar 200 men inte returnerar ett verifierings svar synkront, övergår läget till det manuella validerings läget. Om validerings-URL: en visas inom 5 minuter anses verifierings hand skakningen vara lyckad.

> [!NOTE]
> Det finns inte stöd för att använda självsignerade certifikat för verifiering. Använd ett signerat certifikat från en certifikat utfärdare (CA) i stället.

### <a name="validation-details"></a>Verifierings information

- Vid skapande/uppdatering av händelse prenumerationen skickar Event Grid en prenumerations validerings händelse till mål slut punkten.
- Händelsen innehåller ett huvud värde "AEG-Event-Type: SubscriptionValidation".
- Händelse texten har samma schema som andra Event Grid händelser.
- Händelsens händelse-egenskap är `Microsoft.EventGrid.SubscriptionValidationEvent`.
- Egenskapen data för händelsen innehåller en `validationCode` egenskap med en slumpmässigt genererad sträng. Till exempel "validationCode: acb13...".
- Händelse data innehåller även en `validationUrl` egenskap med en URL för att verifiera prenumerationen manuellt.
- Matrisen innehåller bara verifierings händelsen. Andra händelser skickas i en separat begäran när du har tillbaka verifierings koden.
- EventGrid dataplanen SDK: er har klasser som motsvarar händelse data för prenumerations verifiering och prenumerations verifierings svar.

Ett exempel på en SubscriptionValidationEvent visas i följande exempel:

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

För att bevisa slut punktens ägarskap kan du gå tillbaka till verifierings koden i egenskapen validationResponse, som visas i följande exempel:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Du måste returnera en HTTP 200 OK svars status kod. HTTP 202 accepterades inte som ett giltigt verifierings svar för Event Grid prenumeration. Http-begäran måste slutföras inom 30 sekunder. Om åtgärden inte slutförs inom 30 sekunder avbryts åtgärden och du kan försöka igen efter 5 sekunder. Om alla försöken Miss lyckas behandlas det som ett fel i validerings hand skakningen.

Du kan också validera prenumerationen manuellt genom att skicka en GET-begäran till verifierings-URL: en. Händelse prenumerationen är i ett väntande tillstånd tills den har verifierats. Verifierings-URL: en använder port 553. Om brand Väggs reglerna blockerar port 553 kan regler behöva uppdateras för lyckad hand skakning.

Ett exempel på hur du hanterar en prenumerations validerings hand skakning finns i ett [C#-exempel](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="troubleshooting-eventsubsciption-validation"></a>Felsöka EventSubsciption-verifiering

Om du ser ett fel meddelande som "försöket att validera den angivna slut punkten https:\//Your-Endpoint-here misslyckades, när händelse prenumerationen har skapats. Mer information finns på https:\//aka.MS/esvalidation ", det betyder att det är ett haveri i validerings hand skakningen. Kontrol lera följande aspekter för att lösa det här felet:

- Gör ett HTTP-inlägg till webhook-URL: en med ett [exempel på SubscriptionValidationEvent](#validation-details) för begäran med Postman eller sväng eller liknande verktyg.
- Om webhooken implementerar mekanismen för synkron verifierings hand skakning kontrollerar du att ValidationCode returneras som en del av svaret.
- Om webhooken implementerar mekanismen för asynkron verifierings hand skakning kontrollerar du att HTTP-inlägget returnerar 200 OK.
- Om webhooken returnerar 403 (förbjuden) i svaret kontrollerar du att webhooken ligger bakom en Azure Application Gateway eller brand vägg för webbaserade program. Om så är fallet måste du inaktivera dessa brand Väggs regler och göra ett HTTP-inlägg igen:

  920300 (begäran saknar ett accept-huvud, vi kan åtgärda detta)

  942430 (begränsad SQL Character-avvikelse identifiering (argument): # av specialtecken har överskridits (12))

  920230 (flera URL-kodning identifierade)

  942130 (SQL-injektering: SQL-Tautology har identifierats.)

  931130 (möjliga RFI-angrepp (Remote File inkludering) = av-domän referens/länk)

### <a name="event-delivery-security"></a>Säkerhet för händelse leverans

#### <a name="azure-ad"></a>Azure AD

Du kan skydda webhook-slutpunkten genom att använda Azure Active Directory för att autentisera och auktorisera Event Grid att publicera händelser till dina slut punkter. Du måste skapa ett Azure Active Directory-program, skapa en roll-och tjänst princip i ditt program som auktoriserar Event Grid och konfigurera händelse prenumerationen så att den använder Azure AD-programmet. [Lär dig hur du konfigurerar AAD med event Grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Frågeparametrar

Du kan skydda webhook-slutpunkten genom att lägga till frågeparametrar till webhook-URL: en när du skapar en händelse prenumeration. Ange en av dessa frågeparametrar [som en hemlighet](https://en.wikipedia.org/wiki/Access_token), till exempel en åtkomsttoken. Webhooken kan använda hemligheten för att identifiera att händelsen kommer från Event Grid med giltiga behörigheter. Event Grid tar med dessa frågeparametrar i varje händelse leverans till webhooken.

När du redigerar händelse prenumerationen visas eller returneras inte frågeparametrar om parametern [--include-full-URL-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) används i Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Slutligen är det viktigt att Observera att Azure Event Grid endast stöder HTTPS webhook-slutpunkter.

## <a name="event-subscription"></a>Händelse prenumeration

För att prenumerera på en händelse måste du bevisa att du har åtkomst till händelse källan och-hanteraren. Att bevisa att du äger en webhook har täckts i föregående avsnitt. Om du använder en händelse hanterare som inte är en webhook (till exempel en händelsehubben eller Queue Storage) behöver du Skriv åtkomst till den resursen. Med den här behörighets kontrollen förhindrar du att obehöriga användare skickar händelser till din resurs.

Du måste ha behörigheten **Microsoft. EventGrid/EventSubscriptions/Write** på den resurs som är händelse källa. Du behöver den här behörigheten eftersom du skriver en ny prenumeration i resursens omfattning. Vilken resurs som krävs varierar beroende på om du prenumererar på ett system ämne eller ett anpassat ämne. Båda typerna beskrivs i det här avsnittet.

### <a name="system-topics-azure-service-publishers"></a>System ämnen (Azure Service Publisher)

I system avsnitt måste du ha behörighet att skriva en ny händelse prenumeration vid resurs publiceringen av händelsen. Formatet för resursen är:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Om du till exempel vill prenumerera på en händelse på ett lagrings konto med namnet **acct**, behöver du behörigheten Microsoft. EventGrid/EventSubscriptions/Write på:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Anpassade ämnen

För anpassade ämnen måste du ha behörighet att skriva en ny händelse prenumeration i avsnittet Event Grid. Formatet för resursen är:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Om du till exempel vill prenumerera på ett anpassat ämne med namnet " **ämne**" måste du ha behörigheten Microsoft. EventGrid/EventSubscriptions/Write på:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Anpassad publicering av ämnen

I anpassade avsnitt används antingen signatur för delad åtkomst (SAS) eller nyckel-autentisering. Vi rekommenderar SAS, men Key Authentication tillhandahåller enkel programmering och är kompatibel med många befintliga webhook-utgivare.

Du inkluderar Authentication-värdet i HTTP-huvudet. För SAS använder du **AEG-SAS-token** för Head-värdet. För Key Authentication använder du **AEG-SAS-Key** för huvudets värde.

### <a name="key-authentication"></a>Nyckel autentisering

Nyckel autentisering är den enklaste formen av autentisering. Använd formatet:`aeg-sas-key: <your key>`

Du kan till exempel skicka en nyckel med:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-token

SAS-token för Event Grid inkluderar resursen, en förfallo tid och en signatur. Formatet på SAS-token är: `r={resource}&e={expiration}&s={signature}`.

Resursen är sökvägen till det event Grid-ämne som du skickar händelser till. Till exempel är en giltig resurs Sök väg: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Alla API-versioner som stöds finns i [resurs typer för Microsoft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Du genererar signaturen från en nyckel.

Till exempel är ett giltigt **AEG-SAS-Toke-** värde:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

I följande exempel skapas en SAS-token för användning med Event Grid:

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

Alla händelser eller data som skrivs till disk av tjänsten Event Grid krypteras av en Microsoft-hanterad nyckel som garanterar att den är krypterad i vila. Dessutom är den längsta tids perioden som händelser eller data kvarhålls vara 24 timmar i enlighet med principen för [Event Grid återförsök](delivery-and-retry.md). Event Grid tar automatiskt bort alla händelser eller data efter 24 timmar, eller händelsens tids till Live, beroende på vilket som är mindre.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Slut punkts validering med CloudEvents v 1.0
Om du redan är bekant med Event Grid kan du vara medveten om Event Gridens slut punkts validerings hand skakning för att förhindra missbruk. CloudEvents v 1.0 implementerar egna [insemantiker för missbruks skydd](security-authentication.md#webhook-event-delivery) med hjälp av http-alternativ-metoden. Du kan läsa mer om det [här](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). När du använder CloudEvents-schemat för utdata använder Event Grid med CloudEvents v 1.0 missbruk-skyddet i stället för mekanismen för Event Grid validerings händelser.

## <a name="next-steps"></a>Nästa steg

- En introduktion till Event Grid finns i [About Event Grid](overview.md)
