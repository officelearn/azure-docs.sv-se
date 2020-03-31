---
title: Återanvända mallar över moln
description: Utveckla Azure Resource Manager-mallar som fungerar konsekvent för olika molnmiljöer. Skapa nya eller uppdatera befintliga mallar för Azure Stack.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: c5095efef5d4bef44993bdd9cd52dbdef17378a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156114"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>Utveckla ARM-mallar för molnkonsekvens

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

En viktig fördel med Azure är konsekvens. Utvecklingsinvesteringar för en plats kan återanvändas i en annan. En ARM-mall (Azure Resource Manager) gör dina distributioner konsekventa och repeterbara i alla miljöer, inklusive de globala Azure-, Azure-suveräna molnen och Azure Stack. Om du vill återanvända mallar över moln måste du dock ta hänsyn till molnspecifika beroenden som den här guiden förklarar.

Microsoft erbjuder intelligenta molntjänster som är redo för företag på många platser, bland annat:

* Den globala Azure-plattformen som stöds av ett växande nätverk av Microsoft-hanterade datacenter i regioner runt om i världen.
* Isolerade suveräna moln som Azure Germany, Azure Government och Azure China 21Vianet. Suveräna moln ger en konsekvent plattform med de flesta av samma fantastiska funktioner som globala Azure-kunder har åtkomst till.
* Azure Stack, en hybridmolnplattform som låter dig leverera Azure-tjänster från organisationens datacenter. Företag kan konfigurera Azure Stack i sina egna datacenter eller använda Azure-tjänster från tjänsteleverantörer som kör Azure Stack i sina anläggningar (kallas ibland värdregioner).

Azure Resource Manager är kärnan i alla dessa moln och tillhandahåller ett API som gör att en mängd olika användargränssnitt kan kommunicera med Azure-plattformen. Det här API:et ger dig kraftfulla funktioner för infrastruktur som kod. Alla typer av resurser som är tillgängliga på Azure-molnplattformen kan distribueras och konfigureras med Azure Resource Manager. Med en enda mall kan du distribuera och konfigurera ditt fullständiga program till ett operativt sluttillstånd.

![Azure-miljöer](./media/templates-cloud-consistency/environments.png)

Konsekvensen i globala Azure, de suveräna molnen, värdmolnen och ett moln i ditt datacenter hjälper dig att dra nytta av Azure Resource Manager. Du kan återanvända dina utvecklingsinvesteringar över dessa moln när du ställer in mallbaserad resursdistribution och konfiguration.

Men även om de globala, suveräna, värdbaserade och hybridmolnen tillhandahåller konsekventa tjänster är inte alla moln identiska. Därför kan du skapa en mall med beroenden på funktioner som endast är tillgängliga i ett visst moln.

I resten av den här guiden beskrivs de områden som ska beaktas när du planerar att utveckla nya eller uppdatera befintliga ARM-mallar för Azure Stack. I allmänhet bör din checklista innehålla följande punkter:

* Kontrollera att funktioner, slutpunkter, tjänster och andra resurser i mallen är tillgängliga på måldistributionsplatserna.
* Lagra kapslade mallar och konfigurationsartefakter på tillgängliga platser, vilket säkerställer åtkomst över moln.
* Använd dynamiska referenser i stället för hårdkodningslänkar och element.
* Kontrollera att mallparametrarna som du använder fungerar i målmolnen.
* Kontrollera att resursspecifika egenskaper är tillgängliga målmolnen.

En introduktion till ARM-mallar finns i [Malldistribution](overview.md).

## <a name="ensure-template-functions-work"></a>Se till att mallfunktionerna fungerar

Den grundläggande syntaxen för en ARM-mall är JSON. Mallar använder en superuppsättning av JSON och utökar syntaxen med uttryck och funktioner. Mallspråksbehandlaren uppdateras ofta för att stödja ytterligare mallfunktioner. En detaljerad förklaring av tillgängliga mallfunktioner finns i [ARM-mallfunktioner](template-functions.md).

