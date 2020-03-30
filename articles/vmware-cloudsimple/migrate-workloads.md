---
title: Azure VMware-lösning från CloudSimple – Migrera virtuella virtuella arbetsbelastningar till privat moln
description: Beskriver hur du migrerar virtuella datorer från lokalt vCenter till CloudSimple Private Cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020003"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Migrera virtuella virtuella arbetsbelastningar från lokalt vCenter till private cloud vCenter-miljö

Om du vill migrera virtuella datorer från ett lokalt datacenter till ditt CloudSimple Private Cloud finns flera alternativ tillgängliga.  Det privata molnet ger inbyggd åtkomst till VMware vCenter och verktyg som stöds av VMware kan användas för arbetsbelastningsmigrering. I den här artikeln beskrivs några av alternativen för vCenter-migrering.

## <a name="prerequisites"></a>Krav

Migrering av virtuella datorer och data från ditt lokala datacenter kräver nätverksanslutning från datacentret till din privata molnmiljö.  Använd någon av följande metoder för att upprätta nätverksanslutning:

* [Plats-till-plats VPN-anslutning](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) mellan din lokala miljö och ditt privata moln.
* ExpressRoute Global Reach-anslutning mellan din lokala ExpressRoute-krets och en CloudSimple ExpressRoute-krets.

Nätverkssökvägen från din lokala vCenter-miljö till ditt privata moln måste vara tillgänglig för migrering av virtuella datorer med vMotion.  VMotion-nätverket i ditt lokala vCenter måste ha routningsfunktioner.  Kontrollera att brandväggen tillåter all vMotion-trafik mellan ditt lokala vCenter och Private Cloud vCenter. (I det privata molnet konfigureras routning i vMotion-nätverket som standard.)

## <a name="migrate-isos-and-templates"></a>Migrera ISO-system och mallar

Om du vill skapa nya virtuella datorer i det privata molnet använder du ISO-mallar och VM-mallar.  Om du vill överföra Internetos och mallar till ditt privata moln vCenter och göra dem tillgängliga använder du följande metod.

1. Ladda upp ISO till Private Cloud vCenter från vCenter UI.
2. [Publicera ett innehållsbibliotek](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) på ditt privata moln vCenter:

    1. Publicera ditt lokala innehållsbibliotek.
    2. Skapa ett nytt innehållsbibliotek på Private Cloud vCenter.
    3. Prenumerera på det publicerade lokala innehållsbiblioteket.
    4. Synkronisera innehållsbiblioteket så att du får åtkomst till innehåll som prenumererar.

## <a name="migrate-vms-using-powercli"></a>Migrera virtuella datorer med PowerCLI

Om du vill migrera virtuella datorer från det lokala vCenter till Private Cloud vCenter använder du VMware PowerCLI eller Cross vCenter Workload Migration Utility som är tillgängligt från VMware Labs.  Följande exempelskript visar PowerCLI-migreringskommandon.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Om du vill använda namnen på målbaserade vCenter-servern och ESXi-värdar konfigurerar du DNS-vidarebefordran från lokalt till ditt privata moln.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrera virtuella datorer med VPN ISX Layer 2

Det här alternativet möjliggör livemigrering av arbetsbelastningar från din lokala VMware-miljö till det privata molnet i Azure.  Med det här utsträckta Layer 2-nätverket är undernätet lokalt tillgängligt i det privata molnet.  Efter migreringen krävs inte ny IP-adresstilldelning för de virtuella datorerna.

[Migrera arbetsbelastningar med Layer 2-utsträckta nätverk](migration-layer-2-vpn.md) beskriver hur du använder en Layer 2 VPN för att sträcka ut ett Layer 2-nätverk från din lokala miljö till ditt privata moln.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrera virtuella datorer med hjälp av verktyg för säkerhetskopiering och katastrofåterställning

Migrering av virtuella datorer till private cloud kan göras med hjälp av verktyg för säkerhetskopiering/återställning och verktyg för katastrofåterställning.  Använd det privata molnet som ett mål för återställning från säkerhetskopior som skapas med hjälp av ett verktyg från tredje part.  Det privata molnet kan också användas som ett mål för haveriberedskap med hjälp av VMware SRM eller ett tredjepartsverktyg.

Mer information om hur du använder de här verktygen finns i följande avsnitt:

* [Säkerhetskopiera virtuella datorer för arbetsbelastning på CloudSimple Private Cloud med Veeam B&R](backup-workloads-veeam.md)
* [Konfigurera CloudSimple Private Cloud som en plats för haveriberedskap för lokala VMware-arbetsbelastningar](disaster-recovery-zerto.md)
