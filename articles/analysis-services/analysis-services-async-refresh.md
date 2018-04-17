---
title: Asynkron uppdatering för Azure Analysis Services-modeller | Microsoft Docs
description: Lär dig hur du kodar asynkron uppdatering med hjälp av REST API.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 74ef8ae45215badf2b5a83cc2d82c3db1eef8980
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Asynkron uppdatering med REST API
Genom att använda alla programmeringsspråk som har stöd för REST-anrop kan utföra du datauppdatering asynkrona åtgärder på Azure Analysis Services-tabellmodeller. Detta inkluderar synkronisering av skrivskyddade repliker för frågan skalbar. 

Datauppdatering åtgärder kan ta lite tid beroende på ett antal faktorer, bland annat datavolym för optimering med partitioner etc. Dessa åtgärder traditionellt har anropats med befintliga metoder som använder [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Tabular Object Model) [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) cmdlets eller [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (Tabellmodell Skript Language). Dessa metoder kräver dock ofta instabilt, långvariga HTTP-anslutningar.

REST-API: et för Azure Analysis Services kan datauppdatering åtgärder utföras asynkront. Med hjälp av REST API är långvariga HTTP-anslutningar från klientprogram inte behövs. Det finns även andra inbyggda funktioner för tillförlitlighet, till exempel automatiska omförsök och gruppbaserad genomföranden.

## <a name="base-url"></a>Grundläggande URL

Den grundläggande Webbadressen följer det här formatet:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Anta till exempel att en modell med namnet AdventureWorks på servern minserver, finns i West US Azure-region. Servernamnet är:

```
asazure://westus.asazure.windows.net/myserver 
```

Bas-URL för det här Servernamnet är:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Genom att använda en bas-URL, resurser och åtgärder som kan läggas baserat på följande parametrar: 

![Async-uppdatering](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Allt som slutar i **s** är en samling.
- Allt som slutar med **()** är en funktion.
- Allt annat är en resursobjektet.

Du kan till exempel använda verbet POST på samlingen uppdateras för att utföra en uppdatering:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autentisering

Alla anrop måste autentiseras med en giltig Azure Active Directory (OAuth 2)-token i auktoriseringshuvudet och uppfylla följande krav:

- Token måste vara en användartoken eller ett program tjänstens huvudnamn.
- Token måste ha rätt målgrupp inställd på `https://*.asazure.windows.net`.
- Användaren eller programmet måste ha tillräckliga behörigheter på servern eller i modellen för att begärda ringa. Behörighetsnivån bestäms av roller i modellen eller administratörsgruppen på servern.

    > [!IMPORTANT]
    > För närvarande **serveradministratören** rollbehörigheter krävs.

## <a name="post-refreshes"></a>Bokför /refreshes

Om du vill utföra en uppdatering, använder du verbet POST på samlingen /refreshes att lägga till ett nytt uppdatera objekt i samlingen. Plats-huvudet i svaret innehåller uppdatera-ID. Klientprogrammet kan koppla från och kontrollera status senare vid behov eftersom den är asynkron.

Endast en uppdateringsåtgärden accepteras samtidigt för en modell. Om det finns en aktuell körs uppdateringsåtgärden och en annan skickas, returneras 409 konflikt HTTP-statuskoden.

Innehållet kan likna följande:

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
Det är inte nödvändigt att ange parametrar. Standardvärdet används.

|Namn  |Typ  |Beskrivning  |Standard  |
|---------|---------|---------|---------|
|Typ     |  Enum       |  Typ av bearbetning som ska utföras. Typerna ligger i linje med TMSL [kommandot Uppdatera](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) typer: full, clearValues, beräkna dataOnly, automatisk, lägga till och defragmentera.       |   Automatisk      |
|CommitMode     |  Enum       |  Anger om objekt sparas i batchar eller bara när du är klar. Lägen omfattar: standard transactional, partialBatch.  |  transaktionell       |
|MaxParallelism     |   Int      |  Det här värdet anger det maximala antalet trådar som ska köras bearbetning kommandon parallellt. Det här värdet justerad med egenskapen MaxParallelism som kan anges i TMSL [aktivitetssekvensen kommandot](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) eller använda andra metoder.       | 10        |
|retryCount    |    Int     |   Anger antalet gånger som kommer att försöka igen innan åtgärden misslyckas.      |     0    |
|Objekt     |   Matris      |   En matris med objekt som ska bearbetas. Varje objekt omfattar: ”table” vid bearbetning av hela tabellen eller ”table” och ”partition” vid bearbetning av en partition. Om inga objekt har angetts uppdateras hela modellen. |   Bearbeta hela modellen      |

CommitMode är lika med partialBatch. Den används när du gör en inledande inläsningen av stora datamängder som kan ta timmar. Om uppdateringen misslyckas när du utför en eller flera batchar har skickats batchar förblir allokerat (det kan inte återställa har bekräftats batchar).

> [!NOTE]
> Batchstorleken är MaxParallelism värdet vid tiden för skrivning, men det här värdet kan ändras.

## <a name="get-refreshesrefreshid"></a>GET-/refreshes/\<refreshId >

Om du vill kontrollera status för en uppdatering, använder du GET-verbet på Uppdatera-ID. Här är ett exempel på brödtext för svar. Om åtgärden pågår **inProgress** returneras status.

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

## <a name="get-refreshes"></a>Hämta /refreshes

Om du vill hämta en lista över historiska uppdateringsåtgärder för en modell, använder du GET-verbet i /refreshes-samling. Här är ett exempel på brödtext för svar. 

> [!NOTE]
> Vid tiden för skrivning de senaste 30 dagarna av uppdateringsåtgärder lagras och returneras, men det här antalet kan ändras.

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

## <a name="delete-refreshesrefreshid"></a>Ta bort /refreshes/\<refreshId >

Om du vill avbryta en pågående uppdatering använder du ta bort verbet på Uppdatera-ID.

## <a name="post-sync"></a>POST/Sync

Utföra av uppdateringsåtgärder vara det nödvändigt att synkronisera nya data med repliker för frågan skalbar. Om du vill utföra en synkronisering för en modell, använder du verbet POST för/Sync-funktionen. Plats-huvudet i svaret innehåller sync åtgärds-ID.

## <a name="get-sync-status"></a>Hämta/Sync-status

Använd GET-verbet skickar åtgärds-ID som en parameter för att kontrollera status för en synkroniseringsåtgärd. Här är ett exempel på brödtext för svar:

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

Värden för `syncstate`:

- 0: replikeras. Databasfilerna replikeras till en målmapp.
- 1: återställning. Databasen är att återställd på skrivskyddad server-instanser.
- 2: slutförts. Synkronisera åtgärden har slutförts.
- 3: misslyckades. Det gick inte att synkronisera.
- 4: Slutför. Sync-åtgärden har slutförts men utförs Rensa.

## <a name="code-sample"></a>Kodexempel

Här är en C# kodexempel för att komma igång, [RestApiSample på GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Att använda kodexemplet

1.  Klona eller hämta lagringsplatsen. Öppna RestApiSample-lösningen.
2.  Hitta raden **klienten. BaseAddress =...** och ange din [bas-URL](#base-url).

Kodexemplet kan använda interaktiv inloggning, användarnamn/lösenord eller [tjänstens huvudnamn](#service-principle).

#### <a name="interactive-login-or-usernamepassword"></a>Interaktiv inloggning eller användarnamn/lösenord

Den här typen av autentisering kräver ett Azure-program skapas med de behörigheter som krävs av API tilldelad. 

1.  I Azure-portalen klickar du på **skapar du en resurs** > **Azure Active Directory** > **App registreringar**  >   **Nya appregistrering**.

    ![Ny appregistrering](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  I **skapa**, ange ett namn, Välj **interna** programtyp. För **omdirigerings-URI**, ange **urn: ietf:wg:oauth:2.0:oob**, och klicka sedan på **skapa**.

    ![Inställningar](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Markera appen och sedan kopiera och spara den **program-ID**.

    ![Kopiera program-ID](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  I **inställningar**, klickar du på **nödvändiga behörigheter** > **Lägg till**.

    ![Lägg till API-åtkomst](./media/analysis-services-async-refresh/aas-async-add.png)

5.  I **väljer en API**, typen **Azure Analysis Services** i sökningen och välj sedan den.

    ![Välj API](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Välj **läsa och skriva alla modeller**, och klicka sedan på **Välj**. När båda är markerade klickar du på **klar** att lägga till behörigheter. Det kan ta några minuter att sprida.

    ![Välj läsa och skriva alla modeller](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  I kodexemplet kan hitta den **UpdateToken()** metod. Se innehållet i den här metoden.
8.  Hitta **string clientID =...** , och ange sedan den **program-ID** du kopierade från steg 3.
9.  Kör exemplet.

#### <a name="service-principal"></a>Tjänstens huvudnamn

Se [skapa service - principen i Azure-portalen](../azure-resource-manager/resource-group-create-service-principal-portal.md) och [lägga till en princip för tjänsten i serveradministratörsrollen](analysis-services-addservprinc-admins.md) för mer information om hur du ställer in ett huvudnamn för tjänsten och tilldela behörighet i Azure som . När du har slutfört stegen, utför följande åtgärder:

1.  I kodexemplet kan hitta **string myndigheten =...** , Ersätt **vanliga** med organisationens klient-ID.
2.  Kommentar/ta bort kommentarerna så ClientCredential klassen används för att initiera Använ-objekt. Se till att den \<App-ID > och \<Appkey > värden används på ett säkert sätt eller använda certifikatbaserad autentisering för tjänstens huvudnamn.
3.  Kör exemplet.


## <a name="see-also"></a>Se också

[Exempel](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


