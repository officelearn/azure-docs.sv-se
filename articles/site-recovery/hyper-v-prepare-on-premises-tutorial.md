---
title: "Förbereda lokal Hyper-V-server för katastrofåterställning för Hyper-V virtuella datorer till Azure | Microsoft Docs"
description: "Lär dig hur du förbereder lokala Hyper-V virtuella datorer som inte hanteras av System Center VMM för katastrofåterställning till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9524ffde4a588d3ac029bc8a3df91726082e157d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Förbereda lokala Hyper-V-servrar för katastrofåterställning till Azure

Den här kursen visar hur du förbereder din lokala Hyper-V-infrastruktur när du vill replikera virtuella Hyper-V-datorer till Azure för katastrofåterställning. Hyper-V-värdar kan hanteras av System Center Virtual Machine Manager (VMM), men det krävs inte.  I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Granska krav för Hyper-V och VMM-krav i tillämpliga fall.
> * Förbereda VMM om tillämpligt
> * Kontrollera internet-åtkomst till Azure platser
> * Förbereda virtuella datorer så att du kan komma åt dem efter en redundansväxling till Azure

Det här är den andra kursen i serien. Se till att du har [konfigurerar du Azure komponenterna](tutorial-prepare-azure.md) enligt beskrivningen i föregående kursen.



## <a name="review-server-requirements"></a>Granska serverkrav

Kontrollera att Hyper-V-värdar uppfyller följande krav. Om du hanterar värdar i System Center Virtual Machine Manager (VMM) moln, kontrollera VMM krav.


**Komponent** | **Hyper-V som hanteras av VMM** | **Hyper-V utan VMM**
--- | --- | ---
**Operativsystemet för Hyper-V-värd** | Windows Server 2016, 2012 R2 | Ej tillämpligt
**VMM** | VMM 2012, VMM 2012 R2 | Ej tillämpligt


## <a name="review-hyper-v-vm-requirements"></a>Granska kraven för Hyper-V-dator

Kontrollera att den virtuella datorn uppfyller kraven sammanfattas i tabellen.

**VM-krav** | **Detaljer**
--- | ---
Gästoperativsystemet | Alla gästoperativsystemet [stöds av Azure](https://technet.microsoft.com/library/cc794868.aspx).
**Krav för Azure** | Lokal Hyper-V virtuella datorer måste uppfylla Azure VM requirements(site-recovery-support-matrix-to-azure.md).

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

1. Vid tillämpningen av kursen är den enklaste konfigurationen för Hyper-V-värdar och VMM-servern, om tillämpligt, får direkt åtkomst till internet utan att använda en proxyserver. 
2. Kontrollera att Hyper-V-värdar och VMM-server (om tillämpligt) kan komma åt dessa webbadresser: 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. Se till att:
    - IP-adressbaserade brandväggsregler ska tillåta kommunikation till Azure.
    - Tillåt [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653) (IP-intervall för Azures datacenter) och HTTPS-port 443.
    - Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för hantering av kontrollen och identitet).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Under ett failover-scenario kan du vill ansluta till nätverket replikerade lokalt.

För att ansluta till virtuella Windows-datorer med RDP efter en växling vid fel, gör du följande:

1. För att få åtkomst via internet, aktiverar du RDP på den lokala virtuella datorn före redundans. Kontrollera att TCP och UDP-regler har lagts till för den **offentliga** profil och att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler.
2. För att komma åt via plats-till-plats-VPN, aktiverar du RDP på den lokala datorn. RDP ska tillåtas i den **Windows-brandväggen** -> **tillåtna appar och funktioner** för **domän och privat** nätverk.
   Kontrollera att operativsystemets SAN policyn är inställd att **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135). Det ska vara ingen Windows-uppdateringar som väntar på den virtuella datorn när du utlösa redundansväxling. Om det inte finns kan du inte logga in på den virtuella datorn förrän uppdateringen är klar.
3. På Windows Azure VM efter växling vid fel, kontrollera **starta diagnostik** att visa en skärmbild av den virtuella datorn. Om du inte kan ansluta, kontrollera att den virtuella datorn körs och granska dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera katastrofåterställning till Azure för Hyper-V VMs](tutorial-hyper-v-to-azure.md)
> [konfigurera katastrofåterställning till Azure för Hyper-V virtuella datorer i VMM-moln](tutorial-hyper-v-vmm-to-azure.md)
