---
title: Automatisk uppgradering av operativ system avbildningar med skalnings uppsättningar för virtuella Azure-datorer | Microsoft Docs
description: Lär dig hur du automatiskt uppgraderar OS-avbildningen på virtuella dator instanser i en skalnings uppsättning
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2019
ms.author: manayar
ms.openlocfilehash: a9829f380200e616d242f5406b72593014f0efc2
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656550"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Automatisk uppgradering av operativ system avbildningar för virtuella Azure-datorer

Om du aktiverar automatiska uppgraderingar av OS-avbildningar på din skalnings uppsättning kan du under lätta uppdaterings hanteringen på ett säkert och automatiskt uppgradera operativ system disken för alla instanser i skalnings uppsättningen.

Automatisk uppgradering av operativ system har följande egenskaper:

- När den har kon figurer ATS, tillämpas den senaste OS-avbildningen som publicerats av avbildnings utgivare automatiskt på skalnings uppsättningen utan att användaren behöver göra
- Uppgraderar batchar av instanser på ett rullandet sätt varje gång en ny plattforms avbildning publiceras av utgivaren.
- Integrerar med program hälso avsökningar och [tillägg för program hälsa](virtual-machine-scale-sets-health-extension.md).
- Fungerar för alla VM-storlekar och för både Windows-och Linux-plattforms avbildningar.
- Du kan när som helst välja att inte utföra automatiska uppgraderingar (OS-uppgraderingar kan även initieras manuellt).
- Operativ system disken för en virtuell dator ersätts med den nya OS-disken som skapats med den senaste avbildnings versionen. Konfigurerade tillägg och anpassade data skript körs, medan beständiga data diskar bevaras.
- [](virtual-machine-scale-sets-extension-sequencing.md) Stöd för sekvensering stöds.
- Automatisk uppgradering av operativ system avbildningar kan aktive ras på en skalnings uppsättning i valfri storlek.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hur fungerar automatisk uppgradering av operativ system avbildning?

En uppgradering fungerar genom att ersätta operativ system disken för en virtuell dator med en ny disk som skapats med den senaste avbildnings versionen. Alla konfigurerade tillägg och anpassade data skript körs på OS-disken, medan beständiga data diskar bevaras. För att minimera avbrotts tiden för programmet sker uppgraderingar i batchar, med högst 20% av skalnings uppsättningen när som helst. Du kan också integrera en Azure Load Balancer program hälso avsökning eller [program hälso tillägg](virtual-machine-scale-sets-health-extension.md). Vi rekommenderar att du införlivar ett program pulsslag och verifierar att uppgraderingen lyckades för varje batch i uppgraderings processen.

Uppgraderings processen fungerar på följande sätt:
1. Innan du påbörjar uppgraderings processen ser Orchestrator till att högst 20% av instanserna i hela skalnings uppsättningen är ohälsosam (oavsett orsak).
2. Uppgraderings hanteraren identifierar batchen med de virtuella dator instanser som ska uppgraderas, med en batch som har högst 20% av det totala antalet instanser. För mindre skalnings uppsättningar med 5 eller färre instanser är batchstorleken för en uppgradering en virtuell dator instans.
3. OS-disken för den valda batchen av virtuella dator instanser ersätts med en ny OS-disk som skapats från den senaste avbildningen. Alla angivna tillägg och konfigurationer i skalnings uppsättnings modellen tillämpas på den uppgraderade instansen.
4. För skalnings uppsättningar med konfigurerade program hälso avsökningar eller program hälso tillägg, väntar uppgraderingen upp till 5 minuter för att instansen ska bli felfri innan du går vidare för att uppgradera nästa batch. Om en instans inte återställer hälso tillståndet i 5 minuter efter en uppgradering, återställs den tidigare operativ system disken för instansen som standard.
5. Uppgraderings hanteraren spårar även procent andelen av instanser som skadar en uppgradering. Uppgraderingen stoppas om fler än 20% av de uppgraderade instanserna blir felaktiga under uppgraderings processen.
6. Ovanstående process fortsätter tills alla instanser i skalnings uppsättningen har uppgraderats.

Skalnings uppsättningen operativ system uppgraderings Orchestrator kontrollerar om den övergripande skalnings uppsättnings hälsan innan du uppgraderar varje batch. När du uppgraderar en batch kan det finnas andra samtidiga planerade eller oplanerade underhålls aktiviteter som kan påverka hälso tillståndet för dina skalnings uppsättnings instanser. I sådana fall om fler än 20% av skalnings uppsättningens instanser blir felaktiga, stannar skalnings uppsättnings uppgraderingen i slutet av den aktuella batchen.

