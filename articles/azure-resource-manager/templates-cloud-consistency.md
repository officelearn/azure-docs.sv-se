---
title: Återanvända mallar i molnet – Azure Resource Manager
description: Utveckla Azure Resource Manager-mallar som arbetar konsekvent för olika molnmiljöer. Skapa en ny eller uppdatera befintliga mallar för Azure Stack.
services: azure-resource-manager
documentationcenter: na
author: marcvaneijk
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: ab19baa1c10f329b5bbe3c14261434d7f8e2538f
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076538"
---
# <a name="develop-azure-resource-manager-templates-for-cloud-consistency"></a>Utveckla Azure Resource Manager-mallar för att få konsekvens i molnet

En viktig fördel med Azure är konsekvens. Utveckling-investeringar för en plats som ska återanvändas i en annan. En mall gör dina distributioner konsekvent och repeterbar över miljöer, inklusive den globala Azure, Azure suveräna moln och Azure Stack. Om du vill återanvända mallar i moln, men måste du du överväga moln-specifika beroenden som den här handboken beskrivs.

Microsoft erbjuder intelligenta molntjänster som klart för företag på många platser, inklusive:

* Globala Azure-plattformen stöds av ett växande nätverk av Microsoft-hanterade datacenter i regioner runtom i världen.
* Isolerade suveräna moln som Azure Germany, Azure Government och Azure Kina (Azure som drivs av 21Vianet). Suveräna moln ger en konsekvent plattform med de flesta av samma fantastiska funktioner som globala Azure-kunder har åtkomst till.
* Azure Stack, som är en hybridmolnplattform som låter dig leverera Azure-tjänster från organisationens datacenter. Företag kan ställa in Azure Stack i sina egna Datacenter eller använda Azure-tjänster från tjänstleverantörer som kör Azure Stack i sina lokaler (kallas ibland för värdbaserade regioner).

Kärnan i alla moln tillhandahåller Azure Resource Manager ett API som gör en mängd olika användargränssnitt för att kommunicera med Azure-plattformen. Detta API ger kraftfulla funktioner för infrastruktur som kod. Någon typ av resurs som är tillgänglig på Azure-molnplattformen kan distribueras och konfigureras med Azure Resource Manager. Du kan distribuera och konfigurera fullständig programmet till ett operational sluttillstånd med en enda mall.

![Azure-miljöer](./media/templates-cloud-consistency/environments.png)

Konsekvenskontroll av global Azure, nationella moln värdbaserade moln och ett moln i ditt datacenter kan du dra från Azure Resource Manager. Du kan återanvända dina investeringar för utveckling i dessa moln när du ställer in-mallbaserade distribution och konfiguration.

Men även om den globala, nationella, installation, hybridmoln ger enhetliga tjänster och alla moln är inte identiska. Därför kan du skapa en mall med beroenden på funktioner som är tillgängliga bara i ett visst moln.

Resten av den här guiden beskriver områdena att tänka på när du planerar att utveckla nya eller uppdatering befintliga Azure Resource Manager-mallar för Azure Stack. Din checklista bör generellt sett är följande:

* Kontrollera att funktionerna, slutpunkter, tjänster och andra resurser i din mall är tillgängliga i målplatser för distribution.
* Store kapslade mallar och artefakter konfiguration på tillgängliga platser, ser till att åtkomsten mellan moln.
* Använd dynamisk referenser i stället för att hårdkoda länkar och element.
* Se till att mallparametrar som du använder fungerar i mål-moln.
* Kontrollera att resursspecifika egenskaper är tillgängliga mål moln.

