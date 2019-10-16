---
title: Azure Event Grid säkerhet och autentisering
description: Beskriver Azure Event Grid och dess begrepp.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: f22d8c57b0127e646321a20587d0cd89f5c9ea45
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72325412"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid säkerhet och autentisering 

Azure Event Grid har tre typer av autentisering:

* Leverans av webhook-händelser
* Prenumerationer på händelser
* Anpassad publicering av ämnen

## <a name="webhook-event-delivery"></a>Leverans av webhook-händelser

Webhooks är ett av många sätt att ta emot händelser från Azure Event Grid. När en ny händelse är klar skickar Event Grid tjänsten en HTTP-begäran till den konfigurerade slut punkten med händelsen i begär ande texten.

Precis som många andra tjänster som har stöd för webhookar kräver Event Grid att du förvarar ägarskapet för webhook-slutpunkten innan den börjar leverera händelser till den slut punkten. Detta krav förhindrar att en obehörig användare översvämmar din slut punkt med händelser. När du använder någon av de tre Azure-tjänsterna i listan nedan hanterar Azure-infrastrukturen automatiskt den här verifieringen:

* Azure Logic Apps med [Event Grid koppling](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions med [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md)

Om du använder någon annan typ av slut punkt, till exempel en HTTP-utlösare baserad Azure-funktion, måste du delta i en validerings hand skakning med Event Grid. Event Grid stöder två sätt att verifiera prenumerationen.

1. **ValidationCode-handskakning (programmatisk)** : om du styr käll koden för din slut punkt rekommenderas den här metoden. När händelse prenumerationen skapas skickar Event Grid en prenumerations validerings händelse till din slut punkt. Schemat för den här händelsen liknar andra Event Grid-händelser. Data delen av den här händelsen innehåller en `validationCode`-egenskap. Programmet verifierar att verifierings förfrågan är för en förväntad händelse prenumeration och upprepar verifierings koden för att Event Grid. Den här hand skaknings mekanismen stöds i alla Event Grid-versioner.

2. **ValidationURL hand skakning (manuellt)** : i vissa fall går det inte att komma åt käll koden för slut punkten för att implementera ValidationCode-handskakningen. Om du till exempel använder en tjänst från tredje part (t. ex. [Zapier](https://zapier.com) eller [ifttt](https://ifttt.com/)) kan du inte program mässigt svara med validerings koden.

   Från och med version 2018-05-01 – för hands version har Event Grid stöd för en manuell validerings hand skakning. Om du skapar en händelse prenumeration med ett SDK eller ett verktyg som använder API-versionen 2018-05-01-Preview eller senare, skickar Event Grid en `validationUrl`-egenskap i data delen av prenumerations verifierings händelsen. Om du vill slutföra hand skakningen letar du reda på URL: en i händelse data och skickar manuellt en GET-begäran till den. Du kan antingen använda en REST-klient eller din webbläsare.

   Den angivna webb adressen är giltig i 5 minuter. Under denna tid är etablerings statusen för händelse prenumerationen `AwaitingManualAction`. Om du inte slutför den manuella verifieringen inom 5 minuter anges etablerings statusen till `Failed`. Du måste skapa händelse prenumerationen igen innan du startar den manuella verifieringen.

    Den här autentiseringsmetoden kräver också att webhook-slutpunkten returnerar en HTTP-statuskod på 200 så att den vet att posten för verifierings händelsen accepterades innan den kan placeras i manuellt validerings läge. Med andra ord, om slut punkten returnerar 200 men inte returnerar ett verifierings svar program mässigt, överförs läget till det manuella validerings läget. Om verifierings-URL: en hämtas inom 5 minuter anses verifierings hand skakningen vara lyckad.

> [!NOTE]
> Det finns inte stöd för att använda självsignerade certifikat för verifiering. Använd ett signerat certifikat från en certifikat utfärdare (CA) i stället.

### <a name="validation-details"></a>Verifierings information

* Vid skapande/uppdatering av händelse prenumerationen skickar Event Grid en prenumerations validerings händelse till mål slut punkten. 
* Händelsen innehåller ett huvud värde "AEG-Event-Type: SubscriptionValidation".
* Händelse texten har samma schema som andra Event Grid händelser.
* Händelsens eventType-egenskap är `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Egenskapen data för händelsen innehåller en `validationCode`-egenskap med en slumpmässigt genererad sträng. Till exempel "validationCode: acb13...".
* Händelse data innehåller också en `validationUrl`-egenskap med en URL för att verifiera prenumerationen manuellt.
* Matrisen innehåller bara verifierings händelsen. Andra händelser skickas i en separat begäran när du har tillbaka verifierings koden.
* EventGrid dataplanen SDK: er har klasser som motsvarar händelse data för prenumerations verifiering och prenumerations verifierings svar.

Ett exempel på en SubscriptionValidationEvent visas i följande exempel:

```json
[{
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
}]
```

För att bevisa slut punktens ägarskap kan du gå tillbaka till verifierings koden i egenskapen validationResponse, som visas i följande exempel:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Du måste returnera en HTTP 200 OK svars status kod. HTTP 202 accepterades inte som ett giltigt verifierings svar för Event Grid prenumeration.

Du kan också validera prenumerationen manuellt genom att skicka en GET-begäran till verifierings-URL: en. Händelse prenumerationen är i ett väntande tillstånd tills den har verifierats.

Ett exempel på hantering av hand skakningen för prenumerations validering finns i ett [ C# exempel](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Checklista

Om du ser ett fel meddelande, till exempel "försöket att verifiera den angivna slut punktens https: \//din-slut punkt, när händelse prenumerationen skapas, så misslyckades. Mer information finns på https: \//aka. MS/esvalidation ", det betyder att det är ett haveri i validerings hand skakningen. Kontrol lera följande aspekter för att lösa det här felet:

* Har du kontroll över program koden i mål slut punkten? Om du till exempel skriver en HTTP-utlösare baserad Azure Function har du åtkomst till program koden för att göra ändringar i den?
* Om du har åtkomst till program koden implementerar du mekanismen för ValidationCode-baserad hand skakning som visas i exemplet ovan.

* Om du inte har åtkomst till program koden (till exempel om du använder en tjänst från tredje part som stöder webhookar) kan du använda den manuella mekanismen för hand skakning. Kontrol lera att du använder 2018-05-01-Preview API-versionen eller senare (installera Event Grid Azure CLI-tillägget) för att ta emot validationUrl i verifierings händelsen. För att slutföra den manuella validerings hand skakningen hämtar du värdet för egenskapen `validationUrl` och besöker webb adressen i webbläsaren. Om verifieringen lyckas bör du se ett meddelande i din webbläsare om att verifieringen lyckas. Du ser att händelse prenumerationens provisioningState är "lyckades". 

### <a name="event-delivery-security"></a>Säkerhet för händelse leverans

Du kan skydda webhook-slutpunkten genom att lägga till frågeparametrar till webhook-URL: en när du skapar en händelse prenumeration. Ange en av dessa frågeparametrar [som en hemlighet](https://en.wikipedia.org/wiki/Access_token), till exempel en åtkomsttoken. Webhooken kan använda hemligheten för att identifiera att händelsen kommer från Event Grid med giltiga behörigheter. Event Grid tar med dessa frågeparametrar i varje händelse leverans till webhooken.

När du redigerar händelse prenumerationen visas eller returneras inte frågeparametrar om parametern [--include-full-URL-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) används i Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Slutligen är det viktigt att Observera att Azure Event Grid endast stöder HTTPS webhook-slutpunkter.

## <a name="event-subscription"></a>Händelse prenumeration

För att prenumerera på en händelse måste du bevisa att du har åtkomst till händelse källan och-hanteraren. Att bevisa att du äger en webhook har täckts i föregående avsnitt. Om du använder en händelse hanterare som inte är en webhook (till exempel en händelsehubben eller Queue Storage) behöver du Skriv åtkomst till den resursen. Med den här behörighets kontrollen förhindrar du att obehöriga användare skickar händelser till din resurs.

Du måste ha behörigheten **Microsoft. EventGrid/EventSubscriptions/Write** på den resurs som är händelse källa. Du behöver den här behörigheten eftersom du skriver en ny prenumeration i resursens omfattning. Vilken resurs som krävs varierar beroende på om du prenumererar på ett system ämne eller ett anpassat ämne. Båda typerna beskrivs i det här avsnittet.

### <a name="system-topics-azure-service-publishers"></a>System ämnen (Azure Service Publisher)

I system avsnitt måste du ha behörighet att skriva en ny händelse prenumeration vid resurs publiceringen av händelsen. Resursens format är: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Om du till exempel vill prenumerera på en händelse på ett lagrings konto med namnet **acct**, behöver du behörigheten Microsoft. EventGrid/EventSubscriptions/Write på: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Anpassade ämnen

För anpassade ämnen måste du ha behörighet att skriva en ny händelse prenumeration i avsnittet Event Grid. Resursens format är: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Om du till exempel vill prenumerera på ett anpassat ämne med namnet " **ämne**", behöver du ha behörigheten Microsoft. EventGrid/EventSubscriptions/Write på: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Anpassad publicering av ämnen

I anpassade avsnitt används antingen signatur för delad åtkomst (SAS) eller nyckel-autentisering. Vi rekommenderar SAS, men Key Authentication tillhandahåller enkel programmering och är kompatibel med många befintliga webhook-utgivare. 

Du inkluderar Authentication-värdet i HTTP-huvudet. För SAS använder du **AEG-SAS-token** för Head-värdet. För Key Authentication använder du **AEG-SAS-Key** för huvudets värde.

### <a name="key-authentication"></a>Nyckel autentisering

Nyckel autentisering är den enklaste formen av autentisering. Använd formatet: `aeg-sas-key: <your key>`

Du kan till exempel skicka en nyckel med:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-token

SAS-token för Event Grid inkluderar resursen, en förfallo tid och en signatur. Formatet för SAS-token är: `r={resource}&e={expiration}&s={signature}`.

Resursen är sökvägen till det event Grid-ämne som du skickar händelser till. Till exempel är en giltig resurs Sök väg: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

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

## <a name="management-access-control"></a>Hanterings Access Control

Med Azure Event Grid kan du kontrol lera åtkomst nivån som ges till olika användare för att utföra olika hanterings åtgärder, till exempel lista händelse prenumerationer, skapa nya och generera nycklar. Event Grid använder Azures rollbaserad åtkomst kontroll (RBAC).

### <a name="operation-types"></a>Åtgärds typer

Event Grid stöder följande åtgärder:

* Microsoft. EventGrid/*/Read
* Microsoft. EventGrid/*/Write
* Microsoft. EventGrid/*/Delete
* Microsoft. EventGrid/eventSubscriptions/getFullUrl/Action
* Microsoft. EventGrid/ämnen/Listnycklar/åtgärd
* Microsoft. EventGrid/ämnen/regenerateKey/åtgärd

De sista tre åtgärderna returnerar potentiellt hemlig information som filtreras bort från normala Läs åtgärder. Vi rekommenderar att du begränsar åtkomsten till dessa åtgärder. 

### <a name="built-in-roles"></a>Inbyggda roller

Event Grid innehåller två inbyggda roller för att hantera händelse prenumerationer. De är viktiga när du implementerar [händelse domäner](event-domains.md) eftersom de ger användarna de behörigheter de behöver för att prenumerera på ämnen i din händelse domän. De här rollerna fokuserar på händelse prenumerationer och beviljar inte åtkomst för åtgärder som att skapa ämnen.

Du kan [tilldela dessa roller till en användare eller grupp](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription Contributor (för hands version)** : hantera Event Grid prenumerations åtgärder

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription Reader (för hands version)** : Läs Event Grid prenumerationer

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>Anpassade roller

Om du behöver ange behörigheter som skiljer sig från de inbyggda rollerna kan du skapa anpassade roller.

Följande är exempel på Event Grid roll definitioner som gör det möjligt för användarna att vidta olika åtgärder. De här anpassade rollerna skiljer sig från de inbyggda rollerna eftersom de ger bredare åtkomst än bara händelse prenumerationer.

**EventGridReadOnlyRole. JSON**: Tillåt endast skrivskyddade åtgärder.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole. JSON**: Tillåt begränsade post åtgärder men tillåt inte borttagnings åtgärder.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole. JSON**: tillåter alla Event Grid-åtgärder.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Du kan skapa anpassade roller med [PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md)och [rest](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [About Event Grid](overview.md)
