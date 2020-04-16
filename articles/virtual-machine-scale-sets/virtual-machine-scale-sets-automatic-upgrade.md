---
title: Automatiska os-avbildningsuppgraderingar med Azure-skalningsuppsättningar för virtuella datorer
description: Lär dig hur du automatiskt uppgraderar OS-avbildningen på VM-instanser i en skalningsuppsättning
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mimckitt
ms.openlocfilehash: ee6a25ac5a4cc7de8b8340afb186d170cc147a38
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393780"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure virtual machine scale set automatiska OS-avbildningsuppgraderingar

Om du aktiverar automatiska os-avbildningsuppgraderingar i skalningsuppsättningen kan du underlätta uppdateringshanteringen genom att på ett säkert sätt och automatiskt uppgradera OS-disken för alla instanser i skalningsuppsättningen.

Automatisk OS-uppgradering har följande egenskaper:

- När den senaste OS-avbildningen har konfigurerats av bildutgivare tillämpas den automatiskt på skalningsuppsättningen utan att användaren behöver göra något.
- Uppgraderar batchar med instanser på ett rullande sätt varje gång en ny bild publiceras av utgivaren.
- Integrerar med programhälsoavsökningar och [application health-tillägg](virtual-machine-scale-sets-health-extension.md).
- Fungerar för alla VM-storlekar och för både Windows- och Linux-avbildningar.
- Du kan välja bort automatiska uppgraderingar när som helst (OS-uppgraderingar kan initieras manuellt också).
- Os-disken för en virtuell dator ersätts med den nya OS-disken som skapats med den senaste avbildningsversionen. Konfigurerade tillägg och anpassade dataskript körs, medan beständiga datadiskar behålls.
- [Förlängningssekvensering](virtual-machine-scale-sets-extension-sequencing.md) stöds.
- Automatisk os-avbildningsuppgradering kan aktiveras på en skalningsuppsättning av valfri storlek.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hur fungerar automatisk uppgradering av OS-avbildning?

En uppgradering fungerar genom att ersätta OS-disken på en virtuell dator med en ny disk som skapats med den senaste avbildningsversionen. Alla konfigurerade tillägg och anpassade dataskript körs på OS-disken, medan beständiga datadiskar behålls. För att minimera stilleståndstiden för programmet sker uppgraderingar i batchar, med högst 20 % av skalan inställd uppgradering när som helst. Du kan också integrera en hälsoavsökning för Azure Load Balancer-program eller [programhälsotillägg](virtual-machine-scale-sets-health-extension.md). Vi rekommenderar att du införlivar ett program hjärtslag och validerar uppgraderingsframgång för varje batch i uppgraderingsprocessen.

Uppgraderingsprocessen fungerar på följande sätt:
1. Innan uppgraderingsprocessen påbörjas ser orchestrator till att högst 20 % av instanserna i hela skalningsuppsättningen är felaktiga (av någon anledning).
2. Uppgraderingsorkesorker identifierar batchen med VM-instanser som ska uppgraderas, med en batch som har högst 20 % av det totala antalet instanser, med en minsta batchstorlek på en virtuell dator.
3. OS-disken för den valda batchen med VM-instanser ersätts med en ny OS-disk som skapats från den senaste avbildningen. Alla angivna tillägg och konfigurationer i skalningsuppsättningsmodellen tillämpas på den uppgraderade instansen.
4. För skalningsuppsättningar med konfigurerade programhälsoavsökningar eller Application Health-tillägg väntar uppgraderingen upp till 5 minuter för att instansen ska bli felfri innan du går vidare för att uppgradera nästa batch. Om en instans inte återställer sin hälsa inom 5 minuter efter en uppgradering återställs som standard den tidigare OS-disken för instansen.
5. Uppgraderingsorkestertor spårar också procentandelen instanser som blir felaktiga efter en uppgradering. Uppgraderingen stoppas om mer än 20 % av de uppgraderade instanserna blir felaktiga under uppgraderingsprocessen.
6. Ovanstående process fortsätter tills alla instanser i skalningsuppsättningen har uppgraderats.

