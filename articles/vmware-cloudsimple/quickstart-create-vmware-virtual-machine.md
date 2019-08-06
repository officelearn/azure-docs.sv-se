---
title: Azure VMware-lösning av CloudSimple snabb start – förbruka virtuella VMware-datorer på Azure
description: I den här snabb starten får du lära dig hur du konfigurerar och använder virtuella VMware-datorer från Azure Portal med hjälp av Azure VMware-lösningen av CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dd4faf6df54d478654c59ffc18bf8c5873d576b9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816649"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Snabbstart: Använda virtuella VMware-datorer på Azure

Om du vill skapa en virtuell dator i Azure Portal kan du använda mallarna för virtuella datorer som är tillgängliga i ditt privata moln vCenter. En vCenter-administratör kan skapa ytterligare mallar i det privata molnet.

## <a name="create-a-vm-template"></a>Skapa en mall för virtuell dator

Börja med att skapa en virtuell dator i ditt privata moln med vCenter-ANVÄNDARGRÄNSSNITTET. Om du vill skapa en mall följer du anvisningarna i artikeln VMware [klona en virtuell dator till en mall i vSphere-](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)webbklienten. Lagra VM-mallen i ditt privata moln vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Skapa en virtuell dator i Azure Portal

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple Virtual Machines**.

3. Välj **Lägg till**.

    ![Välj Lägg till](media/create-cloudsimple-virtual-machine.png)

4. Ange följande information om den virtuella datorn och välj **sedan Nästa: Storlek**.

    ![Skapa CloudSimple virtuella datorer – grunder](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Fält | Beskrivning |
    | ------------ | ------------- |
    | **Prenumeration** | Den Azure-prenumeration som är associerad med ditt privata moln.  |
    | **Resursgrupp** | Resurs gruppen som den virtuella datorn ska tilldelas till. Du kan välja en befintlig grupp eller skapa en ny. |
    | **Namn** | Ett namn för att identifiera den virtuella datorn.  |
    | **Location** | Den Azure-region där den virtuella datorn finns.  |
    | **Privat moln** | Det CloudSimple-privata moln som du vill skapa den virtuella datorn i. |
    | **ResourcePool** | En mappad resurspool för den virtuella datorn. Välj bland tillgängliga resurspooler. |
    | **vSphere-mall** | VSphere-mallen för den virtuella datorn.  |
    | **Användarnamn** | Användar namnet för den virtuella dator administratören (för Windows-mallar).|
    | **Lösenord** |  Lösen ordet för VM-administratören (för Windows-mallar). |
    | **Bekräfta lösenord** | Lösen ordet som angavs i föregående fält. |

5. Välj antalet kärnor och minnes kapaciteten för den virtuella datorn. Välj **exponera för gäst** operativ system om du vill exponera fullständig CPU-virtualisering till gäst operativ systemet. Program som kräver virtualisering av maskin vara kan köras på virtuella datorer utan binär översättning eller paravirtualiseringsegenskaperna. Mer information finns i VMware-artikeln <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">exponerar VMware-maskinvarustödd virtualisering</a>. När du är klar väljer **du nästa: Konfigurationer**.

    ![Skapa CloudSimple virtuell dator-storlek](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurera nätverks gränssnitt och diskar enligt beskrivningen i följande tabeller och välj sedan **Granska + skapa**.

    ![Skapa CloudSimple-konfigurationer för virtuella datorer](media/create-cloudsimple-virtual-machine-configurations.png)

    För nätverks gränssnitt väljer du **Lägg till nätverks gränssnitt** och konfigurerar sedan följande inställningar:
    
    | Inställning | Beskrivning |
    | ------------ | ------------- |
    | **Namn** | Ange ett namn för att identifiera gränssnittet.  |
    | **Nätverk** | Välj i listan över konfigurerade distribuerade Port grupper i ditt privata moln vSphere.  |
    | **Kort** | Välj ett vSphere-kort i listan över tillgängliga typer som har kon figurer ATS för den virtuella datorn. Mer information finns i VMware-artikeln <a href="https://kb.vmware.com/s/article/1001805" target="_blank">välja ett nätverkskort för den virtuella datorn</a>. |
    | **Starta vid start** | Välj om du vill aktivera NÄTVERKSKORTets maskin vara när den virtuella datorn startas. Standardvärdet är **Enable**. |

    För diskar väljer du **Lägg till disk** och konfigurerar sedan följande inställningar:

    | Inställning | Beskrivning |
    | ------------ | ------------- |
    | **Namn** | Ange ett namn för att identifiera disken.  |
    | **Storlek** | Välj en av de tillgängliga storlekarna.  |
    | **SCSI-styrenhet** | Välj en SCSI-styrenhet för disken.  |
    | **Offline** | Läget anger hur disken deltar i ögonblicks bilder. Välj något av följande alternativ: <br> **Oberoende beständig**: Alla ändringar som skrivs till disken skrivs permanent.<br> **Oberoende icke-permanent**: Ändringar som skrivs till disken ignoreras när du stänger av eller återställer den virtuella datorn. Oberoende icke-beständigt läge gör att du alltid kan starta om den virtuella datorn i samma tillstånd. Mer information finns i VMware- <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentationen</a>.

7. När valideringen är färdig granskar du inställningarna och väljer **skapa**. Om du vill göra ändringar väljer du flikarna överst eller väljer **föregående: Konfigurationer**.

    ![Skapa CloudSimple virtuell dator – granska + skapa](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Nästa steg

* [Visa lista med virtuella CloudSimple-datorer](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Hantera virtuella CloudSimple-datorer från Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