Nya mallfunktioner som introduceras i Azure Resource Manager är inte omedelbart tillgängliga i de suveräna molnen eller Azure Stack. Om du vill distribuera en mall måste alla funktioner som refereras i mallen vara tillgängliga i målmolnet.

Azure Resource Manager-funktioner kommer alltid att introduceras till globala Azure först. Du kan använda följande PowerShell-skript för att kontrollera om nyligen introducerade mallfunktioner också är tillgängliga i Azure Stack:

1. Gör en klon av GitHub-databasen: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions).

1. När du har en lokal klon av databasen ansluter du till målets Azure Resource Manager med PowerShell.

1. Importera psm1-modulen och kör cmdleten Test-AzureRmTemplateFunctions:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

Skriptet distribuerar flera, minimerade mallar, som alla innehåller endast unika mallfunktioner. Utdata från skriptet rapporterar de mallfunktioner som stöds och inte är tillgängliga.

## <a name="working-with-linked-artifacts"></a>Arbeta med länkade artefakter

En mall kan innehålla referenser till länkade artefakter och innehålla en distributionsresurs som länkar till en annan mall. De länkade mallarna (kallas även kapslad mall) hämtas av Resource Manager vid körning. En mall kan också innehålla referenser till artefakter för vm-tillägg (Virtual Machine). Dessa artefakter hämtas av VM-tillägget som körs inuti den virtuella datorn för konfiguration av VM-tillägget under malldistributionen.

I följande avsnitt beskrivs överväganden för molnkonsekvens när du utvecklar mallar som innehåller artefakter som är externa till huvuddistributionsmallen.

### <a name="use-nested-templates-across-regions"></a>Använda kapslade mallar i olika regioner

Mallar kan delas upp i små, återanvändbara mallar, som alla har ett specifikt syfte och kan återanvändas över distributionsscenarier. Om du vill köra en distribution anger du en enda mall som kallas huvud- eller huvudmallen. Den anger vilka resurser som ska distribueras, till exempel virtuella nätverk, virtuella datorer och webbappar. Huvudmallen kan också innehålla en länk till en annan mall, vilket innebär att du kan kapsla mallar. På samma sätt kan en kapslad mall innehålla länkar till andra mallar. Du kan kapsla upp till fem nivåer djupt.

Följande kod visar hur parametern templateLink refererar till en kapslad mall:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager utvärderar huvudmallen vid körning och hämtar och utvärderar varje kapslad mall. När alla kapslade mallar har hämtats förenklas mallen och ytterligare bearbetning initieras.

### <a name="make-linked-templates-accessible-across-clouds"></a>Göra länkade mallar tillgängliga över moln

Fundera över var och hur du lagrar alla länkade mallar som du använder. Vid körning hämtar Azure Resource Manager – och kräver därför direkt åtkomst till – alla länkade mallar. En vanlig metod är att använda GitHub för att lagra de kapslade mallarna. En GitHub-databas kan innehålla filer som är tillgängliga för allmänheten via en URL. Även om den här tekniken fungerar bra för det offentliga molnet och de suveräna molnen kan en Azure Stack-miljö finnas i ett företagsnätverk eller på en frånkopplad fjärrplats, utan utgående Internet-åtkomst. I dessa fall skulle Azure Resource Manager misslyckas med att hämta de kapslade mallarna.

En bättre praxis för distributioner mellan moln är att lagra dina länkade mallar på en plats som är tillgänglig för målmolnet. Helst underhålls alla distributionsartefakter i och distribueras från en kontinuerlig integration/kontinuerlig utveckling (CI/CD) pipeline. Du kan också lagra kapslade mallar i en blob-lagringsbehållare, från vilken Azure Resource Manager kan hämta dem.

Eftersom blob-lagringen i varje moln använder ett annat slutpunkts fullt kvalificerat domännamn (FQDN), konfigurerar du mallen med platsen för de länkade mallarna med två parametrar. Parametrar kan acceptera användarindata vid distributionen. Mallar skapas och delas vanligtvis av flera personer, så en metod är att använda ett standardnamn för dessa parametrar. Namngivningskonventioner gör mallarna mer återanvändbara i regioner, moln och författare.

