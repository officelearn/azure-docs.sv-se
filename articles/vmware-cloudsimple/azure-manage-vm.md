---
title: Azure VMware-lösning från CloudSimple – Hantera privata virtuella moln virtuella datorer i Azure
description: Beskriver hur du hanterar virtuella cloudsimple-privata moln virtuella datorer i Azure-portalen, inklusive att lägga till diskar, ändra vm-kapacitet och lägga till nätverksgränssnitt
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015005"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Hantera dina virtuella CloudSimple-virtuella molndatorer i Azure

Om du vill hantera de virtuella datorer som du [har skapat för ditt CloudSimple Private Cloud](azure-create-vm.md)loggar du till [Azure-portalen](https://portal.azure.com). Sök efter och välj det virtuella (sök under **Alla tjänster** eller **virtuella datorer** på sidomenyn).

## <a name="control-virtual-machine-operation"></a>Styra åtgärden för virtuella datorer

Följande kontroller är tillgängliga på sidan **Översikt** för den valda virtuella datorn.

| Kontroll | Beskrivning |
| ------------ | ------------- |
| Anslut | Anslut till den angivna virtuella datorn.  |
| Start | Starta den angivna virtuella datorn.  |
| Starta om | Stäng av och starta sedan den angivna virtuella datorn.  |
| Stoppa | Stäng av den specifika virtuella datorn.  |
| Capture | Ta en bild av den angivna virtuella datorn så att den kan användas som en avbildning för att skapa andra virtuella datorer. Se [Skapa en hanterad avbildning av en allmän virtuell dator i Azure](../virtual-machines/windows/classic/capture-image.md).   |
| Flytta | Flytta till den angivna virtuella datorn.  |
| Ta bort | Ta bort den angivna virtuella datorn.  |
| Uppdatera | Uppdatera data i displayen.  |

### <a name="view-performance-information"></a>Visa prestandainformation

Diagrammen i det nedre området på sidan **Översikt** presenterar prestandadata för det valda intervallet (sista timmen till senaste 30 dagarna, standard är sista timmen). I varje diagram kan du visa de numeriska värdena när som helst inom intervallet genom att flytta markören fram och tillbaka över diagrammet.

Följande diagram visas.

| Objekt | Beskrivning |
| ------------ | ------------- |
| CPU (genomsnitt) | Genomsnittlig CPU-användning i procent över det valda intervallet.   |
| Nätverk | Trafik in och ut ur nätverket (MB) under det valda intervallet.  |
| Diskbyte | Totalt data som lästs från disk och skrivits till disk (MB) under det valda intervallet.  |
| Diskåtgärder | Genomsnittlig hastighet för diskåtgärder (åtgärder/sekund) över det valda intervallet. |

## <a name="manage-vm-disks"></a>Hantera VM-diskar

Om du vill lägga till en VM-disk öppnar du sidan **Diskar** för den valda virtuella datorn. Om du vill lägga till en disk klickar du på **Lägg till disk**. Konfigurera var och en av följande inställningar genom att ange eller välja ett infogat alternativ. Klicka på **Spara**.

   | Objekt | Beskrivning |
   | ------------ | ------------- |
   | Namn | Ange ett namn för att identifiera disken.  |
   | Storlek | Välj en av de tillgängliga storlekarna.  |
   | SCSI-styrenhet | Välj en SCSI-styrenhet. De tillgängliga styrenheterna varierar för de olika operativsystem som stöds.  |
   | Läge | Bestämmer hur disken deltar i ögonblicksbilder. Välj ett av följande alternativ: <br> - Oberoende beständig: Alla data som skrivs till disken skrivs permanent.<br> - Oberoende, icke-beständig: Ändringar som skrivs till disken ignoreras när du stänger av eller återställer den virtuella datorn.  I det här läget kan du alltid starta om den virtuella datorn i samma tillstånd. Mer information finns i [VMware-dokumentationen](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Om du vill ta bort en disk markerar du den och klickar på **Ta bort**.

## <a name="change-the-capacity-of-the-vm"></a>Ändra kapaciteten för den virtuella datorn

Om du vill ändra kapaciteten för den virtuella datorn öppnar du sidan **Storlek** för den valda virtuella datorn. Ange något av följande och klicka på **Spara**.

| Objekt | Beskrivning |
| ------------ | ------------- |
| Antal kärnor | Antal kärnor som tilldelats den virtuella datorn.  |
| Maskinvaruvirtualisering | Markera kryssrutan om du vill visa maskinvaruvirtualiseringen för gästoperativsystemet. Se VMware artikeln [Exponera VMware Hardware Assisted Virtualization](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Minnesstorlek | Välj hur mycket minne som ska allokeras till den virtuella datorn.  

## <a name="manage-network-interfaces"></a>Hantera nätverksgränssnitt

Om du vill lägga till ett gränssnitt klickar du på **Lägg till nätverksgränssnitt**. Konfigurera var och en av följande inställningar genom att ange eller markera ett infogat alternativ. Klicka på **Spara**.

   | Kontroll | Beskrivning |
   | ------------ | ------------- |
   | Namn | Ange ett namn för att identifiera gränssnittet.  |
   | Nätverk | Välj från listan över konfigurerade nätverk i ditt privata moln vSphere.  |
   | Adapter | Välj en vSphere-adapter i listan över tillgängliga typer som konfigurerats för den virtuella datorn. Mer information finns i VMware-kunskapsbasartikeln [Välja ett nätverkskort för den virtuella datorn](https://kb.vmware.com/s/article/1001805). |
   | Slå på vid start | Välj om nätverkskortets maskinvara ska aktiveras när den virtuella datorn startas. Standard är **Aktivera**. |

Om du vill ta bort ett nätverksgränssnitt markerar du det och klickar på **Ta bort**.