## <a name="supported-os-images"></a>OS-avbildningar som stöds
Det finns för närvarande inte stöd för vissa avbildningar av operativ system plattformen. Anpassade avbildningar stöds inte för närvarande.

Följande SKU: er stöds för närvarande (och fler läggs till regelbundet):

| Utgivare               | OS-erbjudande      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Falsk våg (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Data Center-med-containers |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Data Center-med-containers |
| Microsoft Corporation   | WindowsServer | Data Center Core-1903-med-containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Krav för att konfigurera automatisk uppgradering av operativ system avbildning

- *Versions* egenskapen för plattforms avbildningen måste anges till *senaste*.
- Använd program hälso avsökningar eller [program hälso tillägg](virtual-machine-scale-sets-health-extension.md) för icke-Service Fabric skalnings uppsättningar.
- Använd Compute API version 2018-10-01 eller högre.
- Se till att externa resurser som anges i skalnings uppsättnings modellen är tillgängliga och uppdaterade. Exempel på detta är SAS URI för start nytto Last i egenskaper för VM-tillägg, nytto Last i lagrings konto, referens till hemligheter i modellen med mera.
- För skalnings uppsättningar som använder virtuella Windows-datorer, från och med Compute API version 2019-03-01, måste egenskapen *virtualMachineProfile. osProfile. windowsConfiguration. enableAutomaticUpdates* anges till false i skalnings uppsättnings modellen definition. Egenskapen ovan aktiverar uppgraderingar i VM där "Windows Update" tillämpar operativ Systems korrigeringar utan att ersätta operativ system disken. Med automatiska uppgraderingar av OS-avbildningar aktiverade på din skalnings uppsättning, krävs ingen ytterligare uppdatering via "Windows Update".

### <a name="service-fabric-requirements"></a>Service Fabric krav

Om du använder Service Fabric, se till att följande villkor är uppfyllda:
-   Service Fabric [hållbarhets nivå](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) är silver eller guld och inte brons.
-   Service Fabric-tillägget i modell definitionen för skalnings uppsättningen måste ha TypeHandlerVersion 1,1 eller senare.
-   Hållbarhets nivån ska vara samma på Service Fabric klustret och Service Fabric tillägget i modell definitionen för skalnings uppsättningen.

Se till att hållbarhets inställningarna inte stämmer överens med Service Fabric klustret och Service Fabric tillägget, eftersom ett matchnings fel resulterar i uppgraderings fel. Du kan ändra hållbarhets nivåer enligt de rikt linjer som beskrivs på [den här sidan](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).

## <a name="configure-automatic-os-image-upgrade"></a>Konfigurera automatisk uppgradering av operativ system avbildning
Om du vill konfigurera automatisk uppgradering av OS-avbildningen kontrollerar du att egenskapen *automaticOSUpgradePolicy. enableAutomaticOSUpgrade* är inställd på *True* i skalnings uppsättningens modell definition.

### <a name="rest-api"></a>REST-API
I följande exempel beskrivs hur du ställer in automatiska OS-uppgraderingar i en skalnings uppsättnings modell:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [Update-AzVmss](/powershell/module/az.compute/update-azvmss) för att kontrol lera din skalnings uppsättning. I följande exempel konfigureras automatiska uppgraderingar för skalnings uppsättningen med namnet *myScaleSet* i resurs gruppen med namnet *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [AZ VMSS Update](/cli/azure/vmss#az-vmss-update) för att kontrol lera din skalnings uppsättning. Använd Azure CLI-2.0.47 eller senare. I följande exempel konfigureras automatiska uppgraderingar för skalnings uppsättningen med namnet *myScaleSet* i resurs gruppen med namnet *myResourceGroup*:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Använda program hälso avsökningar

Under en uppgradering av operativ systemet uppgraderas en batch i taget av virtuella dator instanser i en skalnings uppsättning. Uppgraderingen bör endast fortsätta om kund programmet är felfritt på de uppgraderade VM-instanserna. Vi rekommenderar att programmet ger hälso signaler till uppgraderings motorn för skalnings uppsättningens operativ system. Under operativ systemets uppgraderingar förväntas som standard VM-energi tillstånd och tillägg etablerings status för att avgöra om en VM-instans är felfri efter en uppgradering. Under operativ Systems uppgraderingen av en VM-instans ersätts OS-disken på en virtuell dator instans med en ny disk baserat på den senaste avbildnings versionen. När uppgraderingen av operativ systemet har slutförts körs de konfigurerade tilläggen på de virtuella datorerna. Programmet betraktas som felfritt när alla tillägg på instansen har kon figurer ATS.

En skalnings uppsättning kan alternativt konfigureras med program hälso avsökningar för att tillhandahålla en plattform med korrekt information om det pågående tillståndet för programmet. Program hälso avsökningar är anpassade Load Balancer avsökningar som används som en hälso signal. Programmet som körs på en virtuell dator instans för skalnings uppsättning kan svara på externa HTTP-eller TCP-begäranden som anger om det är felfritt. Mer information om hur anpassade Load Balancer avsökningar fungerar finns i så [](../load-balancer/load-balancer-custom-probe-overview.md)här fungerar belastnings Utjämnings avsökningar. Program hälso avsökningar stöds inte för Service Fabric skalnings uppsättningar. Icke-Service Fabric skalnings uppsättningar kräver antingen Load Balancer program hälso avsökningar eller [program hälso tillägg](virtual-machine-scale-sets-health-extension.md).

Om skalnings uppsättningen har kon figurer ATS för att använda flera placerings grupper, måste avsökningar som använder en [standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) användas.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurera en anpassad Load Balancer-avsökning som program hälso avsökning på en skalnings uppsättning
Vi rekommenderar att du skapar en belastnings Utjämnings avsökning uttryckligen för skalnings uppsättnings hälsa. Samma slut punkt för en befintlig HTTP-avsökning eller TCP-avsökning kan användas, men en hälso avsökning kan kräva olika beteenden från en traditionell belastnings Utjämnings avsökning. En traditionell belastnings Utjämnings avsökning kan till exempel returnera fel om inläsningen på instansen är för hög, men som inte är lämplig för att fastställa instans hälsan vid en automatisk uppgradering av operativ systemet. Konfigurera avsökningen så att den har en hög avsöknings hastighet på mindre än två minuter.

Belastnings Utjämnings avsökningen kan refereras i *networkProfile* i skalnings uppsättningen och kan kopplas till antingen en intern eller offentlig belastningsutjämnare enligt följande:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> När du använder automatiska OS-uppgraderingar med Service Fabric distribueras den nya operativ system avbildningen av uppdaterings domänen av en uppdaterings domän för att upprätthålla hög tillgänglighet för de tjänster som körs i Service Fabric. Om du vill använda automatiska operativ Systems uppgraderingar i Service Fabric klustret måste konfigureras för att använda silver hållbarhets nivån eller högre. Mer information om hållbarhets egenskaperna för Service Fabric kluster finns i [den här dokumentationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Håll dina autentiseringsuppgifter aktuella
Om din skalnings uppsättning använder autentiseringsuppgifter för att komma åt externa resurser, till exempel ett VM-tillägg som kon figurer ATS för att använda en SAS-token för lagrings kontot, kontrollerar du att autentiseringsuppgifterna har uppdaterats. Om autentiseringsuppgifter, inklusive certifikat och token, har upphört att gälla, Miss lyckas uppgraderingen och den första batchen av virtuella datorer kommer att lämnas i ett felaktigt tillstånd.

De rekommenderade stegen för att återställa virtuella datorer och återaktivera automatisk operativ system uppgradering om det uppstår ett autentiseringsfel är följande:

* Återskapa token (eller andra autentiseringsuppgifter) som skickats till dina tillägg.
* Se till att alla autentiseringsuppgifter som används inifrån den virtuella datorn för att kommunicera med externa entiteter är uppdaterade.
* Uppdatera tillägg i skalnings uppsättnings modellen med nya tokens.
* Distribuera den uppdaterade skalnings uppsättningen, som kommer att uppdatera alla VM-instanser inklusive de som misslyckats.

## <a name="using-application-health-extension"></a>Använda program hälso tillägg
Program hälso tillägget distribueras i en instans av en skalnings uppsättning för virtuella datorer och rapporter om VM-hälsa inifrån skalnings uppsättnings instansen. Du kan konfigurera tillägget för avsökning på en program slut punkt och uppdatera status för programmet på den instansen. Den här instans statusen kontrol leras av Azure för att avgöra om en instans är tillgänglig för uppgraderings åtgärder.

När tillägget rapporterar hälso tillstånd från en virtuell dator kan tillägget användas i situationer där externa avsökningar, till exempel program hälso avsökningar (som använder anpassade Azure Load Balancer [](../load-balancer/load-balancer-custom-probe-overview.md)avsökningar) inte kan användas.

Det finns flera sätt att distribuera program hälso tillägget till dina skalnings uppsättningar enligt beskrivningen i exemplen i [den här artikeln](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Hämta historiken för automatiska uppgraderingar av operativ system avbildningar
Du kan kontrol lera historiken för den senaste OS-uppgraderingen som har utförts på din skalnings uppsättning med Azure PowerShell, Azure CLI 2,0 eller REST API: erna. Du kan hämta historiken för de senaste fem uppgraderings försöken för operativ systemet under de senaste två månaderna.

### <a name="rest-api"></a>REST-API
I följande exempel används [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) för att kontrol lera status för skalnings uppsättningen med namnet *myScaleSet* i resurs gruppen med namnet *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

GET-anropet returnerar egenskaper som liknar följande exempel på utdata:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [Get-AzVmss](/powershell/module/az.compute/get-azvmss) för att kontrol lera din skalnings uppsättning. I följande exempel beskrivs hur du granskar uppgraderings status för operativ system för en skalnings uppsättning med namnet *myScaleSet* i resurs gruppen med namnet *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [AZ VMSS get-OS-Upgrade-History](/cli/azure/vmss#az-vmss-get-os-upgrade-history) för att kontrol lera din skalnings uppsättning. Använd Azure CLI-2.0.47 eller senare. I följande exempel beskrivs hur du granskar uppgraderings status för operativ system för en skalnings uppsättning med namnet *myScaleSet* i resurs gruppen med namnet *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Hur skaffar du den senaste versionen av en plattforms operativ system avbildning?

Du kan hämta tillgängliga avbildnings versioner för automatisk OS-uppgradering som stöds SKU: er med hjälp av exemplen nedan:

### <a name="rest-api"></a>REST-API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Aktivera uppgraderingar av OS-avbildningar manuellt
Med automatisk uppgradering av OS-avbildning aktive rad på din skalnings uppsättning behöver du inte utlösa avbildnings uppdateringar manuellt på din skalnings uppsättning. Operativ system uppgraderings Orchestrator kommer automatiskt att tillämpa den senaste tillgängliga avbildnings versionen på dina skalnings uppsättnings instanser utan någon manuell åtgärd.

I vissa fall där du inte vill vänta på att Orchestrator ska tillämpa den senaste avbildningen kan du utlösa en uppgradering av OS-avbildningen manuellt med hjälp av exemplen nedan.

> [!NOTE]
> Manuell utlösare av OS-avbildningsfiler ger inte automatiska återställnings funktioner. Om en instans inte återställer sitt hälso tillstånd efter en uppgraderings åtgärd kan den tidigare OS-disken inte återställas.

### <a name="rest-api"></a>REST-API
Använd [Start operativ systemets uppgraderings](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) -API-anrop för att starta en rullande uppgradering för att flytta alla instanser av en skalnings uppsättning för virtuella datorer till den senaste tillgängliga plattforms AVBILDNINGens OS Instanser som redan kör den senaste tillgängliga OS-versionen påverkas inte. Följande exempel beskriver hur du kan starta en rullande uppgradering av operativ systemet på en skalnings uppsättning med namnet *myScaleSet* i resurs gruppen med namnet *myResourceGroup*:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) för att kontrol lera din skalnings uppsättning. Följande exempel beskriver hur du kan starta en rullande uppgradering av operativ systemet på en skalnings uppsättning med namnet *myScaleSet* i resurs gruppen med namnet *myResourceGroup*:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [AZ VMSS rullande-Upgrade start](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) för att kontrol lera din skalnings uppsättning. Använd Azure CLI-2.0.47 eller senare. Följande exempel beskriver hur du kan starta en rullande uppgradering av operativ systemet på en skalnings uppsättning med namnet *myScaleSet* i resurs gruppen med namnet *myResourceGroup*:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda mallar för att distribuera en skalnings uppsättning med automatiska OS-uppgraderingar för avbildningar som stöds, till exempel [Ubuntu 16,04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Nästa steg
Fler exempel på hur du använder automatiska OS-uppgraderingar med skalnings uppsättningar finns i [GitHub-lagrings platsen](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