En introduktion till Azure Resource Manager-mallar finns i [malldistributionen](resource-group-overview.md#template-deployment).

## <a name="ensure-template-functions-work"></a>Se till att arbeta Mallfunktioner

Den grundläggande syntaxen i Resource Manager-mall är JSON. Mallar använder en supermängd JSON, utöka med syntax för uttryck och funktioner. Mallen språk processorn uppdateras ofta för att stödja ytterligare Mallfunktioner. En detaljerad förklaring av de tillgängliga Mallfunktioner finns i [Azure Resource Manager-Mallfunktioner](resource-group-template-functions.md).

Nya funktioner som introduceras till Azure Resource Manager-mall är inte omedelbart tillgängliga i suveräna moln eller Azure Stack. Om du vill distribuera en mall har måste alla funktioner som refereras till i mallen vara tillgänglig i målmolnet. 

Azure Resource Manager-funktioner kommer alltid att läggas till globala Azure först. Du kan använda följande PowerShell-skript för att verifiera om nyligen införda Mallfunktioner är också tillgängliga i Azure Stack: 

1. Gör en klon av GitHub-lagringsplatsen: [ https://github.com/marcvaneijk/arm-template-functions ](https://github.com/marcvaneijk/arm-template-functions).

1. När du har en lokal klon av lagringsplatsen kan du ansluta till målet Azure Resource Manager med PowerShell.

1. Importera modulen psm1 och kör cmdleten Test-AzTemplateFunctions:

  ```powershell
  # Import the module
  Import-module <path to local clone>\AzTemplateFunctions.psm1

  # Execute the Test-AzTemplateFunctions cmdlet
  Test-AzTemplateFunctions -path <path to local clone>
  ```

Skriptet etablerar flera minimeras mallar som innehåller endast unika Mallfunktioner. Utdata från skriptet rapporterar stöds och otillgängligt Mallfunktioner.

## <a name="working-with-linked-artifacts"></a>Arbeta med länkade artefakter

En mall kan innehålla referenser till länkade artefakter och innehåller en distributionsresurs som länkar till en annan mall. Länkade mallar (kallas även kapslad mall) hämtas av Resource Manager vid körning. En mall kan också innehålla referenser till artefakter för tillägg för virtuell dator (VM). Dessa artefakter hämtas av VM-tillägg som körs i den virtuella datorn för att konfigurera VM-tillägget när mallen distribueras. 

I följande avsnitt beskrivs överväganden för molnet konsekvens när du utvecklar mallar som innehåller artefakter som ligger utanför den huvudsakliga distributionsmallen.

### <a name="use-nested-templates-across-regions"></a>Använda kapslade mallar i flera regioner

Mallar kan delas upp i små, återanvändningsbara mallar, som har ett specifikt syfte och kan återanvändas i distributionsscenarier. Om du vill köra en distribution kan du ange en enda mall som kallas den huvudsakliga eller master mallen. Den anger resurser för att distribuera, till exempel virtuella nätverk, virtuella datorer och webbappar. Den huvudsakliga mallen kan också innehålla en länk till en annan mall, vilket innebär att du kan kapsla mallar. På samma sätt kan kan en kapslad mall innehålla länkar till andra mallar. Du kan kapsla upp till fem nivåers djup.

Följande kod visar hur parametern templateLink refererar till en kapslad mall:

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
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

Azure Resource Manager utvärderar Huvudmall vid körning och hämtar och utvärderar de kapslade mallar. När alla kapslade mallar hämtas mallen förenklas och ytterligare bearbetning initieras.

### <a name="make-linked-templates-accessible-across-clouds"></a>Gör länkade mallar tillgängliga i moln

Överväg att var och hur att lagra alla länkade mallar som du använder. Vid körning, Azure Resource Manager hämtar – och därför kräver direkt åtkomst till – eventuellt länkad mallar. En vanlig metod är att använda GitHub för att lagra de kapslade mallarna. En GitHub-lagringsplats kan innehålla filer som kan nås offentligt via en URL. Även om den här metoden fungerar bra för det offentliga molnet och suveräna moln, kanske en Azure Stack-miljö på ett företagsnätverk eller på en frånkopplad fjärrplats, utan någon utgående Internetåtkomst. I sådana fall kan misslyckas Azure Resource Manager att hämta kapslade mallar. 

En bättre metod för molnöverskridande distributioner är att lagra din länkade mallar på en plats som är tillgänglig för målmolnet. Vi rekommenderar alla distribution artefakter underhålls i och distribueras från en pipeline för kontinuerlig integrering/kontinuerlig utveckling (CI/CD). Du kan också lagra kapslade mallar i ett blob storage-behållare som Azure Resource Manager kan hämta dem. 

Eftersom blob-lagringen i varje moln använder ett fullständigt kvalificerat domännamn (FQDN) för olika slutpunkt kan du konfigurera mallen med platsen för de länkade mallarna med två parametrar. Parametrar kan acceptera indata från användaren vid tidpunkten för distribution. Mallar är vanligtvis skapats och delas av flera personer, så det är bra att använda ett standard namn för dessa parametrar. Namngivningskonventioner hjälpa mallar mer återanvändbara i regioner, moln och författare.

I följande kod, `_artifactsLocation` används för att peka på en enda plats, som innehåller alla distribution-relaterade artefakter. Observera att ett standardvärde har angetts. Vid distributionen, om ingen indatavärdet har angetts för `_artifactsLocation`, används standardvärdet. Den `_artifactsLocationSasToken` används som indata för den `sasToken`. Standardvärdet bör vara en tom sträng för scenarier där de `_artifactsLocation` inte skyddas – till exempel en offentlig GitHub-lagringsplats.

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

I mallen, länkar genereras genom att kombinera bas-URI (från den `_artifactsLocation` parametern) med en artefakt relativ sökväg och `_artifactsLocationSasToken`. Följande kod visar hur du anger en länk till den kapslade mallen med hjälp av mallfunktionen uri:

```json
"resources": [
  {
    "name": "shared",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
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

Med den här metoden kan standardvärdet för den `_artifactsLocation` parametern används. Om de länkade mallarna måste hämtas från en annan plats, parametern indata kan användas vid tidpunkten för distribution för att åsidosätta standardvärdet, krävs ingen ändring i själva mallen.

### <a name="use-artifactslocation-instead-of-hardcoding-links"></a>Använda _artifactsLocation i stället för hardcoding länkar

Förutom att användas för kapslade mallar, URL-Adressen i den `_artifactsLocation` parametern används som bas för alla relaterade artefakter i en Distributionsmall. Vissa VM-tillägg innehåller en länk till ett skript som lagras utanför mallen. För de här tilläggen bör du inte hårdkoda länkarna. Anpassat skript och PowerShell DSC-tillägg kan till exempel länka till ett externt skript på GitHub som visas: 

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

Hardcoding länkar till skriptet potentiellt förhindrar att mallen har distribuera till en annan plats. VM-agenten som körs på den virtuella datorn under konfigurationen av den Virtuella datorresursen initierar en nedladdning av alla skript som är länkade i VM-tillägget och lagrar sedan skripten på den Virtuella datorns hårddisk. Den här metoden fungerar som den kapslade mallen länken beskrevs tidigare i avsnittet ”använda kapslade mallar i olika regioner”.

Resource Manager hämtar kapslade mallar vid körning. Hämtning av alla externa artefakter utförs för VM-tillägg av VM-agenten. Förutom artefakt hämtning olika initierare är lösningen i malldefinitionen samma. Använd parametern _artifactsLocation med ett standardvärde på rotsökvägen där alla artefakter lagras (inklusive skript för VM-tillägget) och `_artifactsLocationSasToken` parameter för indata för sasToken.

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

För att konstruera absolut URI för en artefakt, är det bästa sättet att använda mallfunktionen uri i stället för funktionen concat mall. Den här funktionen i mallen har konfigurerats för molnet konsekvens genom att ersätta hårdkodad länkar till skripten i VM-tillägget med mallfunktionen uri.

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

Med den här metoden kan alla artefakter i distributionen, inklusive konfigurationsskript, lagras på samma plats med själva mallen. Om du vill ändra platsen för alla länkar behöver du bara ange en annan grundläggande Webbadress för _artifactsLocation-parametrar.

## <a name="factor-in-differing-regional-capabilities"></a>Ta hänsyn till skiljer sig regionala funktioner

Med smidig utveckling och kontinuerliga flöde av uppdateringar och nya tjänster som introducerats i Azure, [regioner kan skilja sig åt](https://azure.microsoft.com/regions/services/) tillgänglighet för tjänster eller uppdateringar. Efter rigorösa interna testning introduceras nya tjänster eller uppdateringar av befintliga tjänster vanligtvis till en liten publik för kunder som deltar i ett verifieringsprogram. Efter lyckad validering, är tjänster eller uppdateringar tillgängliga i en delmängd av Azure-regioner, och sedan introducerats i fler regioner, lanseras till nationella moln och potentiellt göras tillgängligt för Azure Stack-kunder.

Att veta att Azure-regioner och moln kan skilja sig i deras tillgängliga tjänster, kan du ta några proaktiva beslut om dina mallar. Det är ett bra ställe att börja genom att undersöka vilka resursprovidrar för ett moln. En resursprovider beskriver uppsättningen resurser och åtgärder som är tillgängliga för en Azure-tjänst.

En mall distribuerar och konfigurerar resurser. En resurstyp som tillhandahålls av en resursprovider. Compute-resursprovidern (Microsoft.Compute), innehåller till exempel flera typer av resurser, till exempel virtuella datorer och availabilitySets. Varje resursprovider tillhandahåller ett API till Azure Resource Manager definieras av ett vanligt kontrakt, aktiverar en konsekvent och enhetlig redigeringen över alla resursprovidrar. Men kanske en provider för nätverksresurser som är tillgängliga i globala Azure inte tillgänglig i ett nationellt moln eller en Azure Stack-region.

![Resursproviders](./media/templates-cloud-consistency/resource-providers.png) 

Kör följande skript för att verifiera de resursprovidrar som är tillgängliga i ett visst moln i Azure kommandoradsgränssnitt ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Du kan också använda följande PowerShell-cmdlet för att se tillgängliga resursproviders:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Kontrollera vilken version av alla typer av resurser

En uppsättning egenskaper är gemensamma för alla typer av resurser, men varje resurs har också sin egen specifika egenskaper. Nya funktioner och relaterade egenskaper läggs till i befintliga resurstyper ibland via en ny API-version. En resurs i en mall har sin egen API-versionsegenskapen - `apiVersion`. Den här versionshantering säkerställer att en befintlig resurskonfiguration i en mall inte påverkas av ändringar på plattformen.

Ny API-versioner som introducerats i befintliga resurstyper i globala Azure kanske omedelbart inte tillgänglig i alla regioner, suveräna moln eller Azure Stack. Du kan använda resursutforskaren i Azure-portalen om du vill visa en lista över de tillgängliga resursproviders och resurstyper API-versioner för ett moln. Sök efter Resursläsaren i alla tjänster-menyn. Expandera noden Providers i Resursläsaren för att returnera alla tillgängliga resursproviders, resurstyper och API-versioner i molnet.

Om du vill visa de tillgängliga API-versionen för alla typer av resurser i ett visst moln i Azure CLI kör du följande skript:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

Du kan också använda följande PowerShell-cmdlet:

```azurepowershell-interactive
Get-AzResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Referera till resursplatser med en parameter

En mall distribueras alltid till en resursgrupp som finns i en region. Förutom att distribuera själva har också varje resurs i en mall för en plats-egenskap som används för att ange den region som du distribuerar i. Om du vill utveckla din mall för att få konsekvens i molnet, måste ett dynamiskt sätt att referera till resursplatser, eftersom varje Azure Stack kan innehålla unika namn. Vanligtvis resurser som distribueras i samma region som resursgruppen, men för att stödja scenarier, till exempel interregionala programmets tillgänglighet, kan det vara användbart att sprida resurser över regioner.

Även om du skulle kunna hårdkoda regionnamn när du anger resursegenskaper i en mall, garanterar den här metoden inte att mallen kan distribueras till andra Azure Stack-miljöer, eftersom områdesnamnet troligen inte finns det.

Lägg till en indataparameter plats i mallen med ett standardvärde för att hantera olika regioner. Standardvärdet används om inget värde anges under distributionen. 

Mallfunktionen `[resourceGroup()]` returnerar ett objekt som innehåller följande nyckel/värde-par:

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

Genom att referera till nyckeln plats för objektet i defaultValue indataparameter, Azure Resource Manager, vid körning, ersätter den `[resourceGroup().location]` mallfunktionen med namnet på platsen för resursgruppen som mallen har distribuerats till.

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
    "name": "storageaccount1",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "location": "[parameters('location')]",
    ...
```

Med den här mallfunktionen distribuerar du mallen till valfritt moln utan även att känna till regionnamn i förväg. Dessutom kan en plats för en specifik resurs i mallen kan skilja sig från plats för resursgruppen. I det här fallet kan du konfigurera den med hjälp av ytterligare indataparametrar för den specifika resursen, medan andra resurser i samma mall fortfarande använda Indataparametern ursprungliga plats.

### <a name="track-versions-using-api-profiles"></a>Spåra versioner med hjälp av API-profiler

Det kan vara svårt att hålla reda på alla tillgängliga resursproviders och relaterade API-versioner som finns i Azure Stack. Till exempel vid tidpunkten för skrivs, den senaste API-versionen för **Microsoft.Compute/availabilitySets** i Azure är `2018-04-01`, medan den tillgängliga API versionen som är gemensamma för Azure och Azure Stack är `2016-03-30`. Vanliga API-version för **Microsoft.Storage/storageAccounts** delas mellan alla platser för Azure och Azure Stack är `2016-01-01`, medan den senaste API-versionen i Azure är `2018-02-01`.

Därför infördes begreppet API-profiler i Resource Manager-mallar. Utan API-profiler kan konfigureras varje resurs i en mall med en `apiVersion` element som beskriver API-versionen för den specifika resursen.

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
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
            "apiVersion": "2016-03-30",
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

En API-version för profilen fungerar som ett alias för en enda API-version per resurstyp som är gemensamma för Azure och Azure Stack. I stället för att ange en API-version för varje resurs i en mall kan du anger bara versionen för API-profilen i en ny rotelement som kallas `apiProfile` och utelämna den `apiVersion` element för enskilda resurser.

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
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
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

API-profilen säkerställer att API-versioner är tillgängliga på platser, så du inte behöver manuellt kontrollera apiVersions som är tillgängliga i en viss plats. För att säkerställa de API-versioner som refereras till av din API-profil finns i en miljö med Azure Stack, måste Azure Stack-operatörer hålla uppdaterade lösningen bygger på principen för support. Om ett system är mer än sex månader inaktuell, betraktas den som inkompatibel och miljön måste uppdateras.

API-profilen är inte ett obligatoriskt element i en mall. Även om du lägger till elementet det ska bara användas för resurser för vilka ingen `apiVersion` har angetts. Det här elementet tillåter ändringar av gradvis men kräver inte några ändringar i befintliga mallar.

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
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "name": "myavailabilityset",
            "type": "Microsoft.Compute/availabilitySets",
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

## <a name="check-endpoint-references"></a>Kontrollera endpoint referenser

Resurser kan ha referenser till andra tjänster på plattformen. En offentlig IP-adress kan till exempel ha en offentlig DNS-namn som tilldelats. Det offentliga molnet, suveräna moln och Azure Stack-lösningar har sina egna distinkta endpoint-namnområden. I de flesta fall kräver bara ett prefix i en resurs som indata i mallen. Lägger till slutpunktsvärdet till den under körning, Azure Resource Manager. Vissa endpoint-värden måste anges explicit i mallen. 

> [!NOTE]
> Om du vill utveckla mallar för molnet konsekvens, inte hårdkoda endpoint namnområden.

Följande två exempel är vanliga slutpunkt-namnområden som måste anges explicit när du skapar en resurs:

* Storage-konton (blob, kö, tabell och fil)
* Anslutningssträngar för databaser och Azure Cache för Redis

Slutpunkt-namnområden kan också användas i utdata från en mall som information för användaren när distributionen är klar. Här följer några vanliga exempel:

* Storage-konton (blob, kö, tabell och fil)
* Anslutningssträngar (MySql, SQLServer, SQLAzure, anpassad, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* domainNameLabel av en offentlig IP-adress
* Molntjänster

Undvik i allmänhet hårdkodad slutpunkter i en mall. Det bästa sättet är att använda mallfunktionen referens för att hämta slutpunkterna dynamiskt. Till exempel slutpunkten oftast hårdkodad är endpoint-namnområde för storage-konton. Varje lagringskonto har ett unikt FQDN som skapas genom att sammanfoga namnet på lagringskontot med slutpunkt-namnområdet. Ett blob storage-konto med namnet mystorageaccount1 leder till olika FQDN beroende på molnet:

* **mystorageaccount1.BLOB.Core.Windows.NET** när du skapade i det globala Azure-molnet.
* **mystorageaccount1.BLOB.Core.chinacloudapi.CN** när skapas i Azure i Kina-molnet.

Följande referens mallfunktionen hämtar endpoint namnområdet från lagringsresursprovidern:

```json
"diskUri":"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

Genom att ersätta värdet hårdkodad för slutpunkten för storage-konto med den `reference` mallfunktionen, du kan använda samma mall för att distribuera till olika miljöer har utan några ändringar endpoint-referensen.

### <a name="refer-to-existing-resources-by-unique-id"></a>Referera till befintliga resurser med unikt ID

Du kan också referera till en befintlig resurs från samma eller en annan resurs gruppen, och inom samma prenumeration eller en annan prenumeration inom samma klientorganisation i samma moln. Om du vill hämta resursegenskaper, måste du använda den unika identifieraren för själva resursen. Den `resourceId` mallfunktionen hämtar unikt ID för en resurs, till exempel SQL Server som i följande kod: 

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Du kan sedan använda den `resourceId` fungera i den `reference` funktionen för att hämta egenskaperna för en databas. Det returnera objektet innehåller de `fullyQualifiedDomainName` egenskap som innehåller värdet för fullständig slutpunkt. Det här värdet hämtas vid körning och tillhandahåller moln miljöspecifika endpoint namnområde. För att definiera anslutningssträngen utan hardcoding endpoint namnområdet, kan du referera till egenskapen returnerade objektets direkt i anslutningssträngen som visas:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Överväg att resursegenskaper

Specifika resurser i Azure Stack-miljöer har unika egenskaper som du måste tänka på i mallen.

### <a name="ensure-vm-images-are-available"></a>Kontrollera VM-avbildningar är tillgängliga

Azure tillhandahåller en omfattande uppsättning avbildningar av Virtuella datorer. De här avbildningarna skapas och sammanställts för distribution av Microsoft och partner. Bilderna utgör grunden för virtuella datorer på plattformen. Men en cloud-konsekventa mall vända sig till tillgängliga parametrar, i synnerhet, utgivare, erbjudande och SKU: N för VM-avbildningarna till den globala Azure, Azure suveräna moln eller en Azure Stack-lösning.

Om du vill hämta en lista över tillgängliga avbildningar av virtuella datorer på en plats kör du följande Azure CLI-kommando:

```azurecli-interactive
az vm image list -all
```

Du kan hämta samma lista med Azure PowerShell-cmdleten [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) och ange den plats du vill med den `-Location` parametern. Exempel:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "West Europe" | Get-AzVMImageOffer | Get-AzVMImageSku | Get-AzureRMVMImage
```

Det här kommandot tar ett par minuter att returnera alla tillgängliga avbildningar i regionen Europa, västra i globala Azure-molnet.

Om du har gjort dessa VM-avbildningar tillgängliga i Azure Stack, skulle all tillgänglig lagring användas. För att hantera även minsta skalningsenheten kan Azure Stack du välja de avbildningar som du vill lägga till en miljö.

Följande kodexempel visar en konsekvent metod för att referera till utgivare, erbjudande och SKU-parametrar i Azure Resource Manager-mallar:

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

### <a name="check-local-vm-sizes"></a>Kontrollera lokal VM-storlekar

Om du vill utveckla din mall för att få konsekvens i molnet, måste du kontrollera att önskad storlek för virtuell dator är tillgänglig i alla målmiljöer som. VM-storlekar är en gruppering av funktioner och prestandaegenskaper. Vissa VM-storlekarna är beroende av den maskinvara som den virtuella datorn körs på. Till exempel om du vill distribuera en GPU-optimerade virtuell dator, måste den maskinvara som kör hypervisor-programmet ha maskinvara GPU: er.

När Microsoft lanserar en ny storlek på virtuell dator som har vissa maskinvara beroenden, görs VM-storleken vanligtvis tillgänglig först i en liten delmängd av regioner i Azure-molnet. Senare kan få det tillgång till andra regioner och moln. Om du vill kontrollera att virtuella datorstorlek finns i varje moln som du distribuerar till, kan du hämta tillgängliga storlekar med följande Azure CLI-kommando:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Använd för Azure PowerShell:

```azurepowershell-interactive
Get-AzVMSize -Location "West Europe"
```

En fullständig lista över tillgängliga tjänster, se [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable).

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Kontrollera användningen av Azure Managed Disks i Azure Stack

Hanterade diskar referensen lagringen för en Azure-klient. Du kan använda hanterade diskar i stället för att uttryckligen skapa ett lagringskonto och ange URI för en virtuell hårddisk (VHD), för att utföra dessa åtgärder implicit när du distribuerar en virtuell dator. Hanterade diskar förbättrar tillgängligheten genom att placera alla diskar från virtuella datorer i samma tillgänglighetsuppsättning till olika lagringsenheter. Befintlig virtuell hårddisk kan dessutom konverteras från Standard till Premium storage betydligt mindre avbrott.

Även om hanterade diskar är på gång för Azure Stack, stöds de för närvarande inte. Tills de är, kan du utveckla molnet-konsekventa mallar för Azure Stack genom att ange VHD: er med hjälp av den `vhd` elementet i mallen för den Virtuella datorresursen som visas:

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

Däremot för att ange en konfiguration för hanterad disk i en mall, ta bort den `vhd` element från diskkonfigurationen.

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

Samma ändringar gäller även [datadiskar](../virtual-machines/windows/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Kontrollera att VM-tillägg är tillgängliga i Azure Stack

Ett annat övervägande för molnet konsekvens är användningen av [tillägg för virtuell dator](../virtual-machines/windows/extensions-features.md) konfigurera resurser i en virtuell dator. Inte alla VM-tillägg är tillgängliga i Azure Stack. En mall kan ange vilka resurser som är dedikerad till VM-tillägg, skapa beroenden och villkor i mallen.

Till exempel om du vill konfigurera en virtuell dator som kör Microsoft SQL Server konfigurera VM-tillägget SQL Server som en del malldistributionen. Vad händer om distributionsmallen innehåller också en programserver konfigurerad för att skapa en databas på den virtuella datorn som kör SQL Server. Förutom att också använda ett VM-tillägg för programservrarna, kan du konfigurera beroendet av programservern på lyckad avkastningen på SQL Server-VM-tillägg-resurs. Den här metoden garanterar att den virtuella datorn som kör SQL Server är konfigurerat och tillgängligt när programservern instrueras att skapa databasen.

Deklarativ för mallen kan du definiera sluttillstånd resurser och deras kommunikation mellan beroenden, medan plattformen tar hand om den logik som krävs för beroenden.

#### <a name="check-that-vm-extensions-are-available"></a>Kontrollera att VM-tillägg är tillgänglig

Det finns många typer av VM-tillägg. När du utvecklar mall för att få konsekvens i molnet, se till att använda de tillägg som är tillgängliga i alla regioner i mallen har som mål.

Att hämta en lista över VM-tillägg som är tillgängliga för en viss region (i det här exemplet `myLocation`), kör du följande Azure CLI-kommando:

```azurecli-interactive
az vm extension image list --location myLocation
```

Du kan också köra Azure PowerShell [Get-AzVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) cmdlet och Använd `-Location` att ange platsen för avbildningen av virtuella datorn. Exempel:

```azurepowershell-interactive
Get-AzVmImagePublisher -Location myLocation | Get-AzVMExtensionImageType | Get-AzVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Kontrollera att versioner är tillgängliga

Eftersom VM-tillägg är från första part Resource Manager-resurser, har de sina egna API-versioner. Så som visas i följande kod, är VM-tillägg-typ en kapslad resurs i Microsoft.Compute-resursprovidern.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

API-versionen av resursen för VM-tillägget måste finnas på alla platser som du planerar att rikta in med din mall. Plats-beroendet fungerar som resursprovider API-versionen tillgänglighet nämnts tidigare i avsnittet ”verifiera version av alla typer av resurser”.

Använd för att hämta en lista över de tillgängliga API-versionerna för VM-tillägg-resursen, den [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider) cmdlet med den **Microsoft.Compute** resursprovidern som visas:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Du kan också använda VM-tillägg i VM-skalningsuppsättningar. På samma platsvillkor gäller. Kontrollera att API-versioner är tillgängliga på alla platser som du planerar att distribuera till för att utveckla din mall för att få konsekvens i molnet. För att hämta API-versioner av VM-tillägget resursen för skalningsuppsättningar, använder du samma cmdlet som innan, men ange VM-skalningsuppsättningen anger resurstyp som visas:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Varje specifika tillägg är också en ny version. Den här versionen visas i den `typeHandlerVersion` egenskapen för VM-tillägget. Se till att versionen som anges i den `typeHandlerVersion` element i din mall VM-tillägg som är tillgängliga på platser där du planerar att distribuera mallen. Följande kod anger till exempel version 1.7:

```json
{
    "name": "MyCustomScriptExtension",
    "type": "extensions",
    "apiVersion": "2016-03-30",
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

Använd för att hämta en lista över tillgängliga versioner för en specifik VM-tillägget i [Get-AzVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) cmdlet. I följande exempel hämtar tillgängliga versioner för PowerShell DSC (Desired State Configuration) VM-tillägget från **myLocation**:

```azurepowershell-interactive
Get-AzVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

Hämta en lista över utgivare med den [Get-AzVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) kommando. Om du vill begära typ, använder den [Get-AzVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) uppskattar.

## <a name="tips-for-testing-and-automation"></a>Tips för att testa och automation

Det är svårt att hålla reda på alla relaterade inställningar, funktioner och begränsningar när du redigerar en mall. Vanlig metod är att utveckla och testa mallar mot ett enda moln innan andra platser som är mål. Men utförs den tidigare tester i redigering processen, mindre felsökning och omskrivningen av kod Utvecklingsteamet har att göra. Distributioner som misslyckas på grund av plats beroenden kan vara tidskrävande att felsöka. Därför rekommenderar vi automatiserade tester så tidigt som möjligt i redigering cykeln. Slutligen du behöver färre resurser och mindre utvecklingstid på och artefakterna cloud-konsekventa blir ännu mer värdefulla.

Följande bild visar ett typexempel på en utvecklingsprocess för ett team med hjälp av en integrerad utvecklingsmiljö (IDE). Under de olika faserna i tidslinjen körs olika testtyper. Här är två utvecklare som arbetar på samma lösning, men det här scenariot gäller lika för en enskild utvecklare eller en stor grupp. Varje utvecklare skapar vanligtvis en lokal kopia av en central lagringsplats, aktiverar var och en ska fungera på den lokala kopian utan att påverka de andra som kanske arbetar på samma filer.

![Arbetsflöde](./media/templates-cloud-consistency/workflow.png) 

Överväg följande tips för att testa och automation:

* Gör användning av testverktyg. Till exempel Visual Studio Code och Visual Studio innehåller IntelliSense och andra funktioner som kan hjälpa dig att validera dina mallar.
* Utföra statiska kodanalys med enhetstester och integreringstester för att förbättra kodkvaliteten på under utvecklingen på den lokala IDE. 
* För en ännu bättre upplevelse under inledande utvecklingsarbete enhetstester och integreringstester bör endast Varna när ett problem har hittats och fortsätter med testerna. På så sätt kan du identifiera problemen åtgärdas upptäcks och prioritera ordningen på ändringarna, även kallat testdriven distribution (TDD).
* Tänk på att vissa tester kan utföras utan att vara ansluten till Azure Resource Manager. Andra, kräver t.ex. testa malldistribution Resource Manager för att utföra vissa åtgärder som inte kan utföras offline.
* Testa en Distributionsmall mot verifieringen API: et är inte lika med en verklig distribution. Även om du distribuerar en mall från en lokal fil måste alla referenser till kapslade mallar i mallen hämtas av Resource Manager direkt, och artefakter som refereras av VM-tillägg hämtas av VM-agenten som körs i den distribuerade virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Överväganden för Azure Resource Manager-mall](../azure-stack/user/azure-stack-develop-templates.md)
* [Metodtips för Azure Resource Manager-mallar](resource-group-authoring-templates.md)
