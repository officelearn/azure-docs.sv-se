---
title: "Uppgradera en skaluppsättning för virtuell dator i Azure | Microsoft Docs"
description: "Uppgradera en skaluppsättning för virtuell Azure-dator"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: gunegatybo
ms.openlocfilehash: fbdc9d40173a40f35eee60cadfdd258293509d53
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
---
# <a name="upgrade-a-virtual-machine-scale-set"></a>Uppgradera en virtuella datorns skaluppsättning
Den här artikeln beskriver hur du lanserar en OS-uppdatering till en virtuell dator i Azure-skala utan driftavbrott. I den här kontexten innebär en OS-uppdatering ändra version eller SKU av Operativsystemet eller ändra URI för en anpassad avbildning. Uppdaterar utan driftavbrott uppdatera virtuella datorer en i taget eller grupper (till exempel en feldomän i taget) i stället för på en gång. På så sätt, kan alla virtuella datorer som inte uppgraderas att köra.

För att undvika tvetydighet vi skilja fyra typer av OS-uppdateringar som du kanske vill utföra:

* Ändra version eller SKU av en plattformsavbildning. Till exempel ändra Ubuntu 14.04.2-LTS version från 14.04.201506100 till 14.04.201507060, eller ändra Ubuntu 15.10/senaste SKU till 16.04.0-LTS/latest. Det här scenariot beskrivs i den här artikeln.
* Ändra den URI som pekar på en ny version av en anpassad avbildning som du skapat (**egenskaper** > **virtualMachineProfile** > **storageProfile**  >  **osDisk** > **bild** > **uri**). Det här scenariot beskrivs i den här artikeln.
* Ändra bildreferens för skaluppsättning som har skapats med hjälp av Azure hanterade diskar.
* Korrigering av operativsystem från en virtuell dator (exempel på detta är hur du installerar en säkerhetskorrigering och kör Windows Update). Det här scenariot stöds, men som inte omfattas i den här artikeln.

Skalningsuppsättningar i virtuella datorer som distribueras som en del av en [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) kluster beskrivs inte här. Läs mer om Service Fabric-korrigering [korrigering Windows OS i Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-patch-orchestration-application)

Grundläggande sekvensen för att ändra OS-version/SKU av en plattformsavbildning eller URI för en anpassad avbildning ser ut som följer:

1. Hämta den virtuella datorn skala modellen.
2. Ändra version SKU, bildreferens eller URI-värdet i modellen.
3. Uppdatera modellen.
4. Gör en *manualUpgrade* anropa för de virtuella datorerna i skaluppsättning. Det här steget gäller endast om *upgradePolicy* är inställd på **manuell** i din skaluppsättningen. Om den är inställd på **automatisk**, alla virtuella datorer uppgraderas samtidigt, vilket orsakar driftstopp.

Med den här informationen i åtanke nu ska vi se hur du kan uppdatera versionen av en skala som angetts i PowerShell och med hjälp av REST API. De här exemplen täcker i fallet med en plattformsavbildning, men den här artikeln innehåller tillräckligt med information att anpassa den här processen för att en anpassad avbildning.

## <a name="powershell"></a>PowerShell
Det här exemplet uppdaterar en Windows virtuella skaluppsättningen (skapar till den nya versionen 4.0.20160229. När du har uppdaterat modellen har en uppdatering för en virtuell datorinstans i taget.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Om du uppdaterar URI för en anpassad avbildning istället för att ändra en plattform Avbildningsversion, ersätter du raden ”ange den nya versionen” med ett kommando som uppdaterar Källavbildningen URI. Till exempel om skaluppsättning skapades utan att använda Azure hanterade diskar, uppdateringen skulle se ut så här:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```

Om en anpassad avbildningsbaserad skaluppsättning har skapats med hjälp av Azure hanterade diskar, skulle bildreferensen uppdateras. Exempel:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.id = $newImageReference
```

## <a name="the-rest-api"></a>REST API
Här är några av Python-exempel som använder Azure REST API för att distribuera en uppdatering för OS-version. Både använda förenklade [azurerm](https://pypi.python.org/pypi/azurerm) bibliotek med Azure REST API-omslutning funktionerna för att utföra GET på skalan ange modell, följt av en PUT med en uppdaterad modell. De också titta på virtuella instanser vyer för att identifiera de virtuella datorerna genom att uppdatera domänen.

### <a name="vmssupgrade"></a>Vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools) är Python-skriptet som används för att distribuera en OS-uppgradering till en aktiv virtuell dator skala ange en uppdateringsdomän i taget.

![Vmssupgrade skript för att välja virtuella datorer eller en uppdateringsdomän](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Det här skriptet kan du välja specifika virtuella datorer för att uppdatera eller ange en uppdateringsdomän. Det stöder ändra en plattform Avbildningsversion eller ändra URI för en anpassad avbildning.

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard) är en generell redigerare för skalningsuppsättningar i virtuella datorer som visar virtuella datorn status som ett heatmap där en rad representerar en uppdateringsdomän. Bland annat kan du uppdatera modellen för en skalningsuppsättning med en ny version, en SKU eller en anpassad avbildning URI och välj sedan feldomäner att uppgradera. När du gör det uppgraderas alla virtuella datorer i domänen uppdateringen till den nya modellen. Alternativt kan du göra en löpande uppgradering baserat på batchstorlek önskat.  

Följande skärmbild visar en modell av en skala som angetts för Ubuntu 14.04-2LTS version 14.04.201507060. Många fler alternativ har lagts till det här verktyget eftersom den här skärmbilden har tagits.

![Vmsseditor modell för en skala som angetts för Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

När du klickar på **uppgradera** och sedan **Hämta detaljer**, virtuella datorer i UD 0 börja uppdatera.

![Vmsseditor visar uppdatering pågår](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

