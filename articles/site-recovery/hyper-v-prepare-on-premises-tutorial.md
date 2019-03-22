---
title: Förbereda lokala Hyper-V-servrar på haveriberedskap för Hyper-V-datorer till Azure | Microsoft Docs
description: Lär dig att förbereda lokala Hyper-V-datorer för haveriberedskap till Azure med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 127e970927e8ac1d0cd9b431c0c0175bdc4f5c0b
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315783"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Förbereda lokala Hyper-V-servrar på haveriberedskap till Azure

Den här självstudien visar hur du förbereder din lokala Hyper-V-infrastruktur när du vill replikera Hyper-V-datorer till Azure för haveriberedskap. Hyper-V-värdar kan hanteras av System Center Virtual Machine Manager (VMM), men det krävs inte.  I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Granska kraven för Hyper-V och kraven för VMM om det är tillämpligt.
> * Förbereda VMM om tillämpligt
> * Kontrollera Internetåtkomst till Azure-platser
> * Förbereda virtuella datorer så att du kan komma åt dem efter redundansväxlingen till Azure

Det här är den andra självstudien i serien. Se till att du har [konfigurerat Azure-komponenterna](tutorial-prepare-azure.md) enligt beskrivningen i föregående självstudie.



## <a name="review-requirements-and-prerequisites"></a>Granska krav och förutsättningar

Se till att Hyper-V-värdar och virtuella datorer uppfyller kraven.

1. [Kontrollera](hyper-v-azure-support-matrix.md#on-premises-servers) lokala server-krav.
2. [Kontrollera kraven](hyper-v-azure-support-matrix.md#replicated-vms) för Hyper-V-datorer som du vill replikera till Azure.
3. Kontrollera Hyper-V-värd [nätverk](hyper-v-azure-support-matrix.md#hyper-v-network-configuration); och värdens och gästens [storage](hyper-v-azure-support-matrix.md#hyper-v-host-storage) stöd för en lokal Hyper-V-värdar.
4. Kontrollera vad som stöds när det gäller [Azure-nätverk](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [lagring](hyper-v-azure-support-matrix.md#azure-storage) och [compute](hyper-v-azure-support-matrix.md#azure-compute-features) efter redundansväxling.
5. Dina lokala virtuella datorer som du replikerar till Azure måste uppfylla [kraven för virtuella Azure-datorer](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Förbereda VMM (valfritt)

Om Hyper-V-värdar hanteras av VMM, måste du förbereda en lokal VMM-servern. 

- Kontrollera att VMM-servern har minst ett moln, med en eller flera värdgrupper. Hyper-V-värden där virtuella datorer kör måste finnas i molnet.
- Förbereda VMM-servern för nätverksmappning.

### <a name="prepare-vmm-for-network-mapping"></a>Förbereda VMM för nätverksmappning

Om du använder VMM, [nätverksmappning](site-recovery-network-mapping.md) mappningar mellan en lokal virtuell dator i VMM-nätverk och virtuella Azure-nätverk. Mappningen säkerställer att virtuella Azure-datorer är anslutna till rätt nätverk när de skapas efter en redundansväxling.

Förbereda VMM nätverksmappningen på följande sätt:

1. Kontrollera att du har en [logiskt nätverk för VMM](https://docs.microsoft.com/system-center/vmm/network-logical) som associeras med det moln som Hyper-V-värdarna finns.
2. Kontrollera att du har en [Virtuellt datornätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) länkad till det logiska nätverket.
3. I VMM kan du ansluta de virtuella datorerna till det Virtuella datornätverket.

## <a name="verify-internet-access"></a>Kontrollera åtkomst till internet

1. För kursen är den enklaste konfigurationen för Hyper-V-värdar och VMM-servern får direkt åtkomst till internet utan att använda en proxyserver. 
2. Kontrollera att Hyper-V-värdar och VMM-servern om det behövs, kan komma åt nödvändiga adresserna nedan.   
3. Om du kontrollera åtkomst med IP-adress, se till att:
    - IP-adressbaserade brandväggsregler kan ansluta till [Azure Datacenter IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653), och HTTPS-port (443).
    - Tillåt IP-adressintervall för Azure-regionen för din prenumeration.
    
### <a name="required-urls"></a>URL: erna


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Under ett redundansscenario kan du ansluta till din replikerade lokala nätverk.

För att ansluta till Windows virtuella datorer med RDP efter en redundansväxling måste du tillåta åtkomst på följande sätt:

1. För att få åtkomst via Internet aktiverar du RDP på den lokala virtuella datorn före redundansen. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.
2. För åtkomst via plats-till-plats-VPN aktiverar du RDP på den lokala datorn. RDP ska tillåtas i **Windows-brandväggen** -> **Tillåtna appar och funktioner** för nätverken **Domän och privat**.
   Kontrollera att operativsystemets SAN-princip har angetts till **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135). Det får inte finnas några väntande Windows-uppdateringar på den virtuella datorn när du utlöser en redundans. Om det finns det kan du inte logga in på den virtuella datorn förrän uppdateringen är klar.
3. Efter en redundans av en virtuell Windows Azure-dator, kontrollerar du att **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om du inte kan ansluta kontrollerar du att den virtuella datorn körs. Granska sedan dessa [felsökningstips](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Du kan komma åt virtuella Azure-datorer med samma IP-adress som replikerade lokal virtuell dator eller en annan IP-adress efter redundansväxling. [Läs mer](concepts-on-premises-to-azure-networking.md) om hur du konfigurerar IP-adresser för redundans.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera katastrofåterställning till Azure för Hyper-V-datorer](tutorial-hyper-v-to-azure.md)
> [konfigurera katastrofåterställning till Azure för Hyper-V-datorer i VMM-moln](tutorial-hyper-v-vmm-to-azure.md)
