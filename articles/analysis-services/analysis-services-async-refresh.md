---
title: Asynkron uppdatering för Azure Analysis Services-modeller | Microsoft-dokument
description: Beskriver hur du använder REST-API:ET för Azure Analysis Services för att koda asynkron uppdatering av modelldata.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6457f062a40e60a491220fcf977585e8b07445b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273723"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Asynkron uppdatering med REST API

Genom att använda programmeringsspråk som stöder REST-anrop kan du utföra asynkrona datauppdateringsåtgärder på dina Azure Analysis Services-tabellmodeller. Detta inkluderar synkronisering av skrivskyddade repliker för frågeskalning. 

Data-uppdateringsåtgärder kan ta lite tid beroende på ett antal faktorer, inklusive datavolym, optimeringsnivå med hjälp av partitioner, etc. Dessa åtgärder har traditionellt anropats med befintliga metoder som att använda [TOM](https://docs.microsoft.com/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (tabellobjektmodell), PowerShell-cmdlets eller [TMSL](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (Tabellmodellskriptspråk). [PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) Dessa metoder kan dock kräva ofta otillförlitliga, långvariga HTTP-anslutningar.

REST API för Azure Analysis Services gör att datauppdateringsåtgärder kan utföras asynkront. Med REST API är långvariga HTTP-anslutningar från klientprogram inte nödvändiga. Det finns också andra inbyggda funktioner för tillförlitlighet, till exempel automatiska återförsök och batchade åtaganden.

## <a name="base-url"></a>Grundläggande URL

Bas-URL:en följer det här formatet:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Tänk dig till exempel en modell med `myserver`namnet AdventureWorks på en server som heter , som finns i regionen Västra USA Azure. Servernamnet är:

```
asazure://westus.asazure.windows.net/myserver 
```

Bas-URL:en för det här servernamnet är:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Med hjälp av bas-URL:en kan resurser och åtgärder läggas till baserat på följande parametrar: 

![Async-uppdatering](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Allt som slutar i **S** är en samling.
- Allt som slutar med **()** är en funktion.
- Allt annat är en resurs/ett objekt.

Du kan till exempel använda POST-verbet i samlingen Uppdatera uppdateringar för att utföra en uppdateringsåtgärd:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autentisering

Alla anrop måste autentiseras med en giltig Azure Active Directory-token (OAuth 2) i auktoriseringshuvudet och måste uppfylla följande krav:

- Token måste vara antingen en användartoken eller ett programtjänsthuvudnamn.
- Token måste ha rätt målgrupp `https://*.asazure.windows.net`inställd på .
- Användaren eller programmet måste ha tillräcklig behörighet på servern eller modellen för att kunna ringa det begärda samtalet. Behörighetsnivån bestäms av roller inom modellen eller administratörsgruppen på servern.

    > [!IMPORTANT]
    > För **server admin** närvarande är serveradministratörsrollbehörigheter nödvändiga.

## <a name="post-refreshes"></a>POST /uppdatera

Om du vill utföra en uppdateringsåtgärd använder du POST-verbet i samlingen /refreshes för att lägga till ett nytt uppdateringsobjekt i samlingen. Platshuvudet i svaret innehåller uppdaterings-ID: et. Klientprogrammet kan koppla från och kontrollera status senare om det behövs eftersom det är asynkront.

Endast en uppdateringsåtgärd accepteras åt gången för en modell. Om det finns en aktuell uppdateringsåtgärd som körs och en annan skickas returneras 409:s STATUS-statuskod för konflikt http.

Kroppen kan likna följande:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parametrar

Det går inte att ange parametrar. Standardvärdet tillämpas.

| Namn             | Typ  | Beskrivning  |Default  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | Den typ av bearbetning som ska utföras. Typerna är justerade med [TMSL-uppdateringskommandotyperna:](https://docs.microsoft.com/analysis-services/tmsl/refresh-command-tmsl) fullständig, clearValues, calculate, dataOnly, automatic och defragment. Lägg till typ stöds inte.      |   automatiskt      |
| `CommitMode`     | Enum  | Avgör om objekt ska bekräftas i batchar eller endast när de är klara. Lägen inkluderar: standard, transaktionell, partialBatch.  |  Transaktionella       |
| `MaxParallelism` | Int   | Det här värdet bestämmer det maximala antalet trådar som bearbetningskommandon ska köras parallellt. Det här värdet justeras mot egenskapen MaxParallelism som kan anges i kommandot TMSL [Sequence](https://docs.microsoft.com/analysis-services/tmsl/sequence-command-tmsl) eller med andra metoder.       | 10        |
| `RetryCount`     | Int   | Anger hur många gånger åtgärden ska försöka igen innan den misslyckas.      |     0    |
| `Objects`        | Matris | En matris med objekt som ska bearbetas. Varje objekt innehåller: "tabell" vid bearbetning av hela tabellen eller "tabell" och "partition" vid bearbetning av en partition. Om inga objekt anges uppdateras hela modellen. |   Bearbeta hela modellen      |

CommitMode är lika med partialBatch. Den används när du gör en första belastning av stora datauppsättningar som kan ta timmar. Om uppdateringsåtgärden misslyckas efter att en eller flera batchar har genomförts kommer de slutförda batcharna att fortsätta att bekräftas (batchar återställs inte.

> [!NOTE]
> I skrivande stund är batchstorleken maxparallelism-värdet, men det här värdet kan ändras.

### <a name="status-values"></a>Statusvärden

|Statusvärde  |Beskrivning  |
|---------|---------|
|`notStarted`    |   Åtgärden har ännu inte startats.      |
|`inProgress`     |   Operation pågår.      |
|`timedOut`     |    Timeout för åtgärden tidsgränsen baserat på den angivna tidsgränsen för användaren.     |
|`cancelled`     |   Åtgärden avbröts av användaren eller systemet.      |
|`failed`     |   Åtgärden misslyckades.      |
|`succeeded`      |   Åtgärden lyckades.      |

## <a name="get-refreshesrefreshid"></a>GET /refreshes/refreshId\<>

Om du vill kontrollera status för en uppdateringsåtgärd använder du GET-verbet på uppdaterings-ID: et. Här är ett exempel på svarsorganet. Om åtgärden pågår returneras `inProgress` status.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /refreshes

Om du vill hämta en lista över historiska uppdateringsåtgärder för en modell använder du GET-verbet i samlingen /refreshes. Här är ett exempel på svarsorganet. 

> [!NOTE]
> I skrivande stund lagras och returneras de senaste 30 dagarnas uppdateringsåtgärder, men det här numret kan ändras.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>TA BORT /uppdatera/uppdateraId\<>

Om du vill avbryta en pågående uppdatering använder du DELETE-verbet på uppdaterings-ID: et.

## <a name="post-sync"></a>POST /sync

Efter att ha utfört uppdateringsåtgärder kan det vara nödvändigt att synkronisera de nya data med repliker för frågeskalning. Om du vill utföra en synkroniseringsåtgärd för en modell använder du POST-verbet på funktionen /sync. Platshuvudet i svaret innehåller synkroniseringsåtgärds-ID.

## <a name="get-sync-status"></a>GET /sync-status

Om du vill kontrollera status för en synkroniseringsåtgärd använder du GET-verbet som skickar åtgärds-ID:et som en parameter. Här är ett exempel på svarstexten:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Värden `syncstate`för :

- 0: Replikera. Databasfiler replikeras till en målmapp.
- 1: Rehydrating. Databasen rehydreras på skrivskyddade serverinstanser.
- 2: Avslutad. Synkroniseringsåtgärden har slutförts.
- 3: Misslyckades. Synkroniseringsåtgärden misslyckades.
- 4: Slutföra. Synkroniseringsåtgärden har slutförts men utför rensningssteg.

## <a name="code-sample"></a>Kodexempel

Här är ett C#-kodexempel för att komma igång, [RestApiSample på GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Så här använder du kodexemplet

1.  Klona eller ladda ner repo. Öppna RestApiSample-lösningen.
2.  Hitta **linjeklienten. BaseAddress = ...** och ange din [bas-URL](#base-url).

Kodexemplet använder [autentisering av tjänstens huvudnamn.](#service-principal)

### <a name="service-principal"></a>Tjänstens huvudnamn

Se [Skapa tjänsthuvudnamn - Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md) och [Lägg till ett tjänsthuvudnamn till serveradministratörsrollen](analysis-services-addservprinc-admins.md) för mer information om hur du konfigurerar ett tjänsthuvudnamn och tilldelar nödvändiga behörigheter i Azure AS. När du har slutfört stegen gör du följande ytterligare steg:

1.  I kodexemplet letar du reda på **strängutfärdaren = ...**, **ersätter vanligt** med organisationens klient-ID.
2.  Kommentar/avkommentar så klassen ClientCredential används för att instansiera cred-objektet. Kontrollera \<att app-ID-> och \<App Key> värden används på ett säkert sätt eller använd certifikatbaserad autentisering för tjänsthuvudnamn.
3.  Kör exemplet.


## <a name="see-also"></a>Se även

[Prover](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


