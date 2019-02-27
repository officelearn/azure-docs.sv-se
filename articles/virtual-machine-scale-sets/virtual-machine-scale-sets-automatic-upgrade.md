---
title: Automatiska uppgraderingar av Operativsystemet avbildning med Azure-datorer med skalningsuppsättningar | Microsoft Docs
description: Lär dig hur du automatiskt uppgradera OS-avbildning på VM-instanser i en skalningsuppsättning
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
ms.date: 02/25/2019
ms.author: manayar
ms.openlocfilehash: 55eb81fd969e18ea25dd4194a532747ef5b7d4ca
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871748"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure VM-skalningsuppsättningen automatisk operativsystemuppgradering för avbildning

Aktivera automatisk OS-avbildning uppgraderar på dina scale set hjälper dig att enkelt uppdatera genom att på ett säkert sätt och automatiskt uppgradera OS-disken för alla instanser i skalningsuppsättningen.

Automatisk uppgradering av Operativsystemet har följande egenskaper:

- När du konfigurerat tillämpas automatiskt den senaste OS-avbildningen som publicerats av avbildningsutgivare i skalningsuppsättningen utan inblandning av användaren.
- Uppgraderingar batchar av instanser i ett rullande sätt varje gång en ny plattformsavbildning har publicerats av utgivaren.
- Kan integreras med program hälsokontroller av slutpunkter och [Programhälsa tillägget](virtual-machine-scale-sets-health-extension.md).
- Fungerar för alla storlekar för Virtuella datorer och för både Windows och Linux-avbildningar.
- Du kan välja bort automatiska uppgraderingar när som helst (OS-uppgraderingar kan initieras manuellt samt).
- OS-Disk för en virtuell dator ersätts med den nya OS-disken som skapats med senaste versionsnumret för avbildningen. Konfigurerade tillägg och anpassade skript körs, när bevarade data diskar finns kvar.
- [Ordningsföljd för](virtual-machine-scale-sets-extension-sequencing.md) stöds.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hur fungerar automatisk OS uppgradera fungera?

En uppgradering fungerar genom att ersätta OS-disk för en virtuell dator med en ny disk som skapats med den senaste avbildningsversionen. Alla konfigurerade tillägg och anpassade skript körs på OS-disken vid bevarade data diskar finns kvar. För att minimera nedtid, ske uppgraderingar batchvis med mer än 20% av skalningsuppsättningen uppgradera när som helst. Du kan också integrera en hälsoavsökning för Azure Load Balancer program eller [Programhälsa tillägget](virtual-machine-scale-sets-health-extension.md). Vi bör inkludera en program-pulsslag och validera uppgradering slutförd för varje batch under uppgraderingen.

Uppgraderingen fungerar på följande sätt:
1. Innan du påbörjar uppgraderingen bör säkerställer orchestrator att mer än 20% av instanserna i hela skaluppsättningen är ohälsosamt (av någon anledning).
2. Uppgradera orchestrator identifierar batch med VM-instanser för uppgradering med alla en batch med högst 20% av det totala instansantalet.
3. OS-disk markerad batch VM-instanser i ersätts med en ny OS-disk som skapats från den senaste avbildningen och alla angivna tillägg och konfigurationer i skalningsuppsättningsmodell tillämpas på den uppgraderade instansen.
4. För skalningsuppsättningar med konfigurerade programmet hälsoavsökningar eller tillägg för programmets hälsotillstånd väntar uppgraderingen upp till 5 minuter för instansen som ska fungerar felfritt, innan du fortsätter att uppgradera nästa batch.
5. Uppgradera orchestrator spårar också procentandelen förekomster som blivit felaktiga efter en uppgradering. Uppgraderingen avbryts om mer än 20% av uppgraderade instanser blivit defekt under uppgraderingsprocessen.
6. Ovanstående processen fortsätter tills alla instanser i skalningsuppsättningen har uppgraderats.

Skalningsuppsättningen OS uppgradera orchestrator söker efter den övergripande hälsan för scale set innan du uppgraderar varje batch. När du uppgraderar en batch, kan det finnas andra samtidiga planerat eller oplanerat underhållsaktiviteter som kan påverka hälsotillståndet för dina skalningsuppsättningsinstanser. I sådana fall om mer än 20% av den skalningsuppsättning instanser blivit defekt, skalningsuppsättningen sedan den uppgradera stoppas i slutet av den aktuella batchen.

## <a name="supported-os-images"></a>OS-avbildningar som stöds
Endast vissa bilder för OS-plattformen stöds för närvarande. Anpassade avbildningar stöds inte för närvarande.