I följande kod `_artifactsLocation` används för att peka på en enda plats som innehåller alla distributionsrelaterade artefakter. Observera att ett standardvärde anges. Vid distributionen används standardvärdet om `_artifactsLocation`inget indatavärde anges för. Används `_artifactsLocationSasToken` som ingång för `sasToken`. Standardvärdet ska vara en tom sträng `_artifactsLocation` för scenarier där den inte är skyddad, till exempel en offentlig GitHub-databas.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

I hela mallen genereras länkar genom att kombinera `_artifactsLocation` bas-URI (från parametern) med en artefakt-relativ sökväg och `_artifactsLocationSasToken`. Följande kod visar hur du anger länken till den kapslade mallen med hjälp av uri-mallfunktionen:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Med den här metoden används `_artifactsLocation` standardvärdet för parametern. Om de länkade mallarna måste hämtas från en annan plats kan parameterindata användas vid distributionen för att åsidosätta standardvärdet– ingen ändring av själva mallen behövs.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Använd _artifactsLocation i stället för hårdkodningslänkar

Förutom att användas för kapslade mallar används URL:en i `_artifactsLocation` parametern som bas för alla relaterade artefakter i en distributionsmall. Vissa VM-tillägg innehåller en länk till ett skript som lagras utanför mallen. För dessa tillägg bör du inte hårdkoda länkarna. Till exempel kan tilläggen Custom Script och PowerShell DSC länka till ett externt skript på GitHub enligt bilden:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

Om du härdar länkarna till skriptet kan mallen distribueras till en annan plats. Under konfigurationen av vm-resursen initierar VM-agenten som körs inuti den virtuella datorn en hämtning av alla skript som är länkade i vm-tillägget och lagrar sedan skripten på den virtuella datorns lokala disk. Den här metoden fungerar som de kapslade malllänkarna som beskrivs tidigare i avsnittet "Använd kapslade mallar över regioner".

Resource Manager hämtar kapslade mallar vid körning. För vm-tillägg utförs hämtningen av externa artefakter av VM-agenten. Förutom den olika initieraren för artefakthämtningen är lösningen i malldefinitionen densamma. Använd parametern _artifactsLocation med standardvärdet för bassökvägen där alla artefakter lagras (inklusive vm-tilläggsskripten) `_artifactsLocationSasToken` och parametern för indata för sasToken.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Om du vill konstruera den absoluta URI:n för en artefakt är den bästa metoden att använda uri-mallfunktionen i stället för funktionen sammanfoga mall. Genom att ersätta hårdkodade länkar till skripten i VM-tillägget med uri-mallfunktionen konfigureras den här funktionen i mallen för molnkonsekvens.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Med den här metoden kan alla distributionsartefakter, inklusive konfigurationsskript, lagras på samma plats med själva mallen. Om du vill ändra platsen för alla länkar behöver du bara ange en annan bas-URL för _artefakterparametrarna_.

## <a name="factor-in-differing-regional-capabilities"></a>Faktor i olika regionala resurser

