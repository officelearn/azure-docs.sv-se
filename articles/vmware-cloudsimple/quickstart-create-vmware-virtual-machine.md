---
title: Azure VMware-lösning av CloudSimple Snabbstart – använda virtuella VMware-datorer på Azure
description: I den här snabbstarten lär du kan konfigurera och använda virtuella VMware-datorer från Azure-portalen med hjälp av Azure VMware-lösning genom CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b430efbc931d72de4b095a7eac4c1e7ca496b1b9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393500"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Snabbstart: Använda virtuella VMware-datorer på Azure

Du kan använda mallar för virtuella datorer som är tillgängliga på privata moln-vCenter om du vill skapa en virtuell dator i Azure-portalen. En vCenter-administratören kan skapa ytterligare mallar på det privata molnet.

## <a name="create-a-vm-template"></a>Skapa en mall

Skapa först en virtuell dator i ditt privata moln med hjälp av Användargränssnittet för vCenter. Om du vill skapa en mall, följer du anvisningarna i artikeln VMware [klona en virtuell dator till en mall i vSphere-webbklienten](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store mallen för virtuella datorer på privat moln-vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Skapa en virtuell dator i Azure portal

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple virtuella datorer**.

3. Välj **Lägg till**.

    ![Välj Lägg till](media/create-cloudsimple-virtual-machine.png)

4. Ange följande information om den virtuella datorn och välj sedan **nästa: Storlek**.

    ![Skapa CloudSimple VM - grunderna](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Fält | Beskrivning |
    | ------------ | ------------- |
    | **Prenumeration** | Azure-prenumerationen som är associerade med ditt privata moln.  |
    | **Resursgrupp** | Den resursgrupp som den virtuella datorn kommer att tilldelas. Du kan välja en befintlig grupp eller skapa en ny. |
    | **Namn** | Ett namn som identifierar den virtuella datorn.  |
    | **Plats** | Azure-regionen som är värd för den virtuella datorn.  |
    | **Privat moln** | Det privata molnet på CloudSimple som du vill skapa den virtuella datorn. |
    | **ResourcePool** | En mappad resurspool för den virtuella datorn. Välj de tillgängliga resurspoolerna. |
    | **vSphere Template** | VSphere-mallen för den virtuella datorn.  |
    | **Användarnamn** | Användarnamn för VM-administratör (för Windows-mallar).|
    | **Lösenord** |  Lösenordet för VM-administratör (för Windows-mallar). |
    | **Bekräfta lösenord** | Lösenordet som angavs i föregående fält. |

5. Välj antal kärnor och minneskapaciteten för den virtuella datorn. Välj **exponerar för gäst-OS** om du vill visa en fullständig CPU-virtualisering till gästoperativsystemet. Program som kräver virtualisering av maskinvara kan köras på virtuella datorer utan binära translation eller paravirtualization. Mer information finns i artikeln VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">exponera VMware Assisted maskinvaruvirtualisering</a>. När du är klar väljer du **nästa: Konfigurationer**.

    ![Skapa CloudSimple VM - storlek](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurera nätverksgränssnitt och diskar som beskrivs i följande tabeller och välj sedan **granska + skapa**.

    ![Skapa CloudSimple VM - konfigurationer](media/create-cloudsimple-virtual-machine-configurations.png)

    Nätverksgränssnitt, Välj **Lägg till nätverksgränssnittet** och konfigurera följande inställningar:
    
    | Inställning | Beskrivning |
    | ------------ | ------------- |
    | **Namn** | Ange ett namn som identifierar gränssnittet.  |
    | **Nätverk** | Välj i listan över konfigurerade distribuerade portgrupper i ditt privata moln vSphere.  |
    | **Adapter** | Välj en vSphere-styrenhet i listan över tillgängliga typer som konfigurerats för den virtuella datorn. Mer information finns i artikeln VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">välja ett nätverkskort för den virtuella datorn</a>. |
    | **Starta vid start** | Välj om du vill aktivera NIC-maskinvaran när den virtuella datorn har startats. Standardvärdet är **aktivera**. |

    Diskar, Välj **Lägg till disk** och konfigurera följande inställningar:

    | Inställning | Beskrivning |
    | ------------ | ------------- |
    | **Namn** | Ange ett namn för att identifiera disken.  |
    | **Storlek** | Välj en av de tillgängliga storlekarna.  |
    | **SCSI-styrenhet** | Välj en SCSI-styrenhet för disken.  |
    | **läge** | Läget som anger hur disken deltar i ögonblicksbilder. Välj något av följande alternativ: <br> **Oberoende beständiga**: Permanent skrivs alla ändringar som skrivs till disken.<br> **Oberoende icke-beständiga**: Ändringar skrivs till disken tas bort när du stängs av eller återställer den virtuella datorn. Oberoende icke-beständiga läge kan du alltid starta om den virtuella datorn i samma tillstånd. Mer information finns i den <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentationen om VMware</a>.

7. När verifieringen är klar granskar du inställningarna och välj **skapa**. Välj för att göra ändringar på flikarna överst eller välj **tidigare: Konfigurationer**.

    ![Skapa CloudSimple virtuell dator – granska + skapa](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Nästa steg

* [Visa en lista över CloudSimple virtuella datorer](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Hantera CloudSimple virtuella datorer från Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
