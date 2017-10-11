---
title: "Krav för VMware till Azure replikering med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattas förutsättningarna för att replikera arbetsbelastningar som körs på virtuella VMware-datorer till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 318156ba-793b-48d0-98d4-cc5436bf28a3
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 7a82e58d9ff9208130c43fcd11d03dcc3238696a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="step-2-review-the-prerequisites-for-vmware-to-azure-replication"></a>Steg 2: Granska kraven för VMware till Azure-replikering

Läs förutsättningarna sammanfattas i följande tabell.

**Krav** | **Detaljer**
--- | ---
**Azure** | Lär dig mer om [krav för Azure](site-recovery-prereq.md#azure-requirements)
**Lokal konfigurationsserver** | Du behöver en VMware VM kör Windows Server 2012 R2 eller senare. Du konfigurerar den här servern under distributionen av Site Recovery.<br/><br/> Processervern och huvudmålservern installeras som standard också på den här virtuella datorn. När du skalar upp, du kan behöva en separat process-server och har samma krav som konfigurationsservern.<br/><br/> Lär dig mer om komponenterna [här](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)
**Lokal VMware-servrar** | En eller flera VMware vSphere-servrar, kör 6.5, 6.0, 5.5, 5.1 med senaste uppdateringarna. Servrarna måste finnas i samma nätverk som konfigurationsservern (eller separat processerver).<br/><br/> Vi rekommenderar en vCenter-server för att hantera värdar som kör 6.5, 6.0 eller 5.5 med de senaste uppdateringarna.
**Lokala virtuella datorer** | Virtuella datorer du vill replikera bör köra ett[operativsystem som stöds](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) och överensstämma med [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). VM ska ha VMware-verktygen körs.
**URL: er** | Konfigurationsservern behöver åtkomst till dessa webbadresser:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Om du har IP-adressbaserade brandväggsregler ontrollerar du att de tillåter kommunikation till Azure.<br/></br> Tillåt [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653) (IP-intervall för Azures datacenter) och HTTPS-port 443.<br/></br> Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för åtkomstkontroll och identitetshantering).<br/><br/> Tillåt URL: en för MySQL-hämtning: http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
**Mobilitetstjänsten** | Installeras på varje replikerade virtuella datorn.




## <a name="limitations"></a>Begränsningar

Kontrollera att du förstår de begränsningar som sammanfattas i tabellen innan du distribuerar.

**Begränsning** | **Detaljer**
--- | ---
**Azure** | Lagring och nätverk konton måste finnas i samma region som valvet<br/><br/> Om du använder ett premiumlagringskonto måste du också ett standard store-konto för att lagra replikeringsloggar<br/><br/> Du kan inte replikeras till premium-konton i Central och södra Indien.
**Lokal konfigurationsserver** | VMware VM korttypen ska VMXNET3. Om det inte är [installera denna uppdatering](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> vSphere PowerCLI 6.0 ska installeras.<br/><br> Datorn får inte vara en domänkontrollant. Datorn ska ha en statisk IP-adress.<br/><br/> Värdnamnet måste innehålla 15 tecken eller mindre, och operativsystemet ska vara på engelska.
**VMware** | Site Recovery har inte stöd för nya vCenter och vSphere 6.5 och 6.0 funktioner såsom mellan vCenter vMotion, virtuella volymerna och lagring DRS.
**Virtuella datorer** | Kontrollera [Virtuella Azure-begränsningar](site-recovery-prereq.md#azure-requirements)<br/><br/> Du kan replikera virtuella datorer med krypterade diskar eller virtuella datorer med UEFI/EFI-start.<br/><br> Delad disk inte stöds. Om källan VM har NIC-teamindelning, konverteras det till ett enda nätverkskort efter växling vid fel.<br/><br/> Om virtuella datorer har en iSCSI-disk, Site Recovery konverterar den till en VHD-fil efter växling vid fel. Om iSCSI-målet kan nås av den virtuella Azure-datorn ansluter till den och ser både den och den virtuella Hårddisken. Om det händer kan du koppla från iSCSI-målet.<br/><br/> Om du vill aktivera konsekvens för flera, som gör att virtuella datorer som kör samma arbetsbelastning som ska återställas tillsammans till en konsekventa data, kan du öppna port 20004 på den virtuella datorn.<br/><br/> Windows måste installeras på enhet C. OS-disk ska vara enkla och inte dynamiska. Datadisken kan vara dynamisk.<br/><br/> Linux/etc/hosts-filer på virtuella datorer bör innehålla poster som matchar namnet på lokala värddatorn till IP-adresser som är kopplade till alla nätverkskort. Värdnamn, monteringspunkter, enhetsnamn, system-sökvägar och filnamn (/ etc; / usr) ska endast på engelska.<br/><br/> Vissa typer av [Linux lagring](site-recovery-support-matrix-to-azure.md#support-for-storage) stöds.<br/><br/>Skapa eller ange **disk.enableUUID=true** i VM-inställningarna. Detta ger en konsekvent UUID till VMDK, så att den monterar korrekt och säkerställer att endast deltaändringar överförs tillbaka till lokala under återställning efter fel utan fullständig replikering.


## <a name="next-steps"></a>Nästa steg

- Om du utför en fullständig distribution går du till [steg3: Planera kapacitet](vmware-walkthrough-capacity.md)
- Om du gör en enkel testdistributionen, gå till [steg 4: Planera nätverk](vmware-walkthrough-network.md).
