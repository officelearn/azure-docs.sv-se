---
title: Asynkron uppdatering för Azure Analysis Services-modeller | Microsoft Docs
description: Lär dig hur du kodar asynkron uppdatering med hjälp av REST API.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f10bae780ebb05d3450f4dab7e53fa87fe25b022
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189561"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Asynkron uppdatering med REST API

Genom att använda valfritt programmeringsspråk som har stöd för REST-anrop kan utföra du asynkrona datauppdateringsåtgärder på din Azure Analysis Services-tabellmodeller. Detta inkluderar synkronisering av skrivskyddade repliker för frågeutskalning. 

Datauppdateringsåtgärder kan ta lite tid beroende på ett antal faktorer, bland annat datavolym för optimering med partitioner etc. De här åtgärderna traditionellt har anropats med befintliga metoder, till exempel med hjälp av [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Tabular Object Model), [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) cmdlet: ar, eller [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (Tabular-modell Scripting Language). Dessa metoder kräver dock ofta otillförlitliga, långvariga HTTP-anslutningar.

REST-API för Azure Analysis Services kan datauppdateringsåtgärder utföras asynkront. Med hjälp av REST-API är tidskrävande HTTP-anslutningar från klientprogram inte behövs. Det finns även andra inbyggda funktioner för tillförlitlighet, bland annat automatiska återförsök och gruppbaserade incheckningar.

## <a name="base-url"></a>Grundläggande URL

Den grundläggande Webbadressen följer det här formatet:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Anta exempelvis att en modell med namnet AdventureWorks på en server med namnet myserver, finns i västra USA Azure-region. Servernamnet är:

```
asazure://westus.asazure.windows.net/myserver 
```

Den grundläggande Webbadressen för det här Servernamnet är:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Genom att använda en bas-URL kan kan resurser och åtgärder läggas utifrån följande parametrar: 

![Asynkron uppdatering](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Allt som slutar med **s** är en samling.
- Allt som slutar med **()** är en funktion.
- Allt annat är en resurs /-objekt.

Du kan till exempel använda verbet INLÄGG på samlingen uppdateras för att utföra en uppdatering:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autentisering

Alla anrop måste autentiseras mot en giltig Azure Active Directory (OAuth 2)-token i auktoriseringshuvudet och måste uppfylla följande krav:

- Token måste vara antingen en användartoken eller ett tjänstobjekt för programmet.
- Token måste ha rätt målgrupp inställd `https://*.asazure.windows.net`.
- Användarens eller programmets måste ha tillräcklig behörighet på servern eller modell för att göra det begärda anropet. Behörighetsnivå bestäms av roller i modellen eller administratörsgruppen på servern.

    > [!IMPORTANT]
    > För närvarande **serveradministratör** rollbehörighet krävs.

## <a name="post-refreshes"></a>POST /refreshes

Om du vill utföra en uppdatering, använder du INLÄGGET verbet i den /refreshes-samlingen att lägga till ett nytt uppdatera objekt i samlingen. Location-huvudet i svaret innehåller uppdatering-ID. Klientprogrammet kan koppla från och kontrollera status senare om det behövs eftersom det är asynkrona.

Endast en uppdateringsåtgärden accepteras i taget för en modell. Om det finns en aktuell åtgärd som körs uppdatering och en annan skickas, returneras 409 konflikt HTTP-statuskoden.

Brödtexten kan likna följande:

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

Att ange parametrar är inte obligatoriskt. Standard tillämpas.

|Namn  |Typ  |Beskrivning  |Standard  |
|---------|---------|---------|---------|
|Typ     |  Enum       |  Typ av bearbetning som ska utföras. Typerna ligger i linje med TMSL [kommandot Uppdatera](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) typer: full, clearValues, beräkna, dataOnly, automatisk, och defragmentera. Lägg till typen inte stöds.      |   Automatisk      |
|CommitMode     |  Enum       |  Anger om objekt genomförs i batchar eller bara när du är klar. Inkludera lägen: standard transaktioner partialBatch.  |  transaktionell       |
|MaxParallelism     |   Int      |  Det här värdet anger det maximala antalet trådar som ska köras bearbetningskommandon parallellt. Det här värdet i linje med egenskapen MaxParallelism som kan ställas in i TMSL [Sekvensera kommandot](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) eller använda andra metoder.       | 10        |
|RetryCount    |    Int     |   Anger hur många gånger som åtgärden kommer att försöka igen innan åtgärden misslyckas.      |     0    |
|Objekt     |   Matris      |   En matris med objekt som ska bearbetas. Varje objekt innehåller: ”tabell” vid bearbetning av hela tabellen eller ”tabell” och ”partition” vid bearbetning av en partition. Om inga objekt har angetts, uppdateras hela modellen. |   Bearbeta hela modellen      |

CommitMode är lika med partialBatch. Den används när du gör en inledande inläsningen av stora datauppsättningar kan ta timmar. Om uppdateringen misslyckas efter att transaktionen har en eller flera batchar har bekräftats batchar förblir allokerade (den återställer inte har bekräftats batchar).

> [!NOTE]
> Batchstorleken är MaxParallelism värdet vid tidpunkten för skrivning, men det här värdet kan ändra.

## <a name="get-refreshesrefreshid"></a>GET-/refreshes/\<refreshId >

Om du vill kontrollera status för en uppdatering, använder du GET-verbet på Uppdatera-ID. Här är ett exempel på svarstexten. Om åtgärden pågår **inProgress** returneras i status.

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

Om du vill hämta en lista över historiska uppdateringsåtgärder för en modell, använder du GET-verbet i /refreshes-samling. Här är ett exempel på svarstexten. 

> [!NOTE]
> När skrivs de senaste 30 dagarna av uppdateringsåtgärder lagras och returneras, men det här talet kan ändra.

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

Om du vill avbryta en pågående uppdateringsåtgärden använder du ta bort verbet på Uppdatera-ID.

## <a name="post-sync"></a>POST/Sync

Genom att utföra uppdateringsåtgärder, kan det vara nödvändigt att synkronisera nya data med repliker för frågeutskalning. Använd verbet INLÄGG på/Sync-funktionen om du vill utföra en synkroniseringsåtgärden för en modell. Location-huvudet i svaret innehåller sync åtgärds-ID.

## <a name="get-sync-status"></a>Hämta/Sync-status

Använd GET-verbet skicka åtgärds-ID som en parameter för att kontrollera status för en synkroniseringsåtgärden. Här är ett exempel på svarstexten:

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

- 0: Replikerar. Databasfilerna replikeras till en målmapp.
- 1: Återställning. Databasen extraheras på skrivskyddade server-instanser.
- 2: Har slutförts. Synkroniseringsåtgärden har slutförts.
- 3: Misslyckades. Det gick inte att synkronisera.
- 4: Slutför. Synkroniseringsåtgärden slutfördes men utför Rensa.

## <a name="code-sample"></a>Kodexempel

Här är ett C#-kodexempel som hjälper dig igång, [RestApiSample på GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Du använder kodexemplet

1.  Klona eller ladda ned lagringsplatsen. Öppna RestApiSample lösningen.
2.  Leta reda på raden **klienten. BaseAddress =...** och ange din [bas-URL](#base-url).

Kodexemplet kan använda interaktiv inloggning, användarnamn/lösenord eller [tjänstens huvudnamn](#service-principal).

#### <a name="interactive-login-or-usernamepassword"></a>Interaktiv inloggning eller användarnamn/lösenord

Den här typen av autentisering kräver ett Azure-program skapas med nödvändiga API-behörigheter. 

1.  I Azure-portalen klickar du på **skapa en resurs** > **Azure Active Directory** > **appregistreringar**  >   **Ny programregistrering**.

    ![Ny programregistrering](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  I **skapa**, anger du ett namn, Välj **interna** programtypen. För **omdirigerings-URI**, ange **urn: ietf:wg:oauth:2.0:oob**, och klicka sedan på **skapa**.

    ![Inställningar](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Välj din app och sedan kopiera och spara den **program-ID**.

    ![Kopiera program-ID](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  I **inställningar**, klickar du på **nödvändiga behörigheter** > **Lägg till**.

    ![Lägg till API-åtkomst](./media/analysis-services-async-refresh/aas-async-add.png)

5.  I **Välj en API**, typ **Azure Analysis Services** i sökfältet och väljer den.

    ![Välj API](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Välj **läsa och skriva alla modeller**, och klicka sedan på **Välj**. När båda alternativen är markerade, klickar du på **klar** att lägga till behörigheter. Det kan ta några minuter att sprida.

    ![Välj läsa och skriva alla modeller](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  I kodexemplet, hitta den **UpdateToken()** metod. Se innehållet i den här metoden.
8.  Hitta **sträng clientID =...** , och ange sedan den **program-ID** du kopierade från steg 3.
9.  Kör exemplet.

#### <a name="service-principal"></a>Tjänstens huvudnamn

Se [skapa tjänstens huvudnamn – Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md) och [lägga till tjänstens huvudnamn till administratörsrollen för servern](analysis-services-addservprinc-admins.md) för mer information om hur du konfigurerar ett tjänstobjekt och tilldela nödvändiga behörigheter i Azure som . När du har slutfört stegen, utför följande åtgärder:

1.  I kodexemplet, hitta **sträng authority =...** , Ersätt **vanliga** med organisationens klient-ID.
2.  Kommenterar/avkommenterar så att klassen ClientCredential används för att skapa en instans av objektet Använ. Se till att den \<App-ID > och \<Appnyckeln > värden används på ett säkert sätt eller använda certifikatbaserad autentisering för tjänstens huvudnamn.
3.  Kör exemplet.


## <a name="see-also"></a>Se också

[Exempel](analysis-services-samples.md)   
[REST-API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


