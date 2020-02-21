---
title: Använda GitHub-åtgärder med Azure App konfigurations synkronisering
description: Använd GitHub-åtgärder för att utlösa en uppdatering av konfigurations instansen när definierade åtgärder utförs på en GitHub-lagringsplats
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9d60f1885a85fd7d45090f1cb4905a3d95d9d1d6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523721"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Synkronisera en App Configuration-instans med GitHub Actions
Azure App-konfigurationen använder GitHub-åtgärder för att utlösa uppdateringar till en app Configuration-instans baserat på åtgärder som utförs på en GitHub-lagringsplats. GitHub-arbetsflöden utlöser konfigurations uppdateringar, vilket möjliggör integrering av dessa uppdateringar i samma arbets flöde som används för att uppdatera appens kod.

Ett [arbets flöde](https://help.github.com/articles/about-github-actions#workflow) för GitHub-åtgärder definierar en automatiserad process i en GitHub-lagringsplats. Den här processen visar GitHub hur du skapar och distribuerar ditt GitHub-projekt. Azure App-konfigurationen tillhandahåller åtgärden *Azure App konfigurations synkronisering* för att aktivera uppdateringar till en app Configuration-instans när ändringar görs i käll lagrings platsen. 

En YAML-fil (. yml) som finns i `/.github/workflows/` sökvägen till lagrings platsen definierar ditt arbets flöde. Den här definitionen innehåller arbets flödets steg och parametrar.

GitHub-händelser, till exempel en push-överföring till en lagrings plats, kan utlösa ett GitHub åtgärds arbets flöde.  Med åtgärden *Azure App Sync-konfiguration* kan du utlösa en uppdatering av en konfigurations instans för en app när en angiven GitHub-åtgärd utförs. Du kan utlösa konfigurations uppdateringar när du skickar, granska eller förgrena appars konfigurationsfiler precis som du gör med app-kod.

GitHub- [dokumentationen](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) innehåller djupgående visning av GitHub-arbetsflöden och åtgärder. 

## <a name="enable-github-actions-in-your-repository"></a>Aktivera GitHub-åtgärder i din lagrings plats
Om du vill börja använda den här GitHub-åtgärden går du till din lagrings plats och väljer fliken **åtgärder** . Klicka på **nytt arbets flöde**och **skapa sedan ett arbets flöde själv**. Slutligen söker du efter "Azure App konfigurations synkronisering" på Marketplace.
> [!div class="mx-imgBorder"]
> ![väljer fliken åtgärd](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![här väljer du synkronisering av app-konfiguration](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synkronisera konfigurationsfiler efter en push
Den här åtgärden synkroniserar Azure App konfigurationsfiler när en ändring flyttas till `appsettings.json`. När en utvecklare skickar en ändring till `appsettings.json`uppdaterar synkroniseringen av app Configuration-instansen med de nya värdena.

Det första avsnittet av det här arbets flödet anger att åtgärden utlöses *på* en *push* som innehåller `appsettings.json` till *huvud* grenen. I det andra avsnittet visas jobben som körs när åtgärden har Aktiver ATS. Åtgärden kontrollerar de relevanta filerna och uppdaterar konfigurations instansen för appen med hjälp av anslutnings strängen som lagras som en hemlighet i lagrings platsen.  Mer information om hur du använder hemligheter i GitHub finns i [GitHub-artikeln](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) om hur du skapar och använder krypterade hemligheter.

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
Föregående åtgärd uppdaterar appens konfigurations instans när `appsettings.json` uppdateras. Den här åtgärden infogar en dynamisk etikett vid varje synkronisering, vilket säkerställer att varje synkronisering kan identifieras unikt och tillåter att kod ändringar mappas till konfigurations ändringar.

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

Med ett djup på 2 returnerar exemplet ovan följande nyckel/värde-par:

| Nyckel | Värde |
| --- | --- |
| Objekt: inre | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Förstå åtgärds inmatningar
Indataparametrar anger data som används av åtgärden under körning.  Följande tabell innehåller indataparametrar som har godkänts av app Configuration Sync och de förväntade värdena för var och en.  Mer information om åtgärds inmatningar för GitHub-åtgärder finns i GitHub- [dokumentationen](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs).

> [!Note]
> Indatamängds-ID: n är Skift läges okänsliga.


| Inmatat namn | Krävs? | Värde |
|----|----|----|
| configurationFile | Ja | Relativ sökväg till konfigurations filen i lagrings platsen.  BLOB mönster stöds och kan innehålla flera filer. |
| format | Ja | Fil format för konfigurations filen.  Giltiga format är: JSON, YAML, Properties. |
| connectionString | Ja | Anslutnings sträng för app Configuration-instansen. Anslutnings strängen ska lagras som en hemlighet i GitHub-lagringsplatsen och endast det hemliga namnet ska användas i arbets flödet. |
| brytning | Ja | Avgränsning som används vid förenkling av konfigurations filen till nyckel/värde-par.  Giltiga värden är:. , ; : - _ __ / |
| prefix | Nej | Prefix som ska läggas till i början av nycklar. |
| etikett | Nej | Etikett som används vid inställning av nyckel/värde-par. Om inget anges används en null-etikett. |
| begränsade | Nej | Ett booleskt värde som anger om strikt läge är aktiverat. Standardvärdet är false. |
| djuplodande | Nej | Högsta djup för att förenkla konfigurations filen.  Djupet måste vara ett positivt tal.  Standardvärdet har inget max djup. |
| taggar | Nej | Anger taggen som angetts för nyckel/värde-par.  Det förväntade formatet är en stringified form av ett JSON-objekt av följande form: {[propertyName: sträng]: String;} Varje egenskaps namn-värdet blir en tagg. |

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om GitHub-åtgärden för app Configuration-synkronisering och hur den kan användas för att automatisera uppdateringar av din konfigurations instans för appar. Fortsätt till nästa [artikel](./concept-app-configuration-event.md)om du vill lära dig hur Azure App-konfigurationen reagerar på ändringar i nyckel/värde-par.
