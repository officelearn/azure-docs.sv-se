---
title: Azure Event Grid säkerhet och autentisering
description: Beskriver Azure Event Grid och dess begrepp.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: babanisa
ms.openlocfilehash: d2bc0d8f78e6fe0806afb3208c88df28b8cce1f9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39460242"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid säkerhet och autentisering 

Azure Event Grid har tre typer av autentisering:

* Prenumerationer på händelser
* Publicering av händelser
* WebHook-händelseleverans

## <a name="webhook-event-delivery"></a>WebHook-händelseleverans

Webhooks är en av de många sätt att ta emot händelser från Azure Event Grid. När en ny händelse är klar skickar EventGrid-tjänsten en HTTP-begäran till den konfigurera slutpunkten med händelsen i begärandetexten.

Liksom många andra tjänster som stöder webhooks måste EventGrid du bevisa ”ägare” till Webhook-slutpunkt innan den startar leverera händelser till denna slutpunkt. Det här kravet är att förhindra att en godtrogna slutpunkt blir slutpunkten för händelseleverans från EventGrid mål. Men när du använder någon av de tre Azure-tjänster som anges nedan, hanterar Azure-infrastrukturen automatiskt den här verifieringen:

* Azure Logic Apps
* Azure Automation
* Azure Functions för EventGrid-utlösare.

Om du använder någon annan typ av slutpunkt, t.ex. en HTTP-utlösare baserade Azure-funktion, måste din slutpunkt kod att delta i en verifiering handskakning med EventGrid. EventGrid stöder två olika verifiering handskakning modeller:

1. ValidationCode baserat handskakning: vid tidpunkten för händelsen prenumeration har skapats, EventGrid publicerar en ”prenumeration verifiering händelse” till slutpunkten. Schemat för den här händelsen är ungefär som andra EventGridEvent och datamängden i den här händelsen innehåller en ”validationCode”-egenskap. När ditt program har verifierat att begäran om verifiering är för en förväntad händelse-prenumeration, måste din programkod ska svara på eko tillbaka verifieringskoden till EventGrid. Den här mekanismen för handskakning stöds i alla EventGrid-versioner.

