---
title: Använda GitHub-åtgärder med Azure App-konfigurationssynkronisering
description: Använda GitHub-åtgärder för att utlösa en uppdatering av appkonfigurationsinstansen när du uppdaterar GitHub-databasen
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46d4aa4d4d37e9cac928e8d1a9e5e77ca0f30f18
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384066"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Synkronisera en App Configuration-instans med GitHub Actions

Team som vill fortsätta använda sina befintliga källkontrollmetoder kan använda GitHub-åtgärder för att automatiskt synkronisera sin GitHub-databas med sitt App Configuration Store. På så sätt kan du göra ändringar i dina konfigurationsfiler som vanligt, samtidigt som du får fördelar för appkonfigurationen som: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Centraliserad konfiguration utanför koden <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Uppdatera konfigurationen utan att distribuera om hela appen <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integrering med tjänster som Azure App Service och funktioner. 

Ett [arbetsflöde](https://help.github.com/articles/about-github-actions#workflow) för GitHub-åtgärder definierar en automatiserad process i en GitHub-databas. *Azure App Configuration Sync* Action utlöser uppdateringar till en appkonfigurationsinstans när ändringar görs i källdatabasen. Den använder en YAML -fil (.yml) som finns i `/.github/workflows/` sökvägen till databasen för att definiera stegen och parametrarna. Du kan utlösa konfigurationsuppdateringar när du trycker, granskar eller förgrenar appkonfigurationsfiler på samma sätt som med appkod.

[GitHub-dokumentationen](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) ger en djupgående vy över GitHub-arbetsflöden och åtgärder. 

## <a name="enable-github-actions-in-your-repository"></a>Aktivera GitHub-åtgärder i databasen
Om du vill börja använda den här GitHub-åtgärden går du till databasen och väljer fliken **Åtgärder.** Klicka på **Nytt arbetsflöde**och konfigurera sedan ett **arbetsflöde själv**. Slutligen, sök på marknaden efter "Azure App Configuration Sync."
> [!div class="mx-imgBorder"]
> ![Välj fliken Åtgärd](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Välj åtgärd för synkronisering av appkonfiguration](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synkronisera konfigurationsfiler efter en push
Den här åtgärden synkroniserar Azure App Configuration-filer när en ändring skjuts till `appsettings.json`. När en utvecklare skickar `appsettings.json`en ändring till uppdaterar åtgärden App Configuration Sync appkonfiguration instansen med de nya värdena.

Det första avsnittet i det här arbetsflödet anger `appsettings.json` att åtgärden utlöser *på* en *push* som innehåller till *huvudgrenen.* I det andra avsnittet visas de jobb som körs när åtgärden har utlösts. Åtgärden checkar ut relevanta filer och uppdaterar appkonfigurationsinstansen med hjälp av anslutningssträngen som lagras som en hemlighet i databasen.  Mer information om hur du använder hemligheter i GitHub finns i [GitHubs artikel](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) om hur du skapar och använder krypterade hemligheter.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>Använda en dynamisk etikett vid synkronisering
Den föregående åtgärden uppdaterar appkonfigurationsinstansen när den `appsettings.json` uppdateras. Den här åtgärden infogar en dynamisk etikett på varje synkronisering, vilket säkerställer att varje synkronisering kan identifieras unikt och att kodändringar kan mappas till konfigurationsändringar.

Det första avsnittet i det här arbetsflödet anger `appsettings.json` att åtgärden utlöser *på* en *push* som innehåller till *huvudgrenen.* Det andra avsnittet kör ett jobb som skapar en unik etikett för konfigurationsuppdateringen baserat på commit-hashen. Jobbet uppdaterar sedan appkonfigurationsinstansen med de nya värdena och den unika etiketten för den här uppdateringen.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>Använd strikt synkronisering
När strikt läge är aktiverat säkerställer synkroniseringen att appkonfigurationsinstansen matchar konfigurationsfilen för det angivna prefixet och etiketten exakt. Nyckelvärdespar med samma prefix och etikett som inte finns i konfigurationsfilen tas bort. 
 
Om strikt läge inte är aktiverat anger synkroniseringen bara nyckelvärden från konfigurationsfilen. Inga nyckel-värde-par tas bort. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>Använd maxdjup för att begränsa GitHub-åtgärden
Standardbeteendet för kapslade JSON-attribut är att förenkla hela objektet.  JSON nedan definierar detta nyckelvärdespar:

| Nyckel | Värde |
| --- | --- |
| Objekt:Inre:InnerKey | InnerValue (innervärde) |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Om det kapslade objektet är avsett att vara det värde som skickas till konfigurationsinstansen kan du använda *djupvärdet* för att stoppa förenklingen på lämpligt djup. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Med tanke på ett djup av 2 returnerar exemplet ovan nu följande nyckelvärdespar:

| Nyckel | Värde |
| --- | --- |
| Objekt:Inre | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Förstå åtgärdsindata
Indataparametrar anger data som används av åtgärden under körning.  Följande tabell innehåller indataparametrar som accepteras av App Configuration Sync och de förväntade värdena för varje.  Mer information om åtgärdsindata för GitHub-åtgärder finns i GitHubs [dokumentation](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs).

> [!Note]
> Indata-ID:n är skiftlägesokänsliga.


| Indatanamn | Krävs? | Värde |
|----|----|----|
| konfigurationFil | Ja | Relativ sökväg till konfigurationsfilen i databasen.  Glob mönster stöds och kan innehålla flera filer. |
| format | Ja | Konfigurationsfilens filformat.  Giltiga format är: JSON, YAML, egenskaper. |
| Connectionstring | Ja | Anslutningssträng för appkonfigurationsinstansen. Anslutningssträngen ska lagras som en hemlighet i GitHub-databasen och endast det hemliga namnet ska användas i arbetsflödet. |
| Avgränsare | Ja | Separator som används när konfigurationsfilen förenklas till nyckelvärdespar.  Giltiga värden är: . , ; : - _ __ / |
| Prefix | Inga | Prefix som ska läggas till i början av nycklarna. |
| etikett | Inga | Etikett som används vid inställning av nyckelvärdespar. Om det inte anges används en null-etikett. |
| Strikt | Inga | Ett booleskt värde som avgör om strikt läge är aktiverat. Standardvärdet är false. |
| Djup | Inga | Maximalt djup för att förenkla konfigurationsfilen.  Djupet måste vara ett positivt tal.  Standardvärdet har inget maxdjup. |
| tags | Inga | Anger tagguppsättningen på nyckelvärdespar.  Det förväntade formatet är en strängform av ett JSON-objekt med följande form: { [propertyName: string]: string; } Varje egenskapsnamnvärde blir en tagg. |

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du veta mer om App Configuration Sync GitHub Action och hur den kan användas för att automatisera uppdateringar av appkonfigurationsinstansen. Om du vill veta hur Azure App Configuration reagerar på ändringar i nyckelvärdespar fortsätter du till nästa [artikel](./concept-app-configuration-event.md).
