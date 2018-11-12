---
title: Automatiska uppgraderingar av Operativsystemet avbildning med Azure-datorer med skalningsuppsättningar | Microsoft Docs
description: Lär dig hur du automatiskt uppgradera OS-avbildning på VM-instanser i en skalningsuppsättning
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: c8ba9ac3150b5a84b2902afaaefcf78c76764fed
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036198"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure VM-skalningsuppsättningen automatisk operativsystemuppgradering för avbildning

Automatisk uppgradering av OS-avbildning är en funktion i Azure VM-skalningsuppsättningar som uppgraderar automatiskt alla virtuella datorer till den senaste OS-avbildningen.

Automatisk uppgradering av Operativsystemet har följande egenskaper:

- När du konfigurerat tillämpas automatiskt den senaste OS-avbildningen som publicerats av avbildningsutgivare i skalningsuppsättningen utan inblandning av användaren.
- Uppgraderingar batchar av instanser i ett rullande sätt varje gång en ny plattformsavbildning har publicerats av utgivaren.
- Integreras med program hälsoavsökning.
- Fungerar för alla storlekar för Virtuella datorer och för både Windows och Linux-avbildningar.
- Du kan välja bort automatiska uppgraderingar när som helst (OS-uppgraderingar kan initieras manuellt samt).
- OS-Disk för en virtuell dator ersätts med den nya OS-disken som skapats med senaste versionsnumret för avbildningen. Konfigurerade tillägg och anpassade skript körs, när bevarade data diskar finns kvar.
- Azure disk encryption (i förhandsversion) stöds för närvarande inte.  

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hur fungerar automatisk OS uppgradera fungera?

En uppgradering fungerar genom att ersätta OS-disk för en virtuell dator med ett nytt lösenord som skapats med den senaste avbildningsversionen. Alla konfigurerade tillägg och anpassade data skript körs, när bevarade data diskar finns kvar. För att minimera nedtid, ske uppgraderingar i grupper med datorer, med mer än 20% av skalningsuppsättningen uppgradera när som helst. Du har också möjlighet att integrera en hälsoavsökning för Azure Load Balancer-programmet. Vi rekommenderar starkt att lägga till ett program-pulsslag och validera uppgradering slutförd för varje batch i uppgraderingsprocessen. Körningen stegen är: 

1. Innan du påbörjar uppgraderingen bör garanterar orchestrator att högst 20% av instanserna är defekta. 
2. Identifiera batch med VM-instanser för uppgradering med en batch med högst 20% av det totala instansantalet.
3. Uppgradera den OS-avbildningen av den här batchen med VM-instanser.
4. Om kunden har konfigurerat Application hälsoavsökningar, väntar uppgraderingen upp till 5 minuter för avsökningar till fungerar felfritt, innan du fortsätter att uppgradera nästa batch. 
5. Om det finns återstående instanser för att uppgradera, gå till steg 1) för nästa batch; Annars har uppgraderingen slutförts.

Skalningsuppsättningen OS uppgradera orchestrator söker efter den övergripande hälsan för VM-instans innan du uppgraderar varje batch. När du uppgraderar en batch, kan det finnas andra samtidiga planerat eller oplanerat underhåll som händer i Azure-datacenter som kan påverka tillgängligheten för dina virtuella datorer. Därför är det möjligt att tillfälligt fler än 20% instanser kanske inte körs. I sådana fall skaluppsättningen i slutet av aktuell batch uppgradera stoppas.

## <a name="supported-os-images"></a>OS-avbildningar som stöds
Endast vissa bilder för OS-plattformen stöds för närvarande. Du det går inte att använder anpassade avbildningar som du har du skapat själv. 

Följande SKU: er stöds för närvarande (fler tillkommer framöver):
    
