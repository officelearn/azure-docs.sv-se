---
title: Förbereda lokal Hyper-V-server för katastrofåterställning för Hyper-V virtuella datorer till Azure | Microsoft Docs
description: Lär dig hur du förbereder lokala Hyper-V virtuella datorer som inte hanteras av System Center VMM för katastrofåterställning till Azure med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7e0219a662483ef123bdc2889a43dd3d93d23ac2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31413236"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Förbereda lokala Hyper-V-servrar för katastrofåterställning till Azure

Den här kursen visar hur du förbereder din lokala Hyper-V-infrastruktur när du vill replikera virtuella Hyper-V-datorer till Azure för katastrofåterställning. Hyper-V-värdar kan hanteras av System Center Virtual Machine Manager (VMM), men det krävs inte.  I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Granska krav för Hyper-V och VMM-krav i tillämpliga fall.
> * Förbereda VMM om tillämpligt
> * Kontrollera internet-åtkomst till Azure platser
> * Förbereda virtuella datorer så att du kan komma åt dem efter en redundansväxling till Azure

Det här är den andra självstudien i serien. Se till att du har [konfigurerat Azure-komponenterna](tutorial-prepare-azure.md) enligt beskrivningen i föregående självstudie.



## <a name="review-requirements-and-prerequisites"></a>Granska krav och förutsättningar

Se till att Hyper-V-värdar och virtuella datorer som uppfyller kraven.

1. [Kontrollera](hyper-v-azure-support-matrix.md#on-premises-servers) lokal server-krav.
2. [Kontrollera kraven](hyper-v-azure-support-matrix.md#replicated-vms) för Hyper-V virtuella datorer du vill replikera till Azure.
3. Kontrollera Hyper-V-värd [nätverk](hyper-v-azure-support-matrix.md#hyper-v-network-configuration); och värden och gästen [lagring](hyper-v-azure-support-matrix.md#hyper-v-host-storage) stöd för lokala Hyper-V-värdar.
4. Kontrollera vad som stöds när det gäller [Azure-nätverk](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [lagring](hyper-v-azure-support-matrix.md#azure-storage) och [compute](hyper-v-azure-support-matrix.md#azure-compute-features) efter redundansväxling.
5. Dina lokala virtuella datorer som du replikerar till Azure måste uppfylla [kraven för virtuella Azure-datorer](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Förbereda VMM (valfritt)

Om Hyper-V-värdar hanteras av VMM, måste du förbereda lokal VMM-servern. 

- Kontrollera att VMM-servern har ett minst ett moln med en eller flera värdgrupper. Hyper-V-värden som virtuella datorer som körs måste finnas i molnet.
- Förbereda VMM-servern för nätverksmappning.

### <a name="prepare-vmm-for-network-mapping"></a>Förbereda VMM för nätverksmappning

Om du använder VMM [nätverksmappning](site-recovery-network-mapping.md) mappningar mellan lokala VMM VM-nätverk och virtuella Azure-nätverk. Mappning säkerställer att virtuella datorer i Azure är ansluten till rätt nätverk när de skapas efter växling vid fel.

Förbered VMM nätverksmappningen på följande sätt:

1. Kontrollera att du har en [logiskt nätverk för VMM](https://docs.microsoft.com/system-center/vmm/network-logical) som associeras med det moln där Hyper-V-värdarna finns.
2. Kontrollera att du har en [Virtuellt datornätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) länkad till det logiska nätverket.
3. Anslut virtuella datorer i VMM till det Virtuella datornätverket.

## <a name="verify-internet-access"></a>Kontrollera internet-åtkomst

1. Vid tillämpningen av kursen är den enklaste konfigurationen för Hyper-V-värdar och VMM-servern får direkt åtkomst till internet utan att använda en proxyserver. 
2. Kontrollera att Hyper-V-värdar och VMM-server (om tillämpligt) kan komma åt dessa webbadresser: 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. Om du kontrollera åtkomst efter IP-adress, kontrollerar du att:
    - IP-adressbaserade brandväggsregler kan ansluta till [IP-intervall för Azure-Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653), och port för HTTPS (443).
    - Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för hantering av kontrollen och identitet).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Under ett failover-scenario kan du vill ansluta till nätverket replikerade lokalt.

Tillåt åtkomst på följande sätt för att ansluta till virtuella Windows-datorer med RDP efter en växling vid fel:

1. För att få åtkomst via Internet aktiverar du RDP på den lokala virtuella datorn före redundansen. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.
2. För åtkomst via plats-till-plats-VPN aktiverar du RDP på den lokala datorn. RDP ska tillåtas i **Windows-brandväggen** -> **Tillåtna appar och funktioner** för nätverken **Domän och privat**.
   Kontrollera att operativsystemets SAN-princip har angetts till **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135). Det får inte finnas några väntande Windows-uppdateringar på den virtuella datorn när du utlöser en redundans. Om det finns det kan du inte logga in på den virtuella datorn förrän uppdateringen är klar.
3. Efter en redundans av en virtuell Windows Azure-dator, kontrollerar du att **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om du inte kan ansluta kontrollerar du att den virtuella datorn körs. Granska sedan dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Efter växling vid fel, kan du komma åt virtuella Azure-datorer med samma IP-adress som den replikerade lokala virtuella datorn eller en annan IP-adress. [Lär dig mer](concepts-on-premises-to-azure-networking.md) om hur du konfigurerar IP-adresser för redundans.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera katastrofåterställning till Azure för Hyper-V VMs](tutorial-hyper-v-to-azure.md)
> [konfigurera katastrofåterställning till Azure för Hyper-V virtuella datorer i VMM-moln](tutorial-hyper-v-vmm-to-azure.md)
