---
title: Använda GitHub-åtgärder med Azure App konfigurations synkronisering
description: Använd GitHub-åtgärder för att utlösa en uppdatering av konfigurations instansen när definierade åtgärder utförs på en GitHub-lagringsplats
author: jpconnock
ms.author: jeconnoc
ms.date: 01/14/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 52fa9a94d86dac2d49b078f98aaa494fbebf52d6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294453"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Synkronisera konfigurations instansen för din app med GitHub-åtgärder
Azure App konfiguration använder GitHub-åtgärder för att uppdatera en konfigurations instans för appar när den utlöses av en åtgärd som utförs på en GitHub-lagringsplats. Du kan utnyttja GitHub-arbetsflöden för att uppdatera app-konfigurationen, så att du kan integrera uppdateringar av app-konfigurationen i samma arbets flöde som används för att uppdatera app-koden.

Ett [arbets flöde](https://help.github.com/articles/about-github-actions#workflow) för GitHub-åtgärder är en automatiserad process som definierats i din GitHub-lagringsplats. Den här processen visar GitHub hur du skapar och distribuerar ditt GitHub-projekt. Azure App-konfigurationen tillhandahåller åtgärden *Azure App konfigurations synkronisering* för att aktivera uppdateringar till en app Configuration-instans när ändringar görs i käll lagrings platsen. 

Ett arbets flöde definieras av en YAML-fil (. yml) som finns i `/.github/workflows/` sökvägen till lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som definierar arbets flödet.

GitHub-händelser, till exempel en push-överföring till en lagrings plats, kan utlösa ett GitHub åtgärds arbets flöde.  Azure tillhandahåller åtgärden *Azure App Sync-konfiguration* som gör att du kan utlösa en uppdatering av en app Configuration-instans när en angiven GitHub-åtgärd utförs. Detta gör att team kan utnyttja GitHubs kärn funktioner när de skickar, granska eller förgrena appars konfigurationsfiler på samma sätt som med app-kod.

GitHub- [dokumentationen](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) innehåller djupgående visning av GitHub-arbetsflöden och åtgärder. 

## <a name="enable-github-actions-in-your-repository"></a>Aktivera GitHub-åtgärder i din lagrings plats
Om du vill börja använda den här GitHub-åtgärden går du till din lagrings plats och väljer fliken **åtgärder** . Leta upp och Välj åtgärden GitHub i Marketplace genom att söka efter "Azure App konfigurations synkronisering". 

> [!div class="mx-imgBorder"]
> ![väljer fliken åtgärd](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![välja syn åtgärd för konfiguration av app-konfiguration](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synkronisera konfigurationsfiler efter en push
Den här åtgärden synkroniserar Azure App konfigurationsfiler när en ändring flyttas till `appsettings.json`. När en utvecklare gör och skickar en ändring till `appsettings.json`uppdaterar synkroniseringen av app-konfigurations instansen med de nya värdena.

Det första avsnittet av det här arbets flödet anger att åtgärden utlöses *på* en *push* som innehåller `appsettings.json` till *huvud* grenen. I det andra avsnittet visas jobben som körs när åtgärden har Aktiver ATS. Åtgärden kontrollerar de relevanta filerna och uppdaterar konfigurations instansen för appen med hjälp av anslutnings strängen som lagras som en hemlighet i lagrings platsen.  Mer information om hur du använder hemligheter i GitHub finns i [den här artikeln](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) om hur du skapar och använder krypterade hemligheter.

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

## <a name="use-a-dynamic-label-on-sync"></a>Använd en dynamisk etikett vid synkronisering
Föregående åtgärd uppdaterade bara konfigurations instansen när `appsettings.json` uppdateras. Den här åtgärden infogar en dynamisk etikett vid varje synkronisering, vilket säkerställer att varje synkronisering kan identifieras unikt.  Detta gör att kod ändringar snabbt kan mappas till konfigurations ändringar.

Det första avsnittet av det här arbets flödet anger att åtgärden utlöses *på* en *push* som innehåller `appsettings.json` till *huvud* grenen. Det andra avsnittet kör ett jobb som skapar en unik etikett för konfigurations uppdateringen baserat på commit hash. Jobbet uppdaterar sedan appens konfigurations instans med de nya värdena och den unika etiketten för uppdateringen.

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
När strikt läge har Aktiver ATS säkerställer synkroniseringen att konfigurations instansen för appen matchar konfigurations filen för angivet prefix och etiketten exakt. Nyckel/värde-par med samma prefix och etikett som inte finns i konfigurations filen tas bort. 
 
Om strikt läge inte är aktiverat kommer synkroniseringen bara att ange nyckel värden från konfigurations filen. Inga nyckel/värde-par kommer att tas bort. 

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

## <a name="use-max-depth-to-limit-github-action"></a>Använd maximalt djup för att begränsa GitHub-åtgärden
Standard beteendet för kapslade JSON-attribut är att förenkla hela objektet.  JSON nedan definierar detta nyckel/värde-par:

| Nyckel | Värde |
| --- | --- |
| Objekt: inre: InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```
Om det kapslade objektet är avsett att vara det värde som skickas till konfigurations instansen kan du använda *djup* -värdet för att stoppa förenklingen vid lämpligt djup. 

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

Med ett djup på 2 returnerar exemplet ovan följande nyckel: värde par:

| Nyckel | Värde |
| --- | --- |
| Objekt: inre | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Förstå åtgärds inmatningar
Indataparametrar anger data som används av åtgärden under körning.  Följande tabell innehåller indataparametrar som har godkänts av app Configuration Sync och de förväntade värdena för var och en.  Mer information om åtgärds inmatningar för GitHub-åtgärder finns i GitHub- [dokumentationen](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs).

> [!Note]
> Indatamängds-ID: n är Skift läges okänsliga.


| Inmatat namn | Krävs? | Värde |
|----|----|----|
| configurationFile | Ja | Sökväg till konfigurations filen i databasen, i förhållande till lagrings platsens rot.  BLOB mönster stöds och kan innehålla flera filer. |
| format | Ja | Fil format för konfigurations filen.  Giltiga format är: JSON, YAML, Properties. |
| connectionString | Ja | Anslutnings sträng för app Configuration-instansen. Anslutnings strängen ska lagras som en hemlighet i GitHub-lagringsplatsen och endast det hemliga namnet ska användas i arbets flödet. |
| brytning | Ja | Avgränsning som används vid förenkling av konfigurations filen till nyckel/värde-par.  Giltiga värden är:. , ; : - _ __ / |
| prefix | Inga | Prefix som ska läggas till i början av nycklar. |
| etikett | Inga | Etikett som används vid inställning av nyckel/värde-par. Om inget anges används en null-etikett. |
| begränsade | Inga | Ett booleskt värde som anger om strikt läge är aktiverat. Standardvärdet är false. |
| djuplodande | Inga | Högsta djup för att förenkla konfigurations filen.  Djupet måste vara ett positivt tal.  Standardvärdet har inget max djup. |
| tags | Inga | Anger taggen som angetts för nyckel/värde-par.  Det förväntade formatet är en stringified form av ett JSON-objekt av följande form: {[propertyName: sträng]: String;} Varje egenskaps namn-värdet blir en tagg. |

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om GitHub-åtgärden för app Configuration-synkronisering och hur den kan användas för att automatisera uppdateringar av din konfigurations instans för appar. Fortsätt till nästa [artikel](./concept-app-configuration-event.md)om du vill lära dig hur Azure App-konfigurationen reagerar på ändringar i nyckel/värde-par.
