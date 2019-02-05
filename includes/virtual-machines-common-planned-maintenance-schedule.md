---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: c7fe0d6f8e03501cca7a8b98f95286b6a21c0476
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735727"
---
## <a name="multi-and-single-instance-vms"></a>Enskild instans virtuella datorer och Multi
Många kunder som kör på Azure antal den kritiska kan schemalägga om sina virtuella datorer genomgår planerat underhåll på grund av avbrott--cirka 15 minuter--som inträffar under underhåll. Du kan använda tillgänglighetsuppsättningar för att kontrollera när etablerade virtuella datorer får planerat underhåll.

Det finns två möjliga konfigurationerna för virtuella datorer som körs på Azure. Virtuella datorer är konfigurerade som Multi-instans eller en instans. Om virtuella datorer finns i en tillgänglighetsuppsättning, är de konfigurerade som flera instanser. Observera, även enkel virtuella datorer kan distribueras i en tillgänglighetsuppsättning, så att de behandlas som flera instanser. Om virtuella datorer inte är i en tillgänglighetsuppsättning är de konfigurerade som en instans.  Mer information om tillgänglighetsuppsättningar finns [hantera tillgängligheten av dina Windows-datorer](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [hantera tillgängligheten av dina Linux-datorer](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Planerat underhållsuppdateringar till virtuella datorer en instans och flera instanser sker separat. Du kan styra när de virtuella datorerna får det planerade underhållet genom att omkonfigurera dina virtuella datorer är en instans (om de finns flera instanser) eller för att vara flera instanser (om de är en instans). Se [planerat underhåll för virtuella Azure Linux-datorer](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [planerat underhåll för Windows Azure virtuella datorer](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) mer information om planerat underhåll för virtuella Azure-datorer.

## <a name="for-multi-instance-configuration"></a>För multi-Instanskonfigurationen
Du kan välja den tid som planerat underhåll påverkar dina virtuella datorer som distribueras i en Tillgänglighetsuppsättning konfiguration genom att ta bort dessa virtuella datorer från tillgänglighetsuppsättningar.

1. Ett e-postmeddelande skickas till dig sju kalenderdagar före det planerade underhållet dina virtuella datorer i en Multi-instanskonfiguration. Prenumerations-ID: N och namnen på de berörda virtuella datorer som flera instanser ingår i brödtexten i e-postmeddelandet.
2. Du kan välja den tid som dina instanser uppdateras genom att ta bort virtuella datorer med flera instanser i regionen från sina tillgänglighetsuppsättning under de sju dagarna. Den här ändringen i konfigurationen orsakar en omstart, som den virtuella datorn flyttas från en fysisk värd, avsedda för underhåll, till en annan fysisk värd som inte är avsedda för underhåll.
3. Du kan ta bort den virtuella datorn från dess tillgänglighetsuppsättning i Azure-portalen.

   1. Välj den virtuella datorn att ta bort från Tillgänglighetsuppsättningen i portalen.  

   2. Under **inställningar**, klickar du på **tillgänglighetsuppsättning**.

      ![Val av Tillgänglighetsuppsättning](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. I tillgängligheten ställer du in går du till menyn, Välj ”inte en del av en tillgänglighetsuppsättning”.

      ![Ta bort från uppsättningen](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. Överst på sidan klickar du på **spara**. Klicka på **Ja** att bekräfta att den här åtgärden startar om den virtuella datorn.

   >[!TIP]
   >Du kan konfigurera om den virtuella datorn till flera instanser senare genom att välja en av de listade tillgänglighetsuppsättningarna.

4. Virtuella datorer tas bort från tillgänglighetsuppsättningar flyttas till Single-Instance-värdar och uppdateras inte under planerat underhåll för att ange konfigurationer för tillgänglighet.
5. När uppdateringen till tillgänglighet ange virtuella datorer har slutförts (enligt det schema som beskrivs i det ursprungliga e-postmeddelandet), bör du lägga till de virtuella datorerna tillbaka till sina tillgänglighetsuppsättningar. Bli en del av en tillgänglighetsuppsättning konfigurerar om de virtuella datorerna som flera instanser och resulterar i en omstart. Vanligtvis när alla Multi-instansuppdateringar har slutförts i hela Azure-miljön, följer enkelinstanser underhåll.

Ta bort en virtuell dator från en tillgänglighetsuppsättning kan även uppnås med hjälp av Azure PowerShell:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>För enskild instans-konfiguration
Du kan välja den tid som planerat underhåll påverkar du virtuella datorer i en enda instans-konfiguration genom att lägga till dessa virtuella datorer i tillgänglighetsuppsättningar.

Steg för steg

1. Ett e-postmeddelande skickas till dig sju kalenderdagar före planerat underhåll för virtuella datorer i en enda instans-konfiguration. Prenumerations-ID: N och alla berörda Single-Instance virtuella datorer ingår i brödtexten i e-postmeddelandet.
2. Du kan välja den tid som din instans startas om genom att lägga till en instans virtuella datorer i en tillgänglighetsuppsättning i samma regionen under de sju dagarna. Den här ändringen i konfigurationen orsakar en omstart, som den virtuella datorn flyttas från en fysisk värd, avsedda för underhåll, till en annan fysisk värd som inte är avsedda för underhåll.
3. Följ instruktionerna här för att lägga till befintliga virtuella datorer i tillgänglighetsuppsättningar med hjälp av Azure-portalen och Azure PowerShell. (Se Azure PowerShell-exemplet som följer dessa steg.)
4. När dessa virtuella datorer konfigureras som flera instanser, undantas de från det planerade underhållet för enskild instans virtuella datorer.
5. När en instans VM-uppdateringen är klar (enligt schema i det ursprungliga e-postmeddelandet), kan du returnera de virtuella datorerna till en instans genom att ta bort de virtuella datorerna från sina tillgänglighetsuppsättningar.

Att lägga till en virtuell dator i en tillgänglighetsuppsättning också kan uppnås med hjälp av Azure PowerShell:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