| Utgivare               | OS-erbjudande      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04 LTS *        | 
| Rogue Wave (OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | Stable             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016 Datacenter med behållare |

* Stöd för dessa avbildningar för närvarande är aktiv och snart blir tillgängligt i alla Azure-regioner. 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Krav för att konfigurera automatisk uppgradering för avbildning av operativsystem

- Den *version* plattform bildens egenskap måste anges till *senaste*.
- Använd programmet hälsoavsökningar för skalningsuppsättningar som inte är Service Fabric.
- Se till att resurserna som skalningsuppsättningsmodell hänvisar till är tillgänglig och hålls uppdaterad. 
  Exa.SAS URI för start av nyttolast i VM-egenskaperna för tillägget, nyttolast i storage-konto, referera till hemligheter i modellen. 

## <a name="configure-automatic-os-image-upgrade"></a>Konfigurera automatisk uppgradering för avbildning av operativsystem
Om du vill konfigurera automatisk uppgradering för avbildning av operativsystem, se till att den *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* är inställd på *SANT* i skalningsuppsättningen principuppsättningsdefinition modell. 

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

I följande exempel används Azure CLI (2.0.47 eller senare) att konfigurera automatiska uppgraderingar för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```azurecli
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```
Stöd för att konfigurera den här egenskapen via Azure PowerShell kommer att lanseras snart.

## <a name="using-application-health-probes"></a>Med hjälp av programmets hälsotillstånd avsökningar 

Under en uppgradering av operativsystem, VM-instanser i en skalningsuppsättning uppgraderas en batch i taget. Uppgraderingen bör endast fortsätta om kund-programmet är felfritt på de uppgraderade VM-instanserna. Vi rekommenderar att programmet tillhandahåller hälsotillstånd signaler till scale set OS Upgrade-motorn. Som standard under Operativsystemuppgraderingar plattformen tar hänsyn till VM-energinivån och etableringsstatusen för att avgöra om en VM-instans är felfri efter en uppgradering. Under OS-uppgradering av en VM-instans ersätts OS-disken på en VM-instans med en ny disk baserat på senaste versionsnumret för avbildningen. När OS-uppgraderingen har slutförts körs tillägg som är konfigurerade på dessa virtuella datorer. Endast när alla tillägg på en virtuell dator har etablerats anses programmet vara felfritt. 

En skalningsuppsättning kan alternativt konfigureras med programmet Hälsoavsökningar för att tillhandahålla en plattform med korrekt information om den pågående statusen för programmet. Programmet Hälsoavsökningar är anpassade Load Balancer kontrollerar som används som en hälsotillståndssignal. Det program som körs på en skalningsuppsättningens Virtuella datorinstans kan svara på externa HTTP eller TCP-begäranden som anger om den är felfri. Mer information om hur anpassade läsa in belastningsutjämnare avsökningar fungerar finns i så här [förstå avsökningar av belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md). Ett program Hälsosonden krävs inte för automatiska uppgraderingar av Operativsystemet, men rekommenderas.

Om skalningsuppsättningen är konfigurerad för att använda flera placeringsgrupper, avsökningar med hjälp av en [Standardbelastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) behöver användas.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurera en anpassad Belastningsutjämnaravsökningen som programmet Hälsosonden på en skala ange
Som bästa praxis, skapa en belastningsutjämnaravsökning uttryckligen för skalningsuppsättningar hälsotillstånd. Samma slutpunkt för en befintlig HTTP-avsökning eller TCP-avsökning kan användas, men en hälsoavsökning kan kräva olika beteenden från en traditionell belastningsutjämnare avsökning. En traditionell belastningsutjämnaravsökning kan till exempel returnera feltillstånd om belastningen på instansen är för hög medan som inte kanske är lämpliga för att fastställa hälsotillståndet för instansen under en automatisk uppgradering av operativsystem. Konfigurera avsökning om du vill ha en hög andel avsöknings mindre än två minuter.

Avsökningen belastningsutjämnare kan refereras i den *networkProfile* skalans ange och kan vara associerat med antingen en intern eller offentlig internetuppkopplade belastningsutjämnare enligt följande:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```
> [!NOTE]
> När du använder automatisk Operativsystemuppgradering med Service Fabric, distribueras den nya operativsystemavbildningen Uppdateringsdomän av Uppdateringsdomänen att upprätthålla hög tillgänglighet för de tjänster som körs i Service Fabric. Om du vill använda automatiska uppgraderingar av Operativsystemet i Service Fabric måste klustret vara konfigurerad för att använda Hållbarhetsnivån Silver eller högre. Mer information om hållbarhet egenskaperna för Service Fabric-kluster finns i [den här dokumentationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Hålla det uppdaterade autentiseringsuppgifter
Om din skalningsuppsättning använder autentiseringsuppgifter för att komma åt externa resurser, till exempel om en VM-tillägget har konfigurerats som använder en SAS-token för storage-konto behöver du kontrollera autentiseringsuppgifterna som hålls uppdaterade. Om några autentiseringsuppgifter, inklusive certifikat och token har upphört att gälla, så misslyckas uppgraderingen och den första batchen med virtuella datorer kommer att lämnas i ett felaktigt tillstånd.

Rekommenderade åtgärder för att återställa virtuella datorer och aktivera automatisk uppgradering av operativsystem igen om ett fel uppstår autentisering resurs är:

* Återskapa token (eller andra autentiseringsuppgifter) som skickades till ditt paket.
* Kontrollera att alla autentiseringsuppgifter som används från inuti den virtuella datorn för att kommunicera med externa enheter är uppdaterade.
* Uppdatera paket i skalningsuppsättningen med de nya token.
* Distribuera den uppdaterade skalningsuppsättning som kommer att uppdatera alla VM-instanser, inklusive de misslyckade som. 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Hämta historiken för automatisk operativsystemuppgradering för avbildning 
Du kan kontrollera historiken för de senaste OS uppgraderingen utförs på din skalningsuppsättning med Azure PowerShell, Azure CLI 2.0 eller REST-API: er. Du kan hämta historiken för de sista fem OS-uppgraderingsförsök inom de senaste två månaderna.

### <a name="azure-powershell"></a>Azure PowerShell
I följande exempel används Azure PowerShell för att kontrollera status för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
I följande exempel används Azure CLI (2.0.47 eller senare) att kontrollera status för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```azurecli
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST-API
I följande exempel används REST API för att kontrollera status för skalningsuppsättningen *myVMSS* i resursgruppen med namnet *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
Läs dokumentationen för detta API här: https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory.

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

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Hur du hämtar den senaste versionen av en plattformsavbildning OS? 

Du får versionerna som bild för automatisk operativsystemuppgradering godkända SKU: er med hjälp av den exemplen nedan: 

```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

```azurecli
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda följande mall för att distribuera en skalningsuppsättning som använder automatiska uppgraderingar <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>automatisk rullande uppgraderingar - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Nästa steg
Fler exempel på hur du använder automatisk operativsystemuppgradering med skalningsuppsättningar finns i den [GitHub-lagringsplatsen för förhandsversionsfunktioner](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