2. ValidationURL baserat handskakning (manuell handskakning): I vissa fall kan du kanske inte har kontroll över källkoden för slutpunkten för att kunna implementera ValidationCode baserat-handskakningen. Exempel: Om du använder en tjänst från tredje part (t.ex. [Zapier](https://zapier.com) eller [IFTTT](https://ifttt.com/)), kan du inte kunna programmässigt svarar med verifieringskoden. Därför stöder från och med versionen 2018-05-01-preview, EventGrid nu en manuell verifiering-handskakning. Om du skapar en händelseprenumeration med hjälp av SDK/verktyg som använder den här nya API-versionen (2018-05-01-preview), EventGrid skickar en ”validationUrl”-egenskap (utöver egenskapen ”validationCode”) som en del av datamängden i valideringen av prenumeration händelsen. För att slutföra handskakningen bara en hämtning begära på URL: en, antingen via en REST-klient eller med hjälp av webbläsaren. Den angivna validationUrl är endast giltig för ungefär 10 minuter, så om du inte slutföra manuell verifiering inom den här tiden, provisioningState händelseprenumerationen övergår till ”misslyckades” och du behöver ett nytt försök att skapa händelsen prenumerationen innan du försöker utföra manuell verifiering igen.

Den här mekanismen för manuell validering är i förhandsversion. Om du vill använda den måste du installera [Event Grid-tillägget](/cli/azure/azure-cli-extensions-list) för [AZ CLI 2.0](/cli/azure/install-azure-cli). Du kan installera det med `az extension add --name eventgrid`. Om du använder REST API måste du använda `api-version=2018-05-01-preview`.

### <a name="validation-details"></a>Verifieringsinformation

* Vid tidpunkten för händelsen skapande/uppdatering av prenumeration publicerar Event Grid en händelse för verifiering av prenumerationen till mål-slutpunkten. 
* Händelsen innehåller ett huvudvärde ”Aeg Händelsetyp: SubscriptionValidation”.
* Händelsemeddelandet har samma schema som andra Event Grid-händelser.
* Händelsetyp-egenskapen för händelsen är ”Microsoft.EventGrid.SubscriptionValidationEvent”.
* Dataegenskapen för händelsen innehåller en ”validationCode”-egenskap med en slumpmässigt genererad sträng. Till exempel ”validationCode: acb13...”.
* Om du använder API-versionen 2018-05-01-preview inkluderar händelsedata även en ”validationUrl”-egenskap med en URL för manuell verifiering av prenumerationen.
* Matrisen innehåller endast händelsen verifiering. Andra händelser skickas i en separat begäran när du tillbaka echo verifieringskoden.
* SDK: er för EventGrid dataplanen har klasser som motsvarar prenumerationen verifiering händelsedata och verifieringssvaret för prenumerationen.

Ett exempel SubscriptionValidationEvent visas i följande exempel:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

För att bevisa ägarskapet för slutpunkten echo tillbaka verifieringskoden i egenskapen validationResponse som visas i följande exempel:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Du kan också manuellt verifiera prenumerationen genom att skicka en GET-begäran till URL: en för verifiering. Händelseprenumerationen kvar i ett väntande tillstånd tills verifieras.

Du kan hitta C#-exempel som visar hur du hanterar prenumerationen verifiering handskakning vid https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs.

### <a name="checklist"></a>Checklista

När händelsen prenumeration skapas om du ser ett felmeddelande visas som ”försök att verifiera den angivna slutpunkten https://your-endpoint-here misslyckades. Mer information på https://aka.ms/esvalidation”, betyder det att det finns ett fel i valideringen-handskakningen. Lös felet genom att kontrollera följande aspekter:

* Har du kontroll över programkoden i mål-slutpunkten? Till exempel om du skriver en HTTP-utlösare baserade Azure-funktion, har du åtkomst till programkod för att göra ändringar i den?
* Om du har åtkomst till programkoden kan implementera ValidationCode baserat handskakning mekanism som visas i exemplet ovan.

* Om du inte har åtkomst till programkoden (t.ex. Om du använder en tredjeparts-tjänst som stöder webhooks), du kan använda mekanismen för manuell handskakning. För att kunna göra detta, se till att du använder API-versionen 2018-05-01-preview (t.ex. med den EventGrid CLI-tillägg som beskrivs ovan) för att få validationUrl i händelsen verifiering. Hämta värdet för egenskapen ”validationUrl” och gå till URL: en i webbläsaren för att slutföra manuell verifiering-handskakningen. Om verifieringen lyckas, bör du se ett meddelande i din webbläsare att valideringen går bra och du ser att händelsen prenumerationens provisioningState är ”lyckades”. 

### <a name="event-delivery-security"></a>Säkerhet med händelser leverans

Du kan skydda din webhook-slutpunkt genom att lägga till frågeparametrar webhook-URL när du skapar en händelseprenumeration. Ange ett av dessa frågeparametrar ska vara en hemlighet som en [åtkomsttoken](https://en.wikipedia.org/wiki/Access_token) som webhooken kan använda för att identifiera händelsen kommer från Event Grid med giltig behörighet. Event Grid tas dessa Frågeparametrar i varje händelseleverans till webhooken.

När du redigerar händelseprenumerationen frågeparametrar inte ska visas eller returneras, såvida inte den [--inkludera-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parametern används i Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Slutligen är det viktigt att notera att Azure Event Grid endast stöder HTTPS webhook-slutpunkter.

## <a name="event-subscription"></a>Händelseprenumeration

Om du vill prenumerera på en händelse, måste du ha den **Microsoft.EventGrid/EventSubscriptions/Write** behörighet på resursen som krävs. Du behöver den här behörigheten eftersom du skriver en ny prenumeration på omfånget för resursen. Den begärda resursen skiljer sig beroende på om du prenumerera på ett system ämne eller ett anpassat ämne. Båda typerna beskrivs i det här avsnittet.

### <a name="system-topics-azure-service-publishers"></a>System-avsnitt (Azure-tjänsten utgivare)

För system ämnen behöver du behörighet att skriva en händelseprenumeration definitionsområdet för resursen publiceringen av händelsen. Formatet för resursen är: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Till exempel att prenumerera på en händelse på ett lagringskonto med namnet **MITTKONTO**, du måste ha behörighet för Microsoft.EventGrid/EventSubscriptions/Write på: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Anpassade ämnen

För anpassade ämnen behöver du behörighet att skriva en händelseprenumeration definitionsområdet för event grid-ämne. Formatet för resursen är: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Till exempel att prenumerera på ett anpassat ämne med namnet **mytopic**, du måste ha behörighet för Microsoft.EventGrid/EventSubscriptions/Write på: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>Avsnittet publicering

Avsnitt om använda signatur för delad åtkomst (SAS) eller nyckelautentisering. Vi rekommenderar SAS, men nyckelautentisering ger enkel programmering och är kompatibel med många befintliga webhook-utgivare. 

Du kan inkludera autentiseringsvärdet för i HTTP-huvudet. SAS, använda **aeg-sas-token** för huvudets värde. Nyckelautentisering, använda **aeg-sas-nyckeln** för huvudets värde.

### <a name="key-authentication"></a>Nyckelautentisering

Nyckelautentisering är den enklaste formen av autentisering. Använd formatet: `aeg-sas-key: <your key>`

Exempelvis kan skicka du en nyckel med:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-token

SAS-token för Event Grid är resursen, en förfallotid och en signatur. Formatet för SAS-token är: `r={resource}&e={expiration}&s={signature}`.

Resursen är sökvägen för event grid-ämne som du skickar händelser. Till exempel är en giltig resurssökväg: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Du kan skapa signaturen från en nyckel.

Till exempel en giltig **aeg-sas-token** värdet är:

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

## <a name="management-access-control"></a>Hantering av åtkomstkontroll

Azure Event Grid kan du kontrollera åtkomstnivån som ges till olika användare att utföra olika hanteringsåtgärder, till exempel listan händelseprenumerationer, skapa nya och generera nycklar. Event Grid använder Azures rollbaserad Kontrollera (RBAC).

### <a name="operation-types"></a>Åtgärdstyper

Azure event grid stöd följande åtgärder:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

De tre sista åtgärderna returnera potentiellt hemlig information, som hämtar filtrerade utanför normal läsåtgärder. Det är bäst för dig att begränsa åtkomsten till dessa åtgärder. Anpassade roller kan skapas med [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md), [Azure kommandoradsgränssnitt (CLI)](../role-based-access-control/role-assignments-cli.md), och [REST API](../role-based-access-control/role-assignments-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Framtvinga roll baserad åtkomstkontroll (RBAC)

Använd följande steg för att upprätthålla RBAC för olika användare:

#### <a name="create-a-custom-role-definition-file-json"></a>Skapa en anpassad roll-definitionsfil (.json)

Följande är exempel Event Grid rolldefinitioner som tillåter användare att utföra olika uppsättningar med åtgärder.

**EventGridReadOnlyRole.json**: Tillåt endast skrivskyddade åtgärder.

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

**EventGridNoDeleteListKeysRole.json**: Tillåt åtgärder begränsade efter Tillåt men inte ta bort åtgärder.

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

**EventGridContributorRole.json**: tillåter alla event grid-åtgärder.

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

#### <a name="create-and-assign-custom-role-with-azure-cli"></a>Skapa och tilldela anpassade roll med Azure CLI

Om du vill skapa en anpassad roll, använder du:

```azurecli
az role definition create --role-definition @<file path>
```

Om du vill tilldela rollen till en användare, använder du:

```azurecli
az role assignment create --assignee <user name> --role "<name of role>"
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [om Event Grid](overview.md)
