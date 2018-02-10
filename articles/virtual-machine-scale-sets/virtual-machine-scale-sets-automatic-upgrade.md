---
title: Den automatiska OS uppgraderingar med virtuella Azure-datorn skala anger | Microsoft Docs
description: "Lär dig hur man automatiskt uppgraderar Operativsystemet på VM-instanser i en skaluppsättning"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: negat
ms.openlocfilehash: 59dad832977c4afc39db3773edf9789cd1a704e7
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Automatiska OS-uppgraderingar skaluppsättning för virtuell Azure-dator

Automatisk uppgradering för OS-avbildningen är en förhandsversion av funktionen för skalningsuppsättningar i virtuella Azure-datorn som uppgraderas automatiskt alla virtuella datorer till den senaste OS-avbildningen.

Automatisk uppgradering av Operativsystemet har följande egenskaper:

- När du konfigurerat tillämpas automatiskt den senaste OS-avbildningen som publicerats av avbildningen utgivare skaluppsättningen utan åtgärder från användaren.
- Uppgraderingar batchar av instanser i en rullande sätt varje gång en ny plattformsavbildning publiceras av utgivaren.
- Kan integreras med hälsoavsökningen för programmet (valfritt, men rekommenderas för säkerhet).
- Fungerar för alla VM-storlekar.
- Fungerar för Windows- och Linux-plattformen bilder.
- Du kan välja bort automatiska uppgraderingar när som helst (OS-uppgraderingar kan initieras manuellt).
- OS-Disk på en virtuell dator ersätts med nya OS-Disk som skapats med senaste Avbildningsversion. Konfigurerade tillägg och anpassade skript körs, medan beständiga data diskar bevaras.


## <a name="preview-notes"></a>Förhandsgranska anteckningar 
Följande begränsningar och restriktioner gäller i förhandsgranskningen:

- Automatisk OS uppgraderar endast stöder [fyra OS SKU: er](#supported-os-images). Det finns ingen SLA eller garantier. Vi rekommenderar att du inte använder automatiska uppgraderingar på kritiska produktionsarbetsbelastningar under förhandsgranskningen.
- Azure disk encryption (för närvarande under förhandsgranskning) är **inte** stöds för närvarande med virtual machine scale set automatisk OS uppgradering.


## <a name="register-to-use-automatic-os-upgrade"></a>Registrera dig för att använda automatisk uppgradering av Operativsystemet
Om du vill använda automatisk OS-uppgraderingsfunktionen registrera preview providern med [registrera AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) på följande sätt:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

Det tar cirka 10 minuter för registreringstillstånd att rapporten eftersom *registrerade*. Du kan kontrollera den aktuella registreringsstatusen med [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). När registreringen är klar kontrollerar du att den *Microsoft.Compute* provider är registrerad [registrera AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) på följande sätt:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

> [!NOTE]
> Service Fabric-kluster har sina egna begreppet programmets hälsotillstånd, men skaluppsättningar utan Service Fabric använder belastningsutjämningsavsökning för hälsotillstånd för att övervaka hälsotillståndet för programmet. Registrera providern funktionen för hälsoavsökningar genom att använda [registrera AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) på följande sätt:
>
> ```powershell
> Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
> ```
>
> Igen, det tar cirka 10 minuter för registreringstillstånd att rapporten eftersom *registrerade*. Du kan kontrollera den aktuella registreringsstatusen med [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). En gång registrerats se till att den *Microsoft.Network* provider är registrerad [registrera AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) på följande sätt:
>
> ```powershell
> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
> ```

## <a name="portal-experience"></a>Portalmiljö
När du följer ovanstående registrering går du till [Azure-portalen](https://aka.ms/managed-compute) att aktivera automatisk OS uppgraderingar på din skaluppsättningar och se förloppet för uppgraderingar:

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>Stöds operativsystemsavbildningar
Endast vissa bilder för OS-plattform som stöds för närvarande. Du det går inte att använder anpassade avbildningar som att du har skapat själv. Den *version* egenskap plattform måste anges till *senaste*.

Följande SKU: er stöds för närvarande (mer läggs):
    
| Utgivare               | Erbjudande         |  Sku               | Version  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | senaste   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | senaste   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | senaste   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | senaste   |



## <a name="application-health-without-service-fabric"></a>Programhälsan utan Service Fabric
> [!NOTE]
> Det här avsnittet gäller enbart för skalningsuppsättningar i utan Service Fabric. Service Fabric har sin egen begreppet programmets hälsotillstånd. När du använder automatisk OS-uppgraderingar med Service Fabric distribuerat ny operativsystemsavbildning Uppdateringsdomän av Uppdateringsdomän att upprätthålla hög tillgänglighet för de tjänster som körs i Service Fabric. Mer information om hållbarhet egenskaperna för Service Fabric-kluster finns [denna dokumentation](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

Under en OS-uppgradering VM-instanser i en skaluppsättning uppgraderas en batch i taget. Uppgraderingen bör bara fortsätta om kunden-programmet är felfritt på de uppgraderade VM-instanserna. Vi rekommenderar att programmet tillhandahåller hälsa signalerar till scale set OS uppgradera-motorn. Som standard under OS uppgraderingar anser plattformen VM energisparläge och tillägget Etableringsstatus för att avgöra om en VM-instans är felfri efter en uppgradering. OS-disk på en VM-instans ersätts under OS-uppgradering av en VM-instans med en ny disk baserat på senaste Avbildningsversion. När OS-uppgraderingen har slutförts, kör konfigurerade tillägg på dessa virtuella datorer. Endast när alla tillägg på en virtuell dator har etablerats anses programmet felfritt. 

En skalningsuppsättning kan alternativt konfigureras med programmet Hälsoavsökning att tillhandahålla plattform med korrekt information om den pågående statusen för programmet. Programmet Hälsoavsökning är anpassad belastningen belastningsutjämnaren-avsökningar som används som en signal hälsa. Det program som körs på en skala set VM-instans kan svara på externa HTTP eller TCP-begäranden om den är felfri. Mer information om hur anpassade läsa in belastningsutjämning-avsökningar fungerar finns i [förstå load balancer avsökningar](../load-balancer/load-balancer-custom-probe-overview.md). Ett program hälsa avsökning krävs inte för automatiska OS-uppgraderingar, men det rekommenderas starkt.

Om skaluppsättning är konfigurerad för att använda flera placering grupper, avsökningar med hjälp av en [Standard belastningsutjämnaren](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) behöver användas.

### <a name="important-keep-credentials-up-to-date"></a>Viktigt: Kontinuerligt autentiseringsuppgifter
Om din skaluppsättning använder inga autentiseringsuppgifter för åtkomst till externa resurser, till exempel om en VM-tillägget har konfigurerats som använder en SAS-token för storage-konto behöver du kontrollera att autentiseringsuppgifterna är uppdaterade. Om alla eventuella autentiseringsuppgifter, inklusive certifikat och token har upphört att gälla, uppgraderingen misslyckas och första batch för virtuella datorer ska lämnas i ett felaktigt tillstånd.

Rekommenderade åtgärder för att återställa virtuella datorer och aktivera automatisk uppgradering av Operativsystemet på nytt om det finns en resurs autentiseringsfel är:

* Generera token (eller andra autentiseringsuppgifter) som skickades till ditt paket.
* Kontrollera att eventuella autentiseringsuppgifter som används från inuti den virtuella datorn för att kommunicera med externa enheter är uppdaterad.
* Uppdatera paket i scale set modellen med en ny token.
* Distribuera den uppdaterade skaluppsättning, vilket uppdaterar alla VM-instanser, inklusive de misslyckades. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Ange hur du konfigurerar en anpassad belastningen belastningsutjämnaren avsökning som programmet hälsa avsökning på en skala
Som bästa praxis, skapa en belastningsutjämningsavsökning explicit för skaluppsättning för hälsotillstånd. Samma slutpunkten för en befintlig HTTP-avsökningen eller TCP-avsökning kan användas men ett hälsoavsökningen kan kräva olika beteenden från en traditionell belastningsutjämnare avsökning. En traditionell belastningsutjämningsavsökning kan returnera feltillstånd om belastningen på instansen är för högt som inte kan vara lämpligt för att fastställa instans hälsa under en automatisk uppgradering av Operativsystemet. Konfigurera avsökningen så att den har en hög andel avsöknings mindre än två minuter.

Avsökningen belastningsutjämnare kan referera till den *networkProfile* ange omfattning och kan vara associerat med antingen en intern eller offentlig Internetriktade belastningsutjämnare på följande sätt:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Tillämpa princip för versionsuppgradering en OS bild över din prenumeration
Säker uppgraderingar rekommenderas att tillämpa en princip för versionsuppgradering. Den här principen kan kräva programhälsan avsökningar i din prenumeration. Följande Azure Resource Manager-princip avvisar distributioner som inte har automatisk OS-avbildningen uppgradera inställningarna:

1. Hämta definitionen för inbyggda Azure Resource Manager-principen med [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) på följande sätt:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Tilldela principen till en prenumeration med [ny AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) på följande sätt:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>Konfigurera automatiska uppdateringar
För att konfigurera automatiska uppgraderingar, kontrollera att den *automaticOSUpgrade* egenskap är inställd på *SANT* ange modell-definition i skalan. Du kan konfigurera den här egenskapen med Azure PowerShell eller Azure CLI 2.0.

I följande exempel används Azure PowerShell (4.4.1 eller senare) att konfigurera automatiska uppgraderingar för skaluppsättningen namngivna *myVMSS* i resursgrupp med namnet *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


I följande exempel används Azure CLI (2.0.20 eller senare) att konfigurera automatiska uppgraderingar för skaluppsättningen namngivna *myVMSS* i resursgrupp med namnet *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Kontrollera status för en automatisk OS-uppgradering
Du kan kontrollera status för senaste OS uppgraderingen utförs på nivå med Azure PowerShell, Azure CLI 2.0 eller REST-API: er.

### <a name="azure-powershell"></a>Azure PowerShell
I följande exempel används Azure PowerShell (4.4.1 eller senare) att kontrollera status för skaluppsättningen namngivna *myVMSS* i resursgrupp med namnet *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
I följande exempel används Azure CLI (2.0.20 eller senare) att kontrollera status för skaluppsättningen namngivna *myVMSS* i resursgrupp med namnet *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST-API
I följande exempel används REST API för att kontrollera status för skaluppsättningen namngivna *myVMSS* i resursgrupp med namnet *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

GET-anrop returnerar egenskaper som liknar följande exempel utdata:

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


## <a name="automatic-os-upgrade-execution"></a>Automatisk uppgradering av OS-körning
För att expandera för användning av programmet hälsoavsökningar kör scale set OS uppgraderingar du följande steg:

1. Om mer än 20% av instanser är ohälsosamma, stoppar du uppgraderingen; Annars Fortsätt.
2. Identifiera VM-instanser för att uppgradera med hjälp av en batch med högst 20% av totalt antal instanser nästa batch.
3. Uppgradera Operativsystemet för nästa batch för VM-instanser.
4. Om mer än 20% av uppgraderade instanser är ohälsosamma, stoppar du uppgraderingen; Annars Fortsätt.
5. Om kunden har konfigurerat programmet Hälsoavsökning kan uppgraderingen väntar upp till 5 minuter för avsökningar ska bli felfri och sedan omedelbart fortsätter till nästa batch; Annars väntar den 30 minuter innan du fortsätter till nästa batch.
6. Om det finns återstående instanser som du vill uppgradera, gå till steg 1) för nästa batch; Annars är uppgraderingen slutförd.

Uppgradera OS-motorn söker efter den övergripande hälsan för VM-instansen innan du uppgraderar varje batch skaluppsättning. När du uppgraderar en grupp, kan det finnas andra samtidiga planerad eller oplanerad underhåll som händer i Azure-datacenter som kan påverka tillgängligheten för din virtuella dator. Därför är det möjligt att tillfälligt fler än 20% instanser kanske inte körs. I sådana fall ange i slutet av aktuell batch skalan uppgradera Stopp.


## <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda följande mall för att distribuera en skalningsuppsättning som använder automatiska uppgraderingar <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatisk rullande uppgraderingar - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"> <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Nästa steg
Fler exempel på hur du använder automatisk OS-uppgraderingar med skaluppsättningar finns i [GitHub-repo för förhandsgranskningsfunktioner](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