Skalningsuppsättningen OS uppgradering orchestrator söker efter den övergripande skalan som hälsa innan du uppgraderar varje batch. När du uppgraderar en batch kan det finnas andra samtidiga planerade eller oplanerade underhållsaktiviteter som kan påverka hälsotillståndet för dina skalenade instanser. I sådana fall om mer än 20 % av skalningsuppsättningens instanser blir felaktiga, stoppas skalan inställd uppgradering i slutet av den aktuella batchen.

## <a name="supported-os-images"></a>Os-bilder som stöds
Endast vissa OS-plattformsavbildningar stöds för närvarande. Anpassat bildstöd är tillgängligt [i förhandsgranskning för](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images-preview) anpassade bilder via [Delat bildgalleri](shared-image-galleries.md).

Följande plattformSKU:er stöds för närvarande (och fler läggs till med jämna mellanrum):

| Utgivare               | OS-erbjudande      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-med-behållare |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-med-behållare |
| Microsoft Corporation   | WindowsServer | Datacenter-Core-1903-med-behållare-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Krav för att konfigurera automatisk uppgradering av OS-avbildning

- Versionsegenskapen för bilden måste vara *senaste*. *version*
- Använd programhälsoavsökningar eller [programhälsotillägg](virtual-machine-scale-sets-health-extension.md) för skaluppsättningar som inte är tjänsteinfrastruktur.
- Använd Compute API version 2018-10-01 eller senare.
- Kontrollera att externa resurser som anges i skalningsuppsättningsmodellen är tillgängliga och uppdaterade. Exempel är SAS URI för bootstrapping nyttolast i vm-tilläggsegenskaper, nyttolast i lagringskontot, referens till hemligheter i modellen med mera.
- För skalningsuppsättningar med windows virtuella datorer, som börjar med Compute API version 2019-03-01, måste egenskapen *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* egenskapen ange *false* i skaluppsättningsmodelldefinitionen. Egenskapen ovan möjliggör uppgraderingar i virtuella datorer där "Windows Update" tillämpar operativsystemkorrigeringar utan att byta ut OS-disken. Med automatiska os-avbildningsuppgraderingar aktiverade i din skalningsuppsättning krävs ingen ytterligare uppdatering via "Windows Update".

### <a name="service-fabric-requirements"></a>Service Fabric krav

Om du använder Service Fabric kontrollerar du att följande villkor är uppfyllda:
-   Service Fabric [hållbarhetsnivå](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) är Silver eller Guld, och inte brons.
-   Tillägget Service Fabric på modelldefinitionen för skalningsuppsättning måste ha TypeHandlerVersion 1.1 eller högre.
-   Hållbarhetsnivån bör vara densamma vid tillägget Service Fabric cluster och Service Fabric på modelldefinitionen för skalningsuppsättning.

Kontrollera att hållbarhetsinställningarna inte är inkompatibla i tillägget Service Fabric- och Service Fabric-tillägg, eftersom en obalans resulterar i uppgraderingsfel. Hållbarhetsnivåer kan ändras enligt riktlinjerna som beskrivs på [den här sidan](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).


## <a name="automatic-os-image-upgrade-for-custom-images-preview"></a>Automatisk uppgradering av os-avbildningar för anpassade bilder (förhandsgranskning)

> [!IMPORTANT]
> Automatisk os-avbildningsuppgradering för anpassade avbildningar finns för närvarande i Offentlig förhandsversion. En opt-in-procedur behövs för att använda den offentliga förhandsversionen som beskrivs nedan.
> Den här förhandsversionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Automatisk os-avbildningsuppgradering är tillgänglig i förhandsgranskningen för anpassade avbildningar som distribueras via [Det delade bildgalleriet](shared-image-galleries.md). Andra anpassade avbildningar stöds inte för automatiska os-avbildningsuppgraderingar.

För att aktivera förhandsversionen krävs en engångsanmälan för funktionen *AutomaticOSUpgradeWithGalleryImage* per prenumeration, enligt beskrivningen nedan.

### <a name="rest-api"></a>REST-API
I följande exempel beskrivs hur du aktiverar förhandsgranskningen för din prenumeration:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage/register?api-version=2015-12-01`
```

