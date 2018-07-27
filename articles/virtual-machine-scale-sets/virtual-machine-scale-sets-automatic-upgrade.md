---
title: Automatisk operativsystemuppgradering med virtuella Azure-datorer med skalningsuppsättningar | Microsoft Docs
description: Lär dig hur du automatiskt uppgradera Operativsystemet på VM-instanser i en skalningsuppsättning
services: virtual-machine-scale-sets
documentationcenter: ''
author: yeki
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: yeki
ms.openlocfilehash: 6b20ef98e008d9c5d984ba29eed894b1c5ec8c09
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263256"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Automatisk operativsystemuppgradering för Azure VM-skalningsuppsättningen

Automatisk uppgradering av OS-avbildning är en förhandsgranskningsfunktion för Azure VM scale sets som uppgraderar automatiskt alla virtuella datorer till den senaste OS-avbildningen.

Automatisk uppgradering av Operativsystemet har följande egenskaper:

- När du konfigurerat tillämpas automatiskt den senaste OS-avbildningen som publicerats av avbildningsutgivare i skalningsuppsättningen utan inblandning av användaren.
- Uppgraderingar batchar av instanser i ett rullande sätt varje gång en ny plattformsavbildning har publicerats av utgivaren.
- Kan integreras med program hälsoavsökning (valfritt, men rekommenderas för säkerhet).
- Fungerar för alla storlekar.
- Fungerar för Windows och Linux-avbildningar.
- Du kan välja bort automatiska uppgraderingar när som helst (OS-uppgraderingar kan startas manuellt och).
- OS-Disk för en virtuell dator ersätts med den nya OS-disken som skapats med senaste versionsnumret för avbildningen. Konfigurerade tillägg och anpassade skript körs, när bevarade data diskar finns kvar.


## <a name="preview-notes"></a>Förhandsversion av information 
I förhandsversionen, gäller följande begränsningar och restriktioner:

- Automatisk OS uppgraderas endast stöder [fyra OS SKU: er](#supported-os-images). Det finns inget serviceavtal eller garantier. Vi rekommenderar att du inte använder automatiska uppgraderingar på kritiska arbetsbelastningar för produktion under förhandsversionen.
- Azure disk encryption är **inte** stöds för närvarande med VM scale set automatisk uppgradering av operativsystem.


## <a name="register-to-use-automatic-os-upgrade"></a>Registrera dig för att använda automatisk OS-uppgradering
Registrera providern förhandsversion med Azure Powershell eller Azure CLI 2.0 för att använda den automatiska uppgraderingsfunktionen i OS.

### <a name="powershell"></a>PowerShell

1. Registrera med [registrera-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature):

     ```powershell
     Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
     ```

2. Det tar cirka 10 minuter för registreringstillstånd rapporten som *registrerad*. Du kan kontrollera den aktuella registreringsstatusen med [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). 

3. När registrerad, bekräftar du att den *Microsoft.Compute* providern är registrerad. I följande exempel används Azure Powershell med [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

     ```powershell
     Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
     ```


### <a name="cli-20"></a>CLI 2.0

1. Registrera med [az funktionen registrera](/cli/azure/feature#az-feature-register):

     ```azurecli
     az feature register --name AutoOSUpgradePreview --namespace Microsoft.Compute
     ```

2. Det tar cirka 10 minuter för registreringstillstånd rapporten som *registrerad*. Du kan kontrollera den aktuella registreringsstatusen med [az funktionen show](/cli/azure/feature#az-feature-show). 
 
3. När registrerad, se till att den *Microsoft.Compute* providern är registrerad. I följande exempel används Azure CLI (2.0.20 eller senare) med [az provider register](/cli/azure/provider#az-provider-register):

     ```azurecli
     az provider register --namespace Microsoft.Compute
     ```

> [!NOTE]
> Service Fabric-kluster har sina egna begreppet programmets hälsotillstånd, men skalningsuppsättningar utan Service Fabric använder hälsoavsökningen för belastningsutjämnaren för att övervaka programmets hälsotillstånd. 
>
> ### <a name="azure-powershell"></a>Azure PowerShell
>
> 1. Registrera funktionen provider för hälsoavsökning med [registrera-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature):
>
>      ```powershell
>      Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
>      ```
>
> 2. Igen, det tar cirka 10 minuter för registreringstillstånd rapporten som *registrerad*. Du kan kontrollera den aktuella registreringsstatusen med [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature)
>
> 3. En gång registrerats kontrollerar du att den *Microsoft.Network* providern är registrerad med [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):
>
>      ```powershell
>      Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
>      ```
>
>
> ### <a name="cli-20"></a>CLI 2.0
>
> 1. Registrera funktionen provider för hälsoavsökning med [az funktionen registrera](/cli/azure/feature#az-feature-register):
>
>      ```azurecli
>      az feature register --name AllowVmssHealthProbe --namespace Microsoft.Network
>      ```
>
> 2. Igen, det tar cirka 10 minuter för registreringstillstånd rapporten som *registrerad*. Du kan kontrollera den aktuella registreringsstatusen med [az funktionen show](/cli/azure/feature#az-feature-show). 
>
> 3. En gång registrerats kontrollerar du att den *Microsoft.Network* providern är registrerad med [az provider register](/cli/azure/provider#az-provider-register) på följande sätt:
>
>      ```azurecli
>      az provider register --namespace Microsoft.Network
>      ```

## <a name="portal-experience"></a>Portalmiljö
När du har följt stegen för registrering ovan måste du gå till [Azure-portalen](https://aka.ms/managed-compute) att aktivera automatisk operativsystemuppgradering på dina skalningsuppsättningar och se förloppet för uppgraderingar:

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>OS-avbildningar som stöds
Endast vissa bilder för OS-plattformen stöds för närvarande. Du det går inte att använder anpassade avbildningar som du har skapat själv. Den *version* plattform bildens egenskap måste anges till *senaste*.

Följande SKU: er stöds för närvarande (mer kommer att läggas till):
    
| Utgivare               | Erbjudande         |  Sku               | Version  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | senaste   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | senaste   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | senaste   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | senaste   |
| MicrosoftWindowsServer  | WindowsServer | 2016 Datacenter med behållare | senaste   |



## <a name="application-health-without-service-fabric"></a>Programmets hälsotillstånd utan Service Fabric
> [!NOTE]
> Det här avsnittet gäller endast för skalningsuppsättningar utan Service Fabric. Service Fabric har sin egen begreppet programmets hälsotillstånd. När du använder automatisk Operativsystemuppgradering med Service Fabric, distribueras den nya operativsystemavbildningen Uppdateringsdomän av Uppdateringsdomänen att upprätthålla hög tillgänglighet för de tjänster som körs i Service Fabric. Mer information om hållbarhet egenskaperna för Service Fabric-kluster finns i [den här dokumentationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

Under en uppgradering av operativsystem, VM-instanser i en skalningsuppsättning uppgraderas en batch i taget. Uppgraderingen bör endast fortsätta om kund-programmet är felfritt på de uppgraderade VM-instanserna. Därför måste programmet ange hälsotillstånd signaler till scale set OS Upgrade-motorn. Under OS-uppgraderingar plattformen tar hänsyn till VM-energinivån och etableringsstatusen för att avgöra om en VM-instans är felfri efter en uppgradering. Under OS-uppgradering av en VM-instans ersätts OS-disken på en VM-instans med en ny disk baserat på senaste versionsnumret för avbildningen. När OS-uppgraderingen har slutförts körs tillägg som är konfigurerade på dessa virtuella datorer. Endast när alla tillägg på en virtuell dator har etablerats anses programmet vara felfritt. 

Dessutom skalningsuppsättningen *måste* konfigureras med programmet Hälsoavsökningar för att tillhandahålla en plattform med rätt information om den pågående statusen för programmet. Programmet Hälsoavsökningar är anpassade Load Balancer kontrollerar som används som en hälsotillståndssignal. Det program som körs på en skalningsuppsättningens Virtuella datorinstans kan svara på externa HTTP eller TCP-begäranden som anger om den är felfri. Mer information om hur anpassade läsa in belastningsutjämnare avsökningar fungerar finns i så här [förstå avsökningar av belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md).

Om skalningsuppsättningen är konfigurerad för att använda flera placeringsgrupper, avsökningar med hjälp av en [Standardbelastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) behöver användas.

### <a name="important-keep-credentials-up-to-date"></a>Viktigt: Håll autentiseringsuppgifter som är uppdaterade
Om din skalningsuppsättning använder autentiseringsuppgifter för att få åtkomst till externa resurser, måste du kontrollera autentiseringsuppgifterna som hålls uppdaterade. Ett VM-tillägg kan till exempel vara konfigurerad för att använda en SAS-token för storage-konto. Om några autentiseringsuppgifter, inklusive certifikat och token har upphört att gälla, så misslyckas uppgraderingen och den första batchen med virtuella datorer kommer att lämnas i ett felaktigt tillstånd.

Rekommenderade åtgärder för att återställa virtuella datorer och aktivera automatisk uppgradering av operativsystem igen om ett fel uppstår autentisering resurs är:

* Återskapa token (eller andra autentiseringsuppgifter) som skickades till ditt paket.
* Se till att alla autentiseringsuppgifter som används från inuti den virtuella datorn för att kommunicera med externa enheter är uppdaterade.
* Uppdatera paket i skalningsuppsättningen med de nya token.
* Distribuera den uppdaterade skalningsuppsättning som kommer att uppdatera alla VM-instanser, inklusive de misslyckade som. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurera en anpassad Belastningsutjämnaravsökningen som programmet Hälsosonden på en skala ange
Du *måste* skapa en belastningsutjämnaravsökning uttryckligen för skalningsuppsättningar hälsotillstånd. Samma slutpunkt för en befintlig HTTP-avsökning eller TCP-avsökning kan användas, men en hälsoavsökning kan kräva olika beteenden från en traditionell belastningsutjämnare avsökning. En traditionell belastningsutjämnaravsökning kan till exempel returnera feltillstånd om belastningen på instansen är för hög. Däremot kanske som inte är lämpliga för att fastställa hälsotillståndet för instansen under en automatisk uppgradering av operativsystem. Konfigurera avsökning om du vill ha en hög andel avsöknings mindre än två minuter.

Avsökningen belastningsutjämnare kan refereras i den *networkProfile* skalans ange och kan vara associerat med antingen en intern eller offentlig internetuppkopplade belastningsutjämnare enligt följande:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Framtvinga en uppgraderingsprincip för OS-avbildning i hela prenumerationen
För säker uppgraderingar rekommenderar vi starkt att tillämpa en princip för uppgradering. Den här principen kan kräva att programmet hälsoavsökningar i din prenumeration. Följande Azure Resource Manager-princip avvisar distributioner som inte har automatisk OS-avbildning uppgradera inställningarna:

### <a name="powershell"></a>PowerShell
1. Hämta inbyggda Azure Resource Manager-principdefinitionen med [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) på följande sätt:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Tilldela principen till en prenumeration med [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) på följande sätt:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```

### <a name="cli-20"></a>CLI 2.0
Tilldela principen till en prenumeration med inbyggda Azure Resource Manager-principen:

```azurecli
az policy assignment create --display-name "Enforce automatic OS upgrades with app health checks" --name "Enforce automatic OS upgrades" --policy 465f0161-0087-490a-9ad9-ad6217f4f43a --scope "/subscriptions/<SubscriptionId>"
```

## <a name="configure-auto-updates"></a>Konfigurera automatiska uppdateringar
Om du vill konfigurera automatiska uppgraderingar, kontrollerar du att den *automaticOSUpgrade* är inställd på *SANT* i skalningsuppsättningen principuppsättningsdefinition modell. Du kan konfigurera den här egenskapen med Azure PowerShell eller Azure CLI 2.0.

### <a name="powershell"></a>PowerShell
I följande exempel används Azure PowerShell (4.4.1 eller senare) att konfigurera automatiska uppgraderingar för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```

### <a name="cli-20"></a>CLI 2.0
I följande exempel används Azure CLI (2.0.20 eller senare) att konfigurera automatiska uppgraderingar för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Kontrollera status för en automatisk uppgradering av operativsystem
Du kan kontrollera status för senaste OS uppgraderingen utförs på din skalningsuppsättning med Azure PowerShell, Azure CLI 2.0 eller REST-API: er.

### <a name="powershell"></a>PowerShell
I följande exempel används Azure PowerShell (4.4.1 eller senare) att kontrollera status för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="cli-20"></a>CLI 2.0
I följande exempel används Azure CLI (2.0.20 eller senare) att kontrollera status för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST-API
I följande exempel används REST API för att kontrollera status för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

GET-anrop returnerar egenskaperna som liknar följande Exempelutdata:

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>Körning av automatisk uppgradering av operativsystem
Om du vill expandera om användning av programmet hälsoavsökningar, kör scale set OS-uppgraderingar du följande steg:

1. Om fler än 20% av instanserna är ohälsosamma, stoppa uppgraderingen; Annars Fortsätt.
2. Identifiera nästa batch med VM-instanser för uppgradering med en batch med högst 20% av totalt instansantalet.
3. Uppgradera datorns operativsystem nästa batch med VM-instanser.
4. Om fler än 20% av uppgraderade instanser är inte felfri, stoppa uppgraderingen; Annars Fortsätt.
5. Uppgraderingen för skalningsuppsättningar som inte ingår i ett Service Fabric-kluster, väntar upp till 5 minuter för avsökningar till fungerar felfritt och genast fortsätter till nästa batch. För skalningsuppsättningar som ingår i ett Service Fabric-kluster, ange skalan väntar 30 minuter innan du fortsätter till nästa batch.
6. Om det finns återstående instanser för att uppgradera, gå till steg 1) för nästa batch; Annars har uppgraderingen slutförts.

OS-uppgradering motorn söker efter den övergripande hälsan för VM-instans innan du uppgraderar varje batch skalningsuppsättningen. När du uppgraderar en batch, kan det finnas andra samtidiga planerat eller oplanerat underhåll som händer i Azure-datacenter som kan påverka tillgängligheten för dina virtuella datorer. Därför är det möjligt att tillfälligt fler än 20% instanser kanske inte körs. I sådana fall skaluppsättningen i slutet av aktuell batch uppgradera stoppas.


## <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda följande mall för att distribuera en skalningsuppsättning som använder automatiska uppgraderingar <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatisk rullande uppgraderingar - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Nästa steg
Fler exempel på hur du använder automatisk operativsystemuppgradering med skalningsuppsättningar finns i den [GitHub-lagringsplatsen för förhandsversionsfunktioner](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