Följande SKU: er stöds för närvarande (och mer läggs regelbundet):

| Utgivare               | OS-erbjudande      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016 Datacenter med behållare |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019 Datacenter med behållare |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Krav för att konfigurera automatisk uppgradering för avbildning av operativsystem

- Den *version* plattform bildens egenskap måste anges till *senaste*.
- Använd programmet hälsoavsökningar eller [Programhälsa tillägget](virtual-machine-scale-sets-health-extension.md) Service Fabric skala anger.
- Se till att externa resurser som anges i skalningsuppsättningen är tillgängliga och uppdaterade. Exemplen omfattar SAS-URI för startprogram nyttolast i egenskaperna för VM-tillägget, nyttolast i storage-konto, referera till hemligheter i modellen och mycket annat.

## <a name="configure-automatic-os-image-upgrade"></a>Konfigurera automatisk uppgradering för avbildning av operativsystem
Om du vill konfigurera automatisk uppgradering för avbildning av operativsystem, se till att den *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* är inställd på *SANT* i skalningsuppsättningen principuppsättningsdefinition modell.

### <a name="rest-api"></a>REST-API
I följande exempel beskrivs hur du ställer in automatiska uppgraderingar av Operativsystemet på en skalningsuppsättningen:

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
Använd den [uppdatering AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet för att kontrollera OS-uppgradering historik för din skalningsuppsättning. I följande exempel konfigureras automatiska uppgraderingar för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az vmss uppdatera](/cli/azure/vmss#az-vmss-update) att kontrollera OS uppgradera historiken för din skalningsuppsättning. Använda Azure CLI 2.0.47 eller senare. I följande exempel konfigureras automatiska uppgraderingar för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```azurecli-interactive
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Med hjälp av programmets hälsotillstånd avsökningar

Under en uppgradering av operativsystem, VM-instanser i en skalningsuppsättning uppgraderas en batch i taget. Uppgraderingen bör endast fortsätta om kund-programmet är felfritt på de uppgraderade VM-instanserna. Vi rekommenderar att programmet tillhandahåller hälsotillstånd signaler till scale set OS Upgrade-motorn. Som standard under Operativsystemuppgraderingar plattformen tar hänsyn till VM-energinivån och etableringsstatusen för att avgöra om en VM-instans är felfri efter en uppgradering. Under OS-uppgradering av en VM-instans ersätts OS-disken på en VM-instans med en ny disk baserat på senaste versionsnumret för avbildningen. När OS-uppgraderingen har slutförts körs tillägg som är konfigurerade på dessa virtuella datorer. Programmet tas i beaktande felfri endast när alla tillägg på instansen har etablerats.

En skalningsuppsättning kan alternativt konfigureras med programmet Hälsoavsökningar för att tillhandahålla en plattform med korrekt information om den pågående statusen för programmet. Programmet Hälsoavsökningar är anpassade Load Balancer kontrollerar som används som en hälsotillståndssignal. Det program som körs på en skalningsuppsättningens Virtuella datorinstans kan svara på externa HTTP eller TCP-begäranden som anger om den är felfri. Mer information om hur anpassade läsa in belastningsutjämnare avsökningar fungerar finns i så här [förstå avsökningar av belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md). Ett program Hälsosonden krävs inte för Service Fabric-skalningsuppsättningar, men rekommenderas. Icke-Service Fabric-skalningsuppsättningar kräver antingen belastningsutjämnaren programmet hälsoavsökningar eller [Programhälsa tillägget](virtual-machine-scale-sets-health-extension.md).

Om skalningsuppsättningen är konfigurerad för att använda flera placeringsgrupper, avsökningar med hjälp av en [Standardbelastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) behöver användas.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurera en anpassad Belastningsutjämnaravsökningen som programmet Hälsosonden på en skala ange
Som bästa praxis, skapa en belastningsutjämnaravsökning uttryckligen för skalningsuppsättningar hälsotillstånd. Samma slutpunkt för en befintlig HTTP-avsökning eller TCP-avsökning kan användas, men en hälsoavsökning kan kräva olika beteenden från en traditionell belastningsutjämnare avsökning. En traditionell belastningsutjämnaravsökning kan till exempel returnera ohälsosamt om belastningen på instansen är för hög, men som inte är lämplig för att fastställa hälsotillståndet för instansen under en automatisk uppgradering av operativsystem. Konfigurera avsökning om du vill ha en hög andel avsöknings mindre än två minuter.

Avsökningen belastningsutjämnare kan refereras i den *networkProfile* skalans ange och kan vara associerat med antingen en intern eller offentlig internetuppkopplade belastningsutjämnare enligt följande:

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
> När du använder automatisk Operativsystemuppgradering med Service Fabric, distribueras den nya operativsystemavbildningen Uppdateringsdomän av Uppdateringsdomänen att upprätthålla hög tillgänglighet för de tjänster som körs i Service Fabric. Om du vill använda automatiska uppgraderingar av Operativsystemet i Service Fabric måste klustret vara konfigurerad för att använda Hållbarhetsnivån Silver eller högre. Mer information om hållbarhet egenskaperna för Service Fabric-kluster finns i [den här dokumentationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Hålla det uppdaterade autentiseringsuppgifter
Om din skalningsuppsättning använder autentiseringsuppgifter för att komma åt externa resurser, till exempel om en VM-tillägget har konfigurerats som använder en SAS-token för storage-konto kan du kontrollera att autentiseringsuppgifterna är uppdaterade. Om några autentiseringsuppgifter, inklusive certifikat och token, har upphört att gälla, så misslyckas uppgraderingen och den första batchen med virtuella datorer kommer att lämnas i ett felaktigt tillstånd.

Rekommenderade åtgärder för att återställa virtuella datorer och aktivera automatisk uppgradering av operativsystem igen om ett fel uppstår autentisering resurs är:

* Återskapa token (eller andra autentiseringsuppgifter) som skickades till ditt paket.
* Kontrollera att alla autentiseringsuppgifter som används från inuti den virtuella datorn för att kommunicera med externa enheter är uppdaterade.
* Uppdatera paket i skalningsuppsättningen med de nya token.
* Distribuera den uppdaterade skalningsuppsättning som kommer att uppdatera alla VM-instanser, inklusive de misslyckade som.

## <a name="using-application-health-extension"></a>Med hjälp av programmets hälsotillstånd tillägget
Programmets hälsotillstånd-tillägget har distribuerats i en VM scale set-instans och rapporter på VM-hälsa i skalningsuppsättningens datorinstans. Du kan konfigurera tillägget för att avsökning på en programslutpunkt och uppdatera status för programmet på den instansen. Den här instansen kontrolleras av Azure för att avgöra om en instans är berättigade till åtgärder för uppgradering.

Som tillägget rapporter hälsotillståndet från en virtuell dator, tillägget kan användas i situationer där externa avsökningar, till exempel programmet Hälsoavsökningar (som använder anpassade Azure Load Balancer [avsökningar](../load-balancer/load-balancer-custom-probe-overview.md)) kan inte användas.

Det finns flera sätt att distribuera Programhälsa tillägg till din skalningsuppsättning anger som beskrivs i exemplen i [i den här artikeln](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Hämta historiken för automatisk operativsystemuppgradering för avbildning
Du kan kontrollera historiken för de senaste OS uppgraderingen utförs på din skalningsuppsättning med Azure PowerShell, Azure CLI 2.0 eller REST-API: er. Du kan hämta historiken för de sista fem OS-uppgraderingsförsök inom de senaste två månaderna.

### <a name="rest-api"></a>REST-API
I följande exempel används [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) att kontrollera status för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

GET-anrop returnerar egenskaperna som liknar följande Exempelutdata:

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
Använd den [Get-AzVmss](/powershell/module/az.compute/get-azvmss) cmdlet för att kontrollera OS-uppgradering historik för din skalningsuppsättning. I följande exempel beskriver hur du granskar OS uppgraderingsstatusen för en skalningsuppsättning med namnet *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az vmss get-os-uppgradering – historik](/cli/azure/vmss#az-vmss-get-os-upgrade-history) att kontrollera OS uppgradera historiken för din skalningsuppsättning. Använda Azure CLI 2.0.47 eller senare. I följande exempel beskriver hur du granskar OS uppgraderingsstatusen för en skalningsuppsättning med namnet *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Hur du hämtar den senaste versionen av en plattformsavbildning OS?

Du får versionerna som bild för automatisk operativsystemuppgradering godkända SKU: er med hjälp av den exemplen nedan:

### <a name="rest-api"></a>REST-API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda mallar för att distribuera en skalningsuppsättning med automatisk operativsystemuppgradering för stöds avbildningar som [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Nästa steg
Fler exempel på hur du använder automatisk operativsystemuppgradering med skalningsuppsättningar, granska de [GitHub-lagringsplatsen](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