Funktionsregistrering kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticOSUpgradeWithGalleryImage?api-version=2015-12-01`
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräkningsresursprovidern.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) för att aktivera förhandsversionen för din prenumeration.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

Funktionsregistrering kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticOSUpgradeWithGalleryImage -ProviderNamespace Microsoft.Compute
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräkningsresursprovidern.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az-funktionsregistret](/cli/azure/feature#az-feature-register) för att aktivera förhandsgranskningen för din prenumeration.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

Funktionsregistrering kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticOSUpgradeWithGalleryImage
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräkningsresursprovidern.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

### <a name="additional-requirements-for-custom-images"></a>Ytterligare krav för anpassade bilder
- Opt-in-processen som beskrivs ovan behöver endast slutföras en gång per prenumeration. Efter opt-in slutförande, automatiska OS-uppgraderingar kan aktiveras för alla skalor som anges i den prenumerationen.
- Det delade bildgalleriet kan finnas i alla prenumerationer och behöver inte anmälas separat. Endast skalningsuppsättningsprenumerationen kräver funktionsanmälningen.
- Konfigurationsprocessen för automatisk uppgradering av OS-avbildningar är densamma för alla skalningsuppsättningar som beskrivs i [konfigurationsavsnittet](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) på den här sidan.
- Skalningsuppsättningar instanser som konfigurerats för automatiska OS-avbildningsuppgraderingar uppgraderas till den senaste versionen av avbildningsgalleriet för delade bilder när en ny version av avbildningen publiceras och [replikeras](shared-image-galleries.md#replication) till området för den skalningsuppsättningen. Om den nya avbildningen inte replikeras till den region där skalan distribueras, uppgraderas inte skalningsuppsättningsinstanserna till den senaste versionen. Med regional bildreplikering kan du styra distributionen av den nya avbildningen för skalningsuppsättningarna.
- Den nya bildversionen bör inte uteslutas från den senaste versionen för den galleribilden. Bildversioner som inte ingår i galleribildens senaste version distribueras inte till skalan som anges genom automatisk os-avbildningsuppgradering.

> [!NOTE]
>Det kan ta upp till 2 timmar för en skalningsuppsättning att få den första avbildningsutrullningen när skalningsuppsättningen har konfigurerats för automatiska OS-uppgraderingar. Detta är en engångsfördröjning per uppsättning skalor. Efterföljande bildrullningar tillämpas på skalningsuppsättningen utan den här fördröjningen.


## <a name="configure-automatic-os-image-upgrade"></a>Konfigurera automatisk uppgradering av OS-avbildning
Om du vill konfigurera automatisk uppgradering av OS-avbildningen kontrollerar du att egenskapen *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* är inställd på *true* i modelldefinitionen för skalningsuppsättning.

### <a name="rest-api"></a>REST-API
I följande exempel beskrivs hur du ställer in automatiska OS-uppgraderingar på en skalningsuppsättningsmodell:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
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
Använd [cmdleten Update-AzVmss](/powershell/module/az.compute/update-azvmss) för att konfigurera automatiska os-avbildningsuppgraderingar för skalningsuppsättningen. I följande exempel konfigureras automatiska uppgraderingar för skalningsuppsättningen *myScaleSet* i resursgruppen *myResourceGroup:*

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az vmss-uppdatering](/cli/azure/vmss#az-vmss-update) för att konfigurera automatiska OS-avbildningsuppgraderingar för din skalningsuppsättning. Använd Azure CLI 2.0.47 eller senare. I följande exempel konfigureras automatiska uppgraderingar för skalningsuppsättningen *myScaleSet* i resursgruppen *myResourceGroup:*

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Använda programhälsoavsökningar

Under en OS-uppgradering uppgraderas VM-instanser i en skalningsuppsättning en batch i taget. Uppgraderingen bör endast fortsätta om kundprogrammet är felfritt på de uppgraderade VM-instanserna. Vi rekommenderar att programmet tillhandahåller hälsosignaler till den skalningsuppsättning os-uppgraderingsmotorn. Som standard, under OS Uppgraderingar plattformen anser VM makttillstånd och tillägg etablering tillstånd för att avgöra om en VM-instans är felfri efter en uppgradering. Under OS-uppgraderingen av en VM-instans ersätts OS-disken på en VM-instans med en ny disk baserat på den senaste avbildningsversionen. När OS-uppgraderingen har slutförts körs de konfigurerade tilläggen på dessa virtuella datorer. Programmet anses vara felfritt endast när alla tillägg i instansen har etablerats.

En skalningsuppsättning kan eventuellt konfigureras med Programhälsoavsökningar för att ge plattformen korrekt information om programmets pågående tillstånd. Programhälsoavsökningar är anpassade belastningsutjämnareavsökningar som används som hälsosignal. Programmet som körs på en skalenlig vm-instans kan svara på externa HTTP- eller TCP-begäranden som anger om det är felfritt. Mer information om hur anpassade belastningsutjämnareavsökningar fungerar finns i [Förstå belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md). Programhälsoavsökningar stöds inte för service fabric-skalningsuppsättningar. Skalningsuppsättningar för icke-serviceinfrastruktur kräver antingen belastningsutjämna programhälsoavsökningar eller [programhälsotillägg](virtual-machine-scale-sets-health-extension.md).

Om skalningsuppsättningen är konfigurerad för att använda flera placeringsgrupper måste avsökningar med hjälp av en [standardbelastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) användas.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurera en anpassad avsökning av belastningsutjämnare som programhälsoavsökning på en skalningsuppsättning
Som bästa praxis kan du skapa en belastningsutjämnad avsökning uttryckligen för skalningsuppsättningshälsa. Samma slutpunkt för en befintlig HTTP-avsökning eller TCP-avsökning kan användas, men en hälsoavsökning kan kräva ett annat beteende än en traditionell belastningsutjämnadavsökning. En traditionell belastningsutjämnadavsökning kan till exempel returnera fel om belastningen på instansen är för hög, men det skulle inte vara lämpligt för att fastställa instansens hälsotillstånd under en automatisk OS-uppgradering. Konfigurera avsökningen så att den har en hög sonderingshastighet på mindre än två minuter.

Belastningsutjämnaravsökningen kan refereras i skalningsuppsättningens *nätverkProfil och* kan associeras med antingen en intern eller offentlig belastningsutjämnare enligt följande:

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
> När du använder Automatiska OS-uppgraderingar med Service Fabric, den nya OS-avbildningen rullas ut Uppdatera domän av update domän för att upprätthålla hög tillgänglighet för de tjänster som körs i Service Fabric. Om du vill använda automatiska OS-uppgraderingar i Service Fabric måste klustret vara konfigurerat för att använda silverhållbarhetsnivån eller högre. Mer information om hållbarhetsegenskaperna för Service Fabric-kluster finns i [den här dokumentationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Håll autentiseringsuppgifterna uppdaterade
Om din skalningsuppsättning använder autentiseringsuppgifter för att komma åt externa resurser, till exempel ett VM-tillägg som konfigurerats för att använda en SAS-token för lagringskonto, kontrollerar du att autentiseringsuppgifterna uppdateras. Om några autentiseringsuppgifter, inklusive certifikat och token, har upphört att gälla misslyckas uppgraderingen och den första batchen med virtuella datorer kommer att lämnas i ett misslyckat tillstånd.

De rekommenderade stegen för att återställa virtuella datorer och återaktivera automatisk os-uppgradering om det finns ett resursautentiseringsfel är:

* Återskapa token (eller andra autentiseringsuppgifter) som skickas till tillägget/tilläggen.
* Kontrollera att alla autentiseringsuppgifter som används inifrån den virtuella datorn för att prata med externa entiteter är uppdaterade.
* Uppdatera tillägg i skalningsuppsättningsmodellen med nya token.
* Distribuera den uppdaterade skalningsuppsättningen, som uppdaterar alla VM-instanser, inklusive de misslyckade.

## <a name="using-application-health-extension"></a>Använda tillägget Programhälsa
Tillägget Programhälsa distribueras i en skalningsuppsättningsinstans för virtuell dator och rapporterar om vm-hälso- och sjukvård inifrån skalningsuppsättningsinstansen. Du kan konfigurera tillägget så att det avsöker på en programslutpunkt och uppdaterar programmets status i den instansen. Den här instansstatusen kontrolleras av Azure för att avgöra om en instans är berättigad till uppgraderingsåtgärder.

Eftersom tillägget rapporterar hälsotillstånd inifrån en virtuell dator kan tillägget användas i situationer där externa avsökningar som Application Health Probes (som använder anpassade Azure Load [Balancer-avsökningar)](../load-balancer/load-balancer-custom-probe-overview.md)inte kan användas.

Det finns flera sätt att distribuera tillägget Programhälsa till skalningsuppsättningarna som beskrivs i exemplen i den [här artikeln](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Hämta historiken för automatiska os-avbildningsuppgraderingar
Du kan kontrollera historiken för den senaste OS-uppgraderingen som utförts på din skalningsuppsättning med Azure PowerShell, Azure CLI 2.0 eller REST-API:erna. Du kan få historik för de senaste fem OS-uppgraderingsförsöken under de senaste två månaderna.

### <a name="rest-api"></a>REST-API
I följande exempel används [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) för att kontrollera status för skalningsuppsättningen *myScaleSet* i resursgruppen *myResourceGroup:*

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

GET-anropet returnerar egenskaper som liknar följande exempelutdata:

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
Använd [Cmdlet Get-AzVmss](/powershell/module/az.compute/get-azvmss) för att kontrollera OS-uppgraderingshistoriken för din skalningsuppsättning. I följande exempel beskrivs hur du granskar os-uppgraderingsstatusen för en skalningsuppsättning med namnet *myScaleSet* i resursgruppen *myResourceGroup:*

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) för att kontrollera os-uppgraderingshistoriken för din skalningsuppsättning. Använd Azure CLI 2.0.47 eller senare. I följande exempel beskrivs hur du granskar os-uppgraderingsstatusen för en skalningsuppsättning med namnet *myScaleSet* i resursgruppen *myResourceGroup:*

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Hur får man den senaste versionen av en plattform OS-avbildning?

Du kan hämta tillgängliga avbildningsversioner för automatiska SKU:er som stöds av OS-uppgradering med hjälp av exemplen nedan:

### <a name="rest-api"></a>REST-API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Utlösa uppgraderingar av os-avbildning manuellt
Med automatisk os-avbildningsuppgradering aktiverad på din skalningsuppsättning behöver du inte utlösa avbildningsuppdateringar manuellt i skalningsuppsättningen. Os uppgradering orchestrator kommer automatiskt att tillämpa den senaste tillgängliga bildversionen på din skala uppsättning instanser utan manuella åtgärder.

För specifika fall där du inte vill vänta på att orchestrator ska tillämpa den senaste avbildningen kan du utlösa en os-avbildningsuppgradering manuellt med hjälp av exemplen nedan.

> [!NOTE]
> Manuell utlösare av os-avbildningsuppgraderingar ger inte automatiska återställningsfunktioner. Om en instans inte återställer sin hälsotillstånd efter en uppgraderingsåtgärd kan dess tidigare OS-disk inte återställas.

### <a name="rest-api"></a>REST-API
Använd API-anropet för uppgradering av [Start OS](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) för att starta en rullande uppgradering för att flytta alla uppsättning instanser för virtuell datorskala till den senast tillgängliga versionen av avbildningsoperativsystemet. Instanser som redan kör den senaste tillgängliga OS-versionen påverkas inte. I följande exempel beskrivs hur du kan starta en rullande OS-uppgradering på en skalningsuppsättning med namnet *myScaleSet* i resursgruppen *myResourceGroup:*

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) för att kontrollera os-uppgraderingshistoriken för din skalningsuppsättning. I följande exempel beskrivs hur du kan starta en rullande OS-uppgradering på en skalningsuppsättning med namnet *myScaleSet* i resursgruppen *myResourceGroup:*

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az vmss rullande uppgradering start](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) för att kontrollera OS uppgraderingshistorik för din skala uppsättning. Använd Azure CLI 2.0.47 eller senare. I följande exempel beskrivs hur du kan starta en rullande OS-uppgradering på en skalningsuppsättning med namnet *myScaleSet* i resursgruppen *myResourceGroup:*

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Distribuera med en mall

Du kan använda mallar för att distribuera en skalningsuppsättning med automatiska OS-uppgraderingar för bilder som stöds, till exempel [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Nästa steg
Mer exempel på hur du använder automatiska OS-uppgraderingar med skalningsuppsättningar finns i [GitHub-repoen](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
