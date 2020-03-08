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
ms.openlocfilehash: 0b7c5b42ac6291c6687337ba8d6a9d35830b9bda
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925777"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Autentisera åtkomst till Event Grid resurser

Azure Event Grid har tre typer av autentisering:

* WebHook-händelseleverans
* Prenumerationer på händelser
* Anpassat ämne publicering

## <a name="webhook-event-delivery"></a>WebHook-händelseleverans

Webhooks är en av de många sätt att ta emot händelser från Azure Event Grid. När en ny händelse är klar kan skickar Event Grid-tjänsten en HTTP-begäran till den konfigurera slutpunkten med händelsen i begärandetexten.

Liksom många andra tjänster som stöder webhooks måste Event Grid du bevisa ägarskapet för Webhook-slutpunkt innan den startar leverera händelser till denna slutpunkt. Det här kravet förhindrar att en obehörig användare överbelasta slutpunkten med händelser. När du använder någon av de tre Azure-tjänster som anges nedan, hanterar den här verifieringen automatiskt i Azure-infrastrukturen:

* Azure Logic Apps med [Event Grid koppling](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions med [Event Grid-utlösare](../azure-functions/functions-bindings-event-grid.md)

Om du använder någon annan typ av slutpunkt, t.ex. en HTTP-utlösare baserade Azure-funktion, måste din slutpunkt kod att delta i en verifiering handskakning med Event Grid. Event Grid stöder två olika sätt att verifiera prenumerationen.

1. **ValidationCode-handskakning (programmatisk)** : om du styr käll koden för din slut punkt rekommenderas den här metoden. Vid tidpunkten för händelsen prenumeration har skapats skickar Event Grid en händelse för verifiering av prenumeration till din slutpunkt. Schemat för den här händelsen är ungefär som andra Event Grid-händelse. Data delen av den här händelsen innehåller en `validationCode`-egenskap. Programmet kontrollerar att begäran om verifiering är för en förväntad händelse-prenumeration och ekar verifieringskoden till Event Grid. Den här mekanismen för handskakning stöds i alla Event Grid-versioner.

2. **ValidationURL hand skakning (manuellt)** : i vissa fall går det inte att komma åt käll koden för slut punkten för att implementera ValidationCode-handskakningen. Om du till exempel använder en tjänst från tredje part (t. ex. [Zapier](https://zapier.com) eller [ifttt](https://ifttt.com/)) kan du inte program mässigt svara med validerings koden.

   Från och med versionen 2018-05-01-preview, Event Grid har stöd för en manuell verifiering-handskakning. Om du skapar en händelse prenumeration med ett SDK eller verktyg som använder API-version 2018-05-01-Preview eller senare, skickar Event Grid en `validationUrl`-egenskap i data delen av prenumerations verifierings händelsen. För att slutföra handskakningen hitta URL: en i händelsedata och manuellt skickar en GET-begäran till den. Du kan använda antingen en REST-klient eller webbläsaren.

   Den angivna webb adressen är giltig i 5 minuter. Under denna tid är etablerings statusen för händelse prenumerationen `AwaitingManualAction`. Om du inte slutför den manuella verifieringen inom 5 minuter anges etablerings statusen till `Failed`. Du kommer behöva skapa händelseprenumeration igen innan du startar manuell verifiering.

    Den här autentiseringsmetoden kräver också att webhook-slutpunkten returnerar en HTTP-statuskod på 200 så att den vet att posten för verifierings händelsen accepterades innan den kan placeras i manuellt validerings läge. Med andra ord, om slut punkten returnerar 200 men inte returnerar ett verifierings svar program mässigt, överförs läget till det manuella validerings läget. Om validerings-URL: en visas inom 5 minuter anses verifierings hand skakningen vara lyckad.

> [!NOTE]
> Det finns inte stöd för att använda självsignerade certifikat för verifiering. Använd ett signerat certifikat från en certifikat utfärdare (CA) i stället.

### <a name="validation-details"></a>Verifieringsinformation

* Vid tidpunkten för händelsen skapande/uppdatering av prenumeration publicerar Event Grid en händelse för verifiering av prenumerationen till mål-slutpunkten. 
* Händelsen innehåller ett huvudvärde ”aeg Händelsetyp: SubscriptionValidation”.
* Händelsemeddelandet har samma schema som andra Event Grid-händelser.
* Händelsens eventType-egenskap är `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Egenskapen data i händelsen innehåller en `validationCode`-egenskap med en slumpvis genererad sträng. Till exempel ”validationCode: acb13...”.
* Händelse data innehåller också en `validationUrl`-egenskap med en URL för att manuellt verifiera prenumerationen.
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
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
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

Du måste returnera en HTTP 200 OK svars status kod. HTTP 202 accepterades inte som ett giltigt verifierings svar för Event Grid prenumeration. Http-begäran måste slutföras inom 30 sekunder. Om åtgärden inte slutförs inom 30 sekunder avbryts åtgärden och du kan försöka igen efter 5 sekunder. Om alla försöken Miss lyckas behandlas det som ett fel i validerings hand skakningen.

Eller så kan du verifiera manuellt prenumerationen genom att skicka en GET-begäran till URL: en för verifiering. Händelseprenumerationen kvar i ett väntande tillstånd tills verifieras. Verifierings-URL: en använder port 553. Om brand Väggs reglerna blockerar port 553 kan regler behöva uppdateras för lyckad hand skakning.

Ett exempel på hantering av hand skakningen för prenumerations validering finns i ett [ C# exempel](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Checklista

Om du ser ett fel meddelande under skapandet av händelse prenumerationen, till exempel "försöket att validera den angivna slut punkten https:\//Your-Endpoint-here misslyckades. Mer information finns på https:\//aka.ms/esvalidation ", det betyder att det är ett haveri i validerings hand skakningen. Lös felet genom att kontrollera följande aspekter:

* Kontrollerar du vilken program kod som körs i mål slut punkten? Till exempel om du skapar en HTTP-utlösare baserade Azure-funktion, har du åtkomst till programkod för att göra ändringar i den?
* Om du har åtkomst till programkoden kan implementera ValidationCode baserat handskakning mekanism som visas i exemplet ovan.

* Om du inte har åtkomst till programkoden (till exempel, om du använder en tredjepartstjänst som stöder webhooks) kan använda du mekanismen för manuell handskakning. Kontrollera att du använder 2018-05-01-preview API-versionen eller senare (installera tillägget för Event Grid Azure CLI) för att ta emot validationUrl i händelsen verifiering. Om du vill slutföra den manuella validerings hand skakningen hämtar du värdet för egenskapen `validationUrl` och besöker webb adressen i webbläsaren. Om verifieringen lyckas, bör du se ett meddelande i din webbläsare att valideringen har lyckats. Du ser att händelsen prenumerationens provisioningState är ”lyckades”. 

### <a name="event-delivery-security"></a>Säkerhet med händelser leverans

#### <a name="azure-ad"></a>Azure AD

Du kan skydda webhook-slutpunkten genom att använda Azure Active Directory för att autentisera och auktorisera Event Grid att publicera händelser till dina slut punkter. Du måste skapa ett Azure Active Directory-program, skapa en roll-och tjänst princip i ditt program som auktoriserar Event Grid och konfigurera händelse prenumerationen så att den använder Azure AD-programmet. [Lär dig hur du konfigurerar AAD med event Grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Frågeparametrar
Du kan skydda din webhook-slutpunkt genom att lägga till frågeparametrar webhook-URL när du skapar en händelseprenumeration. Ange en av dessa frågeparametrar [som en hemlighet](https://en.wikipedia.org/wiki/Access_token), till exempel en åtkomsttoken. Webhooken kan använda hemlighet för att identifiera händelsen kommer från Event Grid med giltig behörighet. Event Grid tas dessa Frågeparametrar i varje händelseleverans till webhooken.

När du redigerar händelse prenumerationen visas eller returneras inte frågeparametrar om parametern [--include-full-URL-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) används i Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Slutligen är det viktigt att notera att Azure Event Grid endast stöder HTTPS webhook-slutpunkter.

## <a name="event-subscription"></a>Händelseprenumeration

Du måste bekräfta att du har åtkomst till händelsekällan och hanterare för att prenumerera på en händelse. Bevisar att du äger en WebHook har beskrivs i föregående avsnitt. Om du använder en händelsehanterare som inte är en WebHook (till exempel en event hub eller queue storage), måste skrivbehörighet till resursen. Den här behörighetskontrollen kan obehöriga användare från att skicka händelser till din resurs.

Du måste ha behörigheten **Microsoft. EventGrid/EventSubscriptions/Write** på den resurs som är händelse källa. Du behöver den här behörigheten eftersom du skriver en ny prenumeration på omfånget för resursen. Den begärda resursen skiljer sig beroende på om du prenumerera på ett system ämne eller ett anpassat ämne. Båda typerna beskrivs i det här avsnittet.

### <a name="system-topics-azure-service-publishers"></a>System-avsnitt (Azure-tjänsten utgivare)

För system ämnen behöver du behörighet att skriva en händelseprenumeration definitionsområdet för resursen publiceringen av händelsen. Resursens format är: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Om du till exempel vill prenumerera på en händelse på ett lagrings konto med namnet **acct**, behöver du behörigheten Microsoft. EventGrid/EventSubscriptions/Write på: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Anpassade ämnen

För anpassade ämnen behöver du behörighet att skriva en händelseprenumeration definitionsområdet för event grid-ämne. Resursens format är: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Om du till exempel vill prenumerera på ett anpassat ämne med namnet " **ämne**", behöver du ha behörigheten Microsoft. EventGrid/EventSubscriptions/Write på: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Anpassat ämne publicering

Anpassade ämnen använda signatur för delad åtkomst (SAS) eller nyckelautentisering. Vi rekommenderar SAS, men nyckelautentisering ger enkel programmering och är kompatibel med många befintliga webhook-utgivare. 

Du kan inkludera autentiseringsvärdet för i HTTP-huvudet. För SAS använder du **AEG-SAS-token** för Head-värdet. För Key Authentication använder du **AEG-SAS-Key** för huvudets värde.

### <a name="key-authentication"></a>Nyckelautentisering

Nyckelautentisering är den enklaste formen av autentisering. Använd formatet: `aeg-sas-key: <your key>`

Exempelvis kan skicka du en nyckel med:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS-token

SAS-token för Event Grid är resursen, en förfallotid och en signatur. Formatet för SAS-token är: `r={resource}&e={expiration}&s={signature}`.

Resursen är sökvägen för event grid-ämne som du skickar händelser. Till exempel är en giltig resurs Sök väg: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Du kan skapa signaturen från en nyckel.

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

## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [About Event Grid](overview.md)