Med den agila utvecklingen och det kontinuerliga flödet av uppdateringar och nya tjänster som introduceras till Azure [kan regioner skilja sig åt](https://azure.microsoft.com/regions/services/) i tillgängligheten för tjänster eller uppdateringar. Efter rigorösa interna tester introduceras vanligtvis nya tjänster eller uppdateringar av befintliga tjänster för en liten målgrupp av kunder som deltar i ett valideringsprogram. Efter lyckad kundvalidering görs tjänsterna eller uppdateringarna tillgängliga inom en delmängd av Azure-regioner, sedan introduceras till fler regioner, distribueras till suveräna moln och kan göras tillgängliga för Azure Stack-kunder också.

Eftersom du vet att Azure-regioner och moln kan skilja sig åt i sina tillgängliga tjänster kan du fatta vissa proaktiva beslut om dina mallar. Ett bra ställe att börja är genom att undersöka tillgängliga resursleverantörer för ett moln. En resursleverantör anger den uppsättning resurser och åtgärder som är tillgängliga för en Azure-tjänst.

En mall distribuerar och konfigurerar resurser. En resurstyp tillhandahålls av en resursprovider. Beräkningsresursprovidern (Microsoft.Compute) tillhandahåller till exempel flera resurstyper, till exempel virtualMachines och availabilitySet. Varje resursprovider tillhandahåller ett API till Azure Resource Manager som definieras av ett gemensamt kontrakt, vilket möjliggör en konsekvent, enhetlig redigeringsupplevelse för alla resursleverantörer. En resursleverantör som är tillgänglig i globala Azure kanske inte är tillgänglig i ett suveränt moln eller en Azure Stack-region.

![Resursproviders](./media/templates-cloud-consistency/resource-providers.png)

Om du vill verifiera de resursleverantörer som är tillgängliga i ett visst moln kör du följande skript i Azure-kommandoradsgränssnittet[(CLI):](/cli/azure/install-azure-cli)

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Du kan också använda följande PowerShell-cmdlet för att se tillgängliga resursleverantörer:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Verifiera versionen av alla resurstyper

En uppsättning egenskaper är vanliga för alla resurstyper, men varje resurs har också sina egna specifika egenskaper. Nya funktioner och relaterade egenskaper läggs till befintliga resurstyper ibland via en ny API-version. En resurs i en mall har `apiVersion`en egen API-versionsegenskap - . Den här versionen säkerställer att en befintlig resurskonfiguration i en mall inte påverkas av ändringar på plattformen.

Nya API-versioner som introduceras till befintliga resurstyper i globala Azure kanske inte omedelbart är tillgängliga i alla regioner, suveräna moln eller Azure Stack. Om du vill visa en lista över tillgängliga resursleverantörer, resurstyper och API-versioner för ett moln kan du använda Resource Explorer i Azure-portalen. Sök efter Resource Explorer på menyn Alla tjänster. Expandera noden Providers i Resource Explorer för att returnera alla tillgängliga resursleverantörer, deras resurstyper och API-versioner i molnet.

Om du vill visa den tillgängliga API-versionen för alla resurstyper i ett visst moln i Azure CLI kör du följande skript:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Du kan också använda följande PowerShell-cmdlet:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Se resursplatser med en parameter

En mall distribueras alltid till en resursgrupp som finns i en region. Förutom själva distributionen har varje resurs i en mall också en platsegenskap som du använder för att ange vilken region som ska distribueras i. Om du vill utveckla mallen för molnkonsekvens behöver du ett dynamiskt sätt att referera till resursplatser, eftersom varje Azure Stack kan innehålla unika platsnamn. Vanligtvis distribueras resurser i samma region som resursgruppen, men för att stödja scenarier som programtillgänglighet mellan regioner kan det vara användbart att sprida resurser mellan regioner.

Även om du kan hårdkoda regionnamnen när du anger resursegenskaperna i en mall, garanterar den här metoden inte att mallen kan distribueras till andra Azure Stack-miljöer, eftersom regionnamnet troligen inte finns där.

Om du vill hantera olika regioner lägger du till en indataparameterplats i mallen med ett standardvärde. Standardvärdet används om inget värde anges under distributionen.

Mallfunktionen `[resourceGroup()]` returnerar ett objekt som innehåller följande nyckel-/värdepar:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Genom att referera till platsnyckeln för objektet i standardvärde för indataparametern ersätter `[resourceGroup().location]` Azure Resource Manager vid körning mallfunktionen med namnet på platsen för den resursgrupp som mallen distribueras till.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Med den här mallfunktionen kan du distribuera mallen till alla moln utan att ens känna till regionnamnen i förväg. Dessutom kan en plats för en viss resurs i mallen skilja sig från resursgruppens plats. I det här fallet kan du konfigurera den med hjälp av ytterligare indataparametrar för den specifika resursen, medan de andra resurserna i samma mall fortfarande använder den ursprungliga indataparametern för plats.

### <a name="track-versions-using-api-profiles"></a>Spåra versioner med API-profiler

Det kan vara mycket svårt att hålla reda på alla tillgängliga resursleverantörer och relaterade API-versioner som finns i Azure Stack. I skrivande stund är `2018-04-01`till exempel den senaste API-versionen för **Microsoft.Compute/availabilitySet** i Azure , `2016-03-30`medan den tillgängliga API-versionen som är gemensam för Azure och Azure Stack är . Den vanliga API-versionen för **Microsoft.Storage/storageAccounts** delas mellan `2016-01-01`alla Azure- och Azure `2018-02-01`Stack-platser är , medan den senaste API-versionen i Azure är .

Därför introducerade Resource Manager begreppet API-profiler till mallar. Utan API-profiler konfigureras varje resurs i `apiVersion` en mall med ett element som beskriver API-versionen för den specifika resursen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

En API-profilversion fungerar som ett alias för en enda API-version per resurstyp som är gemensam för Azure och Azure Stack. I stället för att ange en API-version för varje resurs i en mall `apiProfile` anger du `apiVersion` bara API-profilversionen i ett nytt rotelement som anropas och utelämnar elementet för de enskilda resurserna.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

API-profilen säkerställer att API-versionerna är tillgängliga på olika platser, så du behöver inte verifiera apiVersionerna som är tillgängliga manuellt på en viss plats. För att säkerställa att API-versioner som refereras av din API-profil finns i en Azure Stack-miljö måste Azure Stack-operatörerna hålla lösningen uppdaterad baserat på principen för support. Om ett system är mer än sex månader gammalt anses det vara ur överensstämmelse och miljön måste uppdateras.

API-profilen är inte ett obligatoriskt element i en mall. Även om du lägger till elementet används det `apiVersion` bara för resurser som inget har angetts för. Det här elementet möjliggör gradvisa ändringar men kräver inga ändringar i befintliga mallar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Kontrollera slutpunktsreferenser

Resurser kan ha referenser till andra tjänster på plattformen. En offentlig IP-adress kan till exempel ha ett offentligt DNS-namn tilldelat. Det offentliga molnet, suveräna moln och Azure Stack-lösningar har sina egna distinkta slutpunktsnamnområden. I de flesta fall kräver en resurs bara ett prefix som indata i mallen. Under körning lägger Azure Resource Manager till slutpunktsvärdet till det. Vissa slutpunktsvärden måste uttryckligen anges i mallen.

> [!NOTE]
> Om du vill utveckla mallar för molnkonsekvens ska du inte hårdkoda namnområden för slutpunkt.

Följande två exempel är vanliga slutpunktsnamnområden som uttryckligen måste anges när en resurs skapas:

* Lagringskonton (blob, kö, tabell och fil)
* Anslutningssträngar för databaser och Azure Cache för Redis

Namnområden för slutpunkt kan också användas i utdata från en mall som information för användaren när distributionen är klar. Följande är vanliga exempel:

* Lagringskonton (blob, kö, tabell och fil)
* Anslutningssträngar (MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* domainNameLabel för en offentlig IP-adress
* Molntjänster

Undvik i allmänhet hårdkodade slutpunkter i en mall. Det bästa är att använda referensmallsfunktionen för att hämta slutpunkterna dynamiskt. Den slutpunkt som oftast hårdkodas är till exempel slutpunktsnamnområdet för lagringskonton. Varje lagringskonto har ett unikt FQDN som skapas genom att sammanfoga namnet på lagringskontot med slutpunktsnamnområdet. Ett blob-lagringskonto med namnet mystorageaccount1 resulterar i olika FQDN beroende på molnet:

* **mystorageaccount1.blob.core.windows.net** när det skapas på det globala Azure-molnet.
* **mystorageaccount1.blob.core.chinacloudapi.cn** när det skapas i Azure China 21Vianet-molnet.

Följande referensmallsfunktion hämtar slutpunktsnamnområdet från lagringsresursprovidern:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Genom att ersätta det hårdkodade värdet för `reference` slutpunkten för lagringskontot med mallfunktionen kan du använda samma mall för att distribuera till olika miljöer utan att göra några ändringar i slutpunktsreferensen.

### <a name="refer-to-existing-resources-by-unique-id"></a>Referera till befintliga resurser med unikt ID

Du kan också referera till en befintlig resurs från samma eller en annan resursgrupp, och inom samma prenumeration eller en annan prenumeration, inom samma klientorganisation i samma moln. Om du vill hämta resursegenskaperna måste du använda den unika identifieraren för själva resursen. Mallfunktionen `resourceId` hämtar det unika ID:t för en resurs som SQL Server som följande kod visar:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Du kan sedan `resourceId` använda `reference` funktionen i mallfunktionen för att hämta egenskaperna för en databas. Returobjektet innehåller `fullyQualifiedDomainName` egenskapen som innehåller det fullständiga slutpunktsvärdet. Det här värdet hämtas vid körning och tillhandahåller det molnmiljöspecifika slutpunktsnamnområdet. Om du vill definiera anslutningssträngen utan att begränsa slutpunktsnamnområdet kan du referera till egenskapen för returobjektet direkt i anslutningssträngen som visas:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Tänk på resursegenskaper

Specifika resurser i Azure Stack-miljöer har unika egenskaper som du måste ta hänsyn till i mallen.

### <a name="ensure-vm-images-are-available"></a>Se till att VM-avbildningar är tillgängliga

Azure erbjuder ett rikt urval av VM-avbildningar. Dessa avbildningar skapas och förbereds för distribution av Microsoft och partner. Bilderna utgör grunden för virtuella datorer på plattformen. En molnkonsekvent mall bör dock endast referera till tillgängliga parametrar – i synnerhet utgivare, erbjudande och SKU för vm-avbildningar som är tillgängliga för globala Azure-, Azure-suveräna moln eller en Azure Stack-lösning.

Om du vill hämta en lista över tillgängliga VM-avbildningar på en plats kör du följande Azure CLI-kommando:

```azurecli-interactive
az vm image list -all
```

Du kan hämta samma lista med Azure PowerShell cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) `-Location` och ange den plats du vill ha med parametern. Ett exempel:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Det här kommandot tar ett par minuter att returnera alla tillgängliga avbildningar i regionen Västeuropa i det globala Azure-molnet.

Om du gjorde dessa VM-avbildningar tillgängliga för Azure Stack, kommer all tillgänglig lagring att förbrukas. För att även rymma den minsta skalenheten kan du med Azure Stack välja de avbildningar som du vill lägga till i en miljö.

Följande kodexempel visar en konsekvent metod för att referera till parametrarna utgivare, erbjudande och SKU i ARM-mallarna:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Kontrollera lokala vm-storlekar

Om du vill utveckla mallen för molnkonsekvens måste du se till att den virtuella datorns storlek du vill ha är tillgänglig i alla målmiljöer. VM-storlekar är en gruppering av prestandaegenskaper och funktioner. Vissa vm-storlekar beror på vilken maskinvara som den virtuella datorn körs på. Om du till exempel vill distribuera en GPU-optimerad virtuell dator måste maskinvaran som kör hypervisorn ha maskinvaru GPU:erna.

När Microsoft introducerar en ny storlek på den virtuella datorn som har vissa maskinvaruberoenden görs den virtuella datorns storlek vanligtvis tillgänglig först i en liten delmängd av regioner i Azure-molnet. Senare görs den tillgänglig för andra regioner och moln. Om du vill vara säker på att den virtuella datorns storlek finns i varje moln som du distribuerar till kan du hämta de tillgängliga storlekarna med följande Azure CLI-kommando:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

För Azure PowerShell använder du:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

En fullständig lista över tillgängliga tjänster finns i [Produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Kontrollera användningen av Azure-hanterade diskar i Azure Stack

Hanterade diskar hanterar lagringen för en Azure-klient. I stället för att uttryckligen skapa ett lagringskonto och ange URI för en virtuell hårddisk (VHD) kan du använda hanterade diskar för att implicit utföra dessa åtgärder när du distribuerar en virtuell dator. Hanterade diskar förbättrar tillgängligheten genom att placera alla diskar från virtuella datorer i samma tillgänglighetsuppsättning i olika lagringsenheter. Dessutom kan befintliga virtuella hårddiskar konverteras från Standard till Premium-lagring med betydligt mindre driftstopp.

Även om hanterade diskar finns på översikten för Azure Stack stöds de för närvarande inte. Tills de är det kan du utveckla molnkonsekventa mallar `vhd` för Azure Stack genom att uttryckligen ange virtuella hårddiskar med hjälp av elementet i mallen för VM-resursen som visas:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Om du däremot vill ange en hanterad `vhd` diskkonfiguration i en mall tar du bort elementet från diskkonfigurationen.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Samma ändringar gäller även [datadiskar](../../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Kontrollera att VM-tillägg är tillgängliga i Azure Stack

En annan faktor för molnkonsekvens är användningen av tillägg för [virtuella datorer](../../virtual-machines/windows/extensions-features.md) för att konfigurera resurserna i en virtuell dator. Alla VM-tillägg är inte tillgängliga i Azure Stack. En mall kan ange de resurser som avsatts för vm-tillägget, vilket skapar beroenden och villkor i mallen.

Om du till exempel vill konfigurera en virtuell dator som kör Microsoft SQL Server kan VM-tillägget konfigurera SQL Server som en del av malldistributionen. Tänk på vad som händer om distributionsmallen också innehåller en programserver som konfigurerats för att skapa en databas på den virtuella datorn som kör SQL Server. Förutom att även använda ett VM-tillägg för programservrarna kan du konfigurera beroendet av programservern när SQL Server VM-tilläggsresursen har returnerats. Den här metoden säkerställer att den virtuella datorn som kör SQL Server är konfigurerad och tillgänglig när programservern instrueras att skapa databasen.

Med mallens deklarativa metod kan du definiera sluttillståndet för resurserna och deras beroenden, medan plattformen tar hand om den logik som krävs för beroenden.

#### <a name="check-that-vm-extensions-are-available"></a>Kontrollera att vm-tillägg är tillgängliga

Det finns många typer av VM-tillägg. När du utvecklar mall för molnkonsekvens, se till att bara använda de tillägg som är tillgängliga i alla regioner mallen mål.

Om du vill hämta en lista över de VM-tillägg som `myLocation`är tillgängliga för en viss region (i det här exemplet ) kör du följande Azure CLI-kommando:

```azurecli-interactive
az vm extension image list --location myLocation
```

Du kan också köra cmdleten Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) och använda `-Location` för att ange platsen för avbildningen för den virtuella datorn. Ett exempel:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Se till att versioner är tillgängliga

Eftersom VM-tillägg är resurser från första part har de egna API-versioner. Som följande kod visar är vm-tilläggstypen en kapslad resurs i Microsoft.Compute-resursprovidern.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

API-versionen av vm-tilläggsresursen måste finnas på alla platser som du planerar att rikta med mallen. Platsberoendet fungerar som resursproviderns API-versionstillgänglighet som beskrivs tidigare i avsnittet "Verifiera versionen av alla resurstyper".

Om du vill hämta en lista över tillgängliga API-versioner för vm-tilläggsresursen använder du cmdleten [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) med Resursprovidern **Microsoft.Compute** som visas:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Du kan också använda VM-tillägg i skalningsuppsättningar för virtuella datorer. Samma platsvillkor gäller. Om du vill utveckla mallen för molnkonsekvens kontrollerar du att API-versionerna är tillgängliga på alla platser som du planerar att distribuera till. Om du vill hämta API-versionerna av vm-tilläggsresursen för skalningsuppsättningar använder du samma cmdlet som tidigare, men anger resurstypen för skalärningsuppsättningar för den virtuella datorn som visas:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Varje specifikt tillägg är också versioned. Den här versionen `typeHandlerVersion` visas i egenskapen för vm-tillägget. Kontrollera att den version som `typeHandlerVersion` anges i elementet i mallens VM-tillägg är tillgänglig på de platser där du planerar att distribuera mallen. Följande kod anger till exempel version 1.7:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Om du vill hämta en lista över tillgängliga versioner för ett visst VM-tillägg använder du cmdleten [Get-AzureRmVMExtensionImage.](/powershell/module/az.compute/get-azvmextensionimage) I följande exempel hämtas de tillgängliga versionerna för VM-tillägget PowerShell DSC (Desired State Configuration) från **myLocation:**

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Om du vill hämta en lista över utgivare använder du kommandot [Get-AzureRmVmImagePublisher.](/powershell/module/az.compute/get-azvmimagepublisher) Om du vill begära typ använder du den [get-AzureRmVMExtensionImageType-rekommendationen.](/powershell/module/az.compute/get-azvmextensionimagetype)

## <a name="tips-for-testing-and-automation"></a>Tips för testning och automatisering

Det är en utmaning att hålla reda på alla relaterade inställningar, funktioner och begränsningar när du skapar en mall. Den gemensamma metoden är att utveckla och testa mallar mot ett enda moln innan andra platser är riktade. Men ju tidigare tester som utförs i redigeringsprocessen, desto mindre felsökning och kod skriva om utvecklingsteamet kommer att behöva göra. Distributioner som misslyckas på grund av platsberoenden kan vara tidskrävande att felsöka. Därför rekommenderar vi automatiserad testning så tidigt som möjligt i redigeringscykeln. I slutändan behöver du mindre utvecklingstid och färre resurser, och dina molnkonsekventa artefakter blir ännu mer värdefulla.

Följande bild visar ett typiskt exempel på en utvecklingsprocess för ett team som använder en integrerad utvecklingsmiljö (IDE). I olika skeden i tidslinjen körs olika testtyper. Här arbetar två utvecklare på samma lösning, men det här scenariot gäller lika för en enda utvecklare eller ett stort team. Varje utvecklare skapar vanligtvis en lokal kopia av en central databas, så att var och en kan arbeta med den lokala kopian utan att påverka de andra som kanske arbetar med samma filer.

![Arbetsflöde](./media/templates-cloud-consistency/workflow.png)

Tänk på följande tips för testning och automatisering:

* Använd testverktyg. Visual Studio Code och Visual Studio innehåller till exempel IntelliSense och andra funktioner som kan hjälpa dig att validera mallarna.
* För att förbättra kodkvaliteten under utveckling på den lokala IDE: et utför du statisk kodanalys med enhetstester och integrationstester.
* För en ännu bättre upplevelse under den inledande utvecklingen bör enhetstester och integrationstester endast varna när ett problem hittas och fortsätta med testerna. På så sätt kan du identifiera de problem som ska åtgärdas och prioritera ordningen på ändringarna, även kallad testdriven distribution (TDD).
* Tänk på att vissa tester kan utföras utan att vara anslutna till Azure Resource Manager. Andra, som att testa malldistribution, kräver att Resource Manager utför vissa åtgärder som inte kan utföras offline.
* Att testa en distributionsmall mot validerings-API:et är inte lika med en faktisk distribution. Även om du distribuerar en mall från en lokal fil hämtas alla referenser till kapslade mallar i mallen direkt av Resource Manager och artefakter som refereras av VM-tillägg hämtas av vm-agenten som körs inuti den distribuerade virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Överväganden för Azure Resource Manager-mall](/azure-stack/user/azure-stack-develop-templates)
* [Metodtips för ARM-mallar](template-syntax.md)
