---
title: Azure VMware-lösning av CloudSimple Quickstart - förbrukar virtuella VMware-datorer på Azure
description: Lär dig hur du konfigurerar och använder virtuella VMware-datorer från Azure-portalen med Azure VMware-lösning genom CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0bb8d7a5a1ea30b704b44c9337cd28043597ff7
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209533"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Snabbstart – använda virtuella VMware-datorer på Azure

Använd mallar för virtuella datorer som CloudSimple administratören har aktiverat för din prenumeration för att skapa en virtuell dator i Azure-portalen. Dessa mallar för virtuella datorer finns på VMware-infrastruktur.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Skapa CloudSimple VM på Azure måste du skapa en VM-mall

Skapa en virtuell dator i ditt privata moln från vCenter-Användargränssnittet. Om du vill skapa en mall, följer du anvisningarna i [klona en virtuell dator till en mall i vSphere-webbklienten](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store mallen för virtuella datorer på privat moln-vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Skapa en virtuell dator i Azure portal

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple virtuella datorer**.

3. Klicka på **Lägg till**.

    ![Skapa CloudSimple virtuell dator](media/create-cloudsimple-virtual-machine.png)

4. Ange grundläggande information klickar du på **nästa: storlek**.

    ![Skapa CloudSimple VM - grunderna](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Fält | Beskrivning |
    | ------------ | ------------- |
    | Prenumeration | Azure-prenumeration som är associerade med ditt privata moln.  |
    | Resursgrupp | Resursgrupp som den virtuella datorn kommer att tilldelas. Du kan välja en befintlig grupp eller skapa en ny. |
    | Namn | Namn som identifierar den virtuella datorn.  |
    | Location | Azure-region som är värd för den här virtuella datorn.  |
    | Private Cloud | CloudSimple privata moln där du vill skapa den virtuella datorn. |
    | Resurspool | Mappa resurspool för den virtuella datorn. Välj de tillgängliga resurspoolerna. |
    | vSphere Template | vSphere-mallen för den virtuella datorn.  |
    | Användarnamn | Användarnamn för VM-administratör (för Windows-mallar)|
    | Lösenord |  Lösenordet för VM-administratör (för Windows-mallar). |
    | Bekräfta lösenordet | Bekräfta lösenordet |

5. Välj antal kärnor och minneskapacitet för den virtuella datorn och klicka på **nästa: konfigurationer**. Markera kryssrutan om du vill visa fullständig CPU-virtualisering till gästoperativsystemet. Program som kräver virtualisering av maskinvara kan köras på virtuella datorer utan binära translation eller paravirtualization. Mer information finns i artikeln VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">exponera VMware Assisted maskinvaruvirtualisering</a>.

    ![Skapa CloudSimple VM - storlek](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurera nätverksgränssnitt och diskar som beskrivs i följande tabeller och klicka på **granska + skapa**.

    ![Skapa CloudSimple VM - konfigurationer](media/create-cloudsimple-virtual-machine-configurations.png)

    För nätverksgränssnitt, klickar du på **Lägg till nätverksgränssnittet** och konfigurera följande inställningar.
    
    | Kontroll | Beskrivning |
    | ------------ | ------------- |
    | Namn | Ange ett namn som identifierar gränssnittet.  |
    | Nätverk | Välj i listan över konfigurerade distribuerade portgrupp i ditt privata moln vSphere.  |
    | Nätverkskort | Välj en vSphere-adapter i listan över tillgängliga typer som konfigurerats för den virtuella datorn. Mer information finns i knowledge base-artikeln VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">välja ett nätverkskort för den virtuella datorn</a>. |
    | Starta vid start | Välj om du vill aktivera NIC-maskinvaran när den virtuella datorn har startats. Standardvärdet är **aktivera**. |

    För diskar, klickar du på **Lägg till disk** och konfigurera följande inställningar.

    | Objekt | Beskrivning | 
    | ------------ | ------------- | 
    | Namn | Ange ett namn för att identifiera disken.  | 
    | Storlek | Välj en av de tillgängliga storlekarna.  | 
    | SCSI-styrenhet | Välj en SCSI-styrenhet för disken.  |
    | Läge | Anger hur disken deltar i ögonblicksbilder. Välj något av följande alternativ: <br> -Oberoende beständiga: Alla data som skrivs till disken skrivs permanent.<br> -Oberoende icke-beständiga: Ändringar skrivs till disken tas bort när du stängs av eller återställer den virtuella datorn.  Oberoende icke-beständiga läge kan du alltid starta om den virtuella datorn i samma tillstånd. Mer information finns i den <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentationen om VMware</a>.

7. När verifieringen är klar granskar du inställningarna och klicka på **skapa**. Om du vill göra några ändringar, klickar på flikarna längst upp.

    ![Skapa CloudSimple virtuell dator – granska](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Nästa steg

* [Visa en lista över CloudSimple virtuella datorer](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Hantera CloudSimple virtuell dator från Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
