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
ms.openlocfilehash: 58edadb553730b646f23f4981d6cbf1bdbfe76d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577727"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Snabbstart – använda virtuella VMware-datorer på Azure

Använd mallar för virtuella datorer som CloudSimple administratören har aktiverat för din prenumeration för att skapa en virtuell dator i Azure-portalen. Dessa mallar för virtuella datorer finns på VMware-infrastruktur.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Skapa CloudSimple VM på Azure måste du skapa en VM-mall

Skapa en virtuell dator i ditt privata moln från vCenter-Användargränssnittet. Om du vill skapa en mall, följer du anvisningarna i [klona en virtuell dator till en mall i vSphere-webbklienten](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store mallen för virtuella datorer på privat moln-vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Skapa en virtuell dator i Azure portal

1. På menyn till vänster klickar du på **+** eller **skapa en resurs**.

2. På menyn till vänster klickar du på **Compute**, och klicka sedan på **CloudSimple VM**.

3. Klicka på **Bekräfta** att verifiera att du vill skapa en ny virtuell dator.

4. Ange den grundläggande konfigurationen som beskrivs i följande tabell och klicka sedan på **nästa: Storlek**.

    | Fält | Beskrivning |
    | ------------ | ------------- |
    | Prenumeration | Azure-prenumeration som är associerade med din distribution av privat moln.  |
    | Resursgrupp | Grupp som den virtuella datorn kommer att tilldelas. Du kan välja en befintlig grupp eller skapa en ny. |
    | Namn | Namn som identifierar den virtuella datorn.  |
    | Location | Azure-region som är värd för den här virtuella datorn.  |
    | Resurspool | Fysiska resurser för den virtuella datorn. Välj de tillgängliga resurspoolerna. |
    | vSphere Template | Typ av operativsystem mallen för den virtuella datorn.  |
    | Användarnamn | Användarnamn för VM-administratör. |
    | Lösenord Bekräfta lösenord | Lösenordet för VM-administratör.  |

5. Välj antal kärnor och minneskapacitet för den virtuella datorn.

6. (Valfritt) Om du vill visa fullständig CPU-virtualisering till gästoperativsystemet väljer du den **exponerar för gäst-OS** kryssrutan.
Det här alternativet gör det möjligt för program som kräver virtualisering av maskinvara ska köras på virtuella datorer utan binära translation eller paravirtualization. Mer information finns i artikeln VMware [exponera VMware Assisted maskinvaruvirtualisering](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

7. Klicka på **Nästa: Konfiguration av**.

8. Konfigurera nätverksgränssnitt och diskar som beskrivs i följande tabeller.

    För nätverksgränssnitt, klickar du på **Lägg till nätverksgränssnittet** och konfigurera följande inställningar.

    | Kontroll | Beskrivning |
    | ------------ | ------------- |
    | Namn | Ange ett namn som identifierar gränssnittet.  |
    | Nätverk | Välj i listan över konfigurerade nätverk i ditt privata moln vSphere.  |
    | Nätverkskort | Välj en vSphere-adapter i listan över tillgängliga typer som konfigurerats för den virtuella datorn. Mer information finns i knowledge base-artikeln VMware [välja ett nätverkskort för den virtuella datorn](https://kb.vmware.com/s/article/1001805). |
    | Starta vid start | Välj om du vill aktivera NIC-maskinvaran när den virtuella datorn har startats. Standardvärdet är **aktivera**. |

    För diskar, klickar du på **Lägg till disk** och konfigurera följande inställningar.

    | Objekt | Beskrivning |
    | ------------ | ------------- |
    | Namn | Ange ett namn för att identifiera disken.  |
    | Storlek | Välj en av de tillgängliga storlekarna.  |
    | SCSI-styrenhet | Välj en SCSI-styrenhet. Tillgängliga styrenheterna variera för olika operativsystem som stöds.  |
    | Läge | Anger hur disken deltar i ögonblicksbilder. Välj något av följande alternativ: <br> -Oberoende beständiga: Alla data som skrivs till disken skrivs permanent.<br> -Oberoende icke-beständiga: Ändringar skrivs till disken tas bort när du stängs av eller återställer den virtuella datorn.  Oberoende icke-beständiga läge kan du alltid starta om den virtuella datorn i samma tillstånd. Mer information finns i den [VMware-dokumentationen.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)

9. Granska inställningarna. Klicka på flikarna längst upp för att göra några ändringar.

10. Klicka på **skapa** att spara inställningarna och skapa den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Visa en lista över CloudSimple virtuella datorer](https://docs.azure.cloudsimple.com/azurelistvms/)
* [Hantera CloudSimple virtuell dator från Azure](https://docs.azure.cloudsimple.com/azureoverviewpage/)