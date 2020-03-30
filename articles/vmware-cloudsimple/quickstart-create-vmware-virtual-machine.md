---
title: 'Snabbstart: Förbruka virtuella virtuella datorer på Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Lär dig hur du konfigurerar och använder virtuella VMware-datorer från Azure-portalen med Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019561"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Snabbstart – förbruka virtuella virtuella datorer på Azure

Om du vill skapa en virtuell dator i Azure-portalen använder du mallar för virtuella datorer som din CloudSimple-administratör har aktiverat för din prenumeration. Vm-mallarna finns i VMware-infrastrukturen.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>CloudSimple VM-skapande på Azure kräver en VM-mall

Skapa en virtuell dator på ditt privata moln från användargränssnittet för vCenter. Om du vill skapa en mall följer du instruktionerna i [Klona en virtuell dator till en mall i vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Lagra VM-mallen på ditt privata moln vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Skapa en virtuell dator på Azure Portal

1. Välj **Alla tjänster**.

2. Sök efter **Virtuella CloudSimple-datorer**.

3. Klicka på **Lägg till**.

    ![Skapa virtual machine cloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Ange grundläggande information och klicka på **Nästa:Storlek**.

    ![Skapa Virtual Machine CloudSimple – grunderna](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Field | Beskrivning |
    | ------------ | ------------- |
    | Prenumeration | Azure-prenumeration som är associerad med ditt privata moln.  |
    | Resursgrupp | Resursgrupp som den virtuella datorn ska tilldelas. Du kan välja en befintlig grupp eller skapa en ny. |
    | Namn | Namn för att identifiera den virtuella datorn.  |
    | Location | Azure-region där den här virtuella datorn är värd.  |
    | Privat moln | CloudSimple Private Cloud där du vill skapa den virtuella datorn. |
    | Resurspool | Mappad resurspool för den virtuella datorn. Välj bland tillgängliga resurspooler. |
    | mall för vSphere | vSphere-mallen för den virtuella datorn.  |
    | Användarnamn | Användarnamn för vm-administratören (för Windows-mallar).|
    | lösenord |  Lösenord för vm-administratören (för Windows-mallar). |
    | Bekräfta lösenord | Bekräfta lösenordet. |

5. Välj antalet kärnor och minneskapacitet för den virtuella datorn och klicka på **Nästa:Konfigurationer**. Markera kryssrutan om du vill exponera fullständig CPU-virtualisering för gästoperativsystemet. Program som kräver maskinvaruvirtualisering kan köras på virtuella datorer utan binär översättning eller paravirtualisering. Mer information finns i VMware-artikeln <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Expose VMware Hardware Assisted Virtualization</a>.

    ![Skapa CloudSimple virtuell dator - storlek](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurera nätverksgränssnitt och diskar enligt beskrivningen i följande tabeller och klicka på **Granska + skapa**.

    ![Skapa Virtual Machine CloudSimple - konfigurationer](media/create-cloudsimple-virtual-machine-configurations.png)

    För nätverksgränssnitt klickar du på **Lägg till nätverksgränssnitt** och konfigurerar följande inställningar.

    | Kontroll | Beskrivning |
    | ------------ | ------------- |
    | Namn | Ange ett namn för att identifiera gränssnittet.  |
    | Nätverk | Välj från listan över konfigurerade distribuerade portgrupp i ditt privata moln vSphere.  |
    | Adapter | Välj en vSphere-adapter i listan över tillgängliga typer som konfigurerats för den virtuella datorn. Mer information finns i VMware-kunskapsbasartikeln <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Välja ett nätverkskort för den virtuella datorn</a>. |
    | Slå på vid start | Välj om nätverkskortets maskinvara ska aktiveras när den virtuella datorn startas. Standard är **Aktivera**. |

    För diskar klickar du på **Lägg till disk** och konfigurerar följande inställningar.

    | Objekt | Beskrivning |
    | ------------ | ------------- |
    | Namn | Ange ett namn för att identifiera disken.  |
    | Storlek | Välj en av de tillgängliga storlekarna.  |
    | SCSI-styrenhet | Välj en SCSI-styrenhet för disken.  |
    | Läge | Bestämmer hur disken deltar i ögonblicksbilder. Välj ett av följande alternativ: <br> - Oberoende beständig: Alla data som skrivs till disken skrivs permanent.<br> - Oberoende icke-beständiga: Ändringar som skrivs på disken ignoreras när du stänger av eller återställer den virtuella datorn.  Oberoende icke-beständigt läge kan du alltid starta om den virtuella datorn i samma tillstånd. Mer information finns i <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware-dokumentationen</a>.

7. När valideringen är klar granskar du inställningarna och klickar på **Skapa**. Om du vill göra några ändringar klickar du på flikarna högst upp eller klickar på.

    ![Skapa CloudSimple virtuell dator - recension](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Nästa steg

* [Visa lista över virtuella CloudSimple-datorer](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Hantera CloudSimple virtuell dator från Azure](azure-manage-vm.md)
