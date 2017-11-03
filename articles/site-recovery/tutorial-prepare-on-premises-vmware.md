---
title: "Förbereda lokal VMware-servrar för katastrofåterställning av virtuella VMware-datorer till Azure | Microsoft Docs"
description: "Lär dig hur du förbereder lokal VMware-servrar för katastrofåterställning till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 90a4582c-6436-4a54-a8f8-1fee806b8af7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 33ec5775a371a04074f07d589d35d1c05bd64d30
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Förbereda lokal VMware-servrar för katastrofåterställning till Azure

Den här kursen visar hur du förbereder din lokala VMware infrastructure när du vill replikera virtuella VMware-datorer till Azure. I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Förbereda ett konto på vCenter server eller vSphere ESXi värden, att automatisera VM-identifiering
> * Förbereda ett konto för automatisk installation av mobilitetstjänsten på virtuella VMware-datorer
> * Granska kraven för VMware-server
> * Granska kraven för VMware VM

I den här självstudiekursen serien visar vi hur du säkerhetskopierar en enda virtuell dator med hjälp av Azure Site Recovery. Om du planerar att skydda flera virtuella VMware-datorer, bör du hämta den [distribution Kapacitetsplaneringsverktyget för](https://aka.ms/asr-deployment-planner) för VMware-replikering. Det här verktyget som du samlar in information om kompatibilitet för VM, diskar per virtuell dator och dataomsättningen per disk. Verktyget omfattar även kraven på nätverksbandbredd och Azure-infrastrukturen för lyckad replikering och testa redundans. [Lär dig mer](site-recovery-deployment-planner.md) om hur du kör verktyget.

Det här är den andra kursen i serien. Se till att du har [konfigurerar du Azure komponenterna](tutorial-prepare-azure.md) enligt beskrivningen i föregående kursen.

## <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery behöver åtkomst till VMware-servrar till:

- Upptäcker automatiskt virtuella datorer. Minst en skrivskyddad konto är obligatoriskt.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som till exempel att skapa och ta bort diskar och startar på virtuella datorer.

Skapa kontot enligt följande:

1. Om du vill använda ett särskilt konto skapar du en roll på vCenter-nivå. Namnge rollen som **Azure_Site_Recovery**.
2. Tilldela rollen behörigheterna sammanfattas i tabellen nedan.
3. Skapa en användare på vCenter server eller vSphere-värd. Tilldela användaren rollen.

### <a name="vmware-account-permissions"></a>Behörighet för VMware

**Aktivitet** | **Rollbehörigheter /** | **Detaljer**
--- | --- | ---
**VM-identifiering** | Minst en skrivskyddad användare<br/><br/> Data Center objektet –> Sprid till underordnade objekt rollen = skrivskyddad | Användaren tilldelas på nivån för datacenter och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten, tilldela den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datastores, virtuella datorer och nätverk).
**Fullständig replikering, redundans och återställning efter fel** |  Skapa en roll (Azure_Site_Recovery) med behörigheterna som krävs och sedan tilldela rollen till en VMware-användare eller grupp<br/><br/> Data Center objektet –> Sprid till underordnade objekt rollen = Azure_Site_Recovery<br/><br/> DataStore -> allokera utrymme, bläddra datalagret, låg nivå filåtgärder, ta bort filen och uppdatera filer för virtuella datorer<br/><br/> Nätverk -> nätverk tilldela<br/><br/> Resurs -> Tilldela VM resurspool, migrera är avstängt VM, migrera driven på den virtuella datorn<br/><br/> Aktiviteter -> Skapa uppgift, uppdatera uppgift<br/><br/> Konfiguration av virtuell dator -><br/><br/> Virtual machine -> interagera -> fråga enhetsanslutning, konfigurera CD-skivor, konfigurera diskettenheter media, stänga av, slå på strömmen, installera för VMware-verktyg<br/><br/> Virtual machine -> Lager -> Skapa, registrera, avregistrera<br/><br/> Etablering av virtuell dator -> -> Tillåt virtuella hämtning, tillåter Överför filer för virtuella datorer<br/><br/> Virtual machine -> ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på nivån för datacenter och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten, tilldela den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datastores, virtuella datorer och nätverk).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installationen av Mobility

Mobilitetstjänsten måste installeras på den virtuella datorn som du vill replikera. Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för den virtuella datorn. För automatisk installation måste du förbereda ett konto som Site Recovery för att komma åt den virtuella datorn. När du ställer in katastrofåterställning i Azure-konsolen ska du ange det här kontot.

1. Förbereda en domän eller lokalt konto med behörighet för att installera på den virtuella datorn.
2. Om du vill installera på virtuella Windows-datorer, om du inte använder ett domänkonto, inaktivera kontroll av fjärråtkomst till användare på den lokala datorn.
   - I registret under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, lägga till DWORD-posten **LocalAccountTokenFilterPolicy**, med värdet 1.
3. Förbered en rotkontot på Linux källservern för att installera på virtuella Linux-datorer.


## <a name="check-vmware-server-requirements"></a>Kontrollera kraven för VMware-server

Kontrollera att följande krav är uppfyllda VMware-servrar.

**Komponent** | **Krav**
--- | ---
**vCenter-servern** | vCenter 6.5, 6.0 eller 5.5
**vSphere-värd** | vSphere 6.5, 6.0, 5.5

## <a name="check-vmware-vm-requirements"></a>Kontrollera kraven för VMware VM

Kontrollera att den virtuella datorn uppfyller kraven för Azure sammanfattas i följande tabell.

**VM-krav** | **Detaljer**
--- | ---
**Operativsystemdisken** | Upp till 2 048 GB.
**Operativsystemet disk antal** | 1
**Datadiskar** | 64 eller mindre
**Datadisken för virtuell Hårddisk** | Upp till 4095 GB
**Nätverkskort** | Flera nätverkskort som stöds
**Delad virtuell Hårddisk** | Stöds inte
**FC-disk** | Stöds inte
**Format för hårddisk** | VHD- eller VHDX.<br/><br/> Även om VHDX inte stöds för närvarande i Azure, konverterar Site Recovery automatiskt VHDX till virtuell Hårddisk när du redundansväxlar till Azure. När du växlar tillbaka till lokala virtuella datorer kan du fortsätta att använda VHDX-format.
**BitLocker** | Stöds inte. Inaktivera innan du aktiverar replikering för en virtuell dator.
**Namn på virtuell dator** | Mellan 1 och 63 tecken.<br/><br/> Begränsat till bokstäver, siffror och bindestreck. VM-namn måste börja och sluta med en bokstav eller siffra.
**VM-typ** | Generation 1 - Linux eller Windows<br/><br/>Generation 2 - endast Windows

Den virtuella datorn måste också köra ett operativsystem som stöds. Finns det [Site Recovery-supportmatrisen](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) för en fullständig lista över versioner som stöds.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Under ett failover-scenario kan du vill ansluta till de replikerade virtuella datorerna i Azure från ditt lokala nätverk.

För att ansluta till virtuella Windows-datorer med RDP efter en växling vid fel, gör du följande:

1. För att få åtkomst via internet, aktiverar du RDP på den lokala virtuella datorn före redundans. Kontrollera att TCP och UDP-regler har lagts till för den **offentliga** profil och att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler.
2. För att komma åt via plats-till-plats-VPN, aktiverar du RDP på den lokala datorn. RDP ska tillåtas i den **Windows-brandväggen** -> **tillåtna appar och funktioner** för **domän och privat** nätverk.
   Kontrollera att operativsystemets SAN policyn är inställd att **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135). Det ska vara ingen Windows-uppdateringar som väntar på den virtuella datorn när du utlösa redundansväxling. Om det inte finns kan du inte logga in på den virtuella datorn förrän uppdateringen är klar.
3. På Windows Azure VM efter växling vid fel, kontrollera **starta diagnostik** att visa en skärmbild av den virtuella datorn. Om du inte kan ansluta, kontrollera att den virtuella datorn körs och granska dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

För att ansluta till virtuella Linux-datorer med hjälp av SSH efter växling vid fel, gör du följande:

1. Kontrollera att Secure Shell-tjänsten är inställd att starta automatiskt på datorn startar på den lokala datorn före redundans. Kontrollera att brandväggsreglerna tillåter en SSH-anslutning.

2. Tillåta inkommande anslutningar till SSH-porten för reglerna för nätverkssäkerhetsgrupper på den redundansväxlade virtuella datorn och Azure-undernätet som den är ansluten på Azure-VM efter växling vid fel.
   [Lägg till en offentlig IP-adress](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) för den virtuella datorn. Du kan kontrollera **starta diagnostik** att visa en skärmbild av den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera katastrofåterställning till Azure för virtuella VMware-datorer](tutorial-vmware-to-azure.md)
