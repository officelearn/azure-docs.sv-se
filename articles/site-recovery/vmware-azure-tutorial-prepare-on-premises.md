---
title: Förbereda lokala VMware-servrar på haveriberedskap av virtuella VMware-datorer till Azure | Microsoft Docs
description: Lär dig att förbereda lokala VMware-servrar på haveriberedskap till Azure med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 536c173cd2bc3c32d9a205a5b4dff22cc987aaa4
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214621"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Förbereda lokala VMware-servrar på haveriberedskap till Azure

[Azure Site Recovery](site-recovery-overview.md) bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra dem vid planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer, inklusive replikering, redundans och återställning.

- Det här är den fjärde kursen i en serie som illustrerar hur du konfigurerar haveriberedskap i Azure för lokala virtuella VMware-datorer. I den första självstudien [konfigurerade vi de Azure-komponenter](tutorial-prepare-azure.md) som krävs för katastrofåterställning för VMware.


> [!NOTE]
> Självstudierna är utformade för att visa den enklaste distributionsvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i **instruktionsavsnittet** för motsvarande scenario.

I den här artikeln visar vi hur du förbereder din lokala VMware-miljö när du vill replikera virtuella VMware-datorer till Azure med Azure Site Recovery. Lär dig att:

> [!div class="checklist"]
> * Förbereda ett konto på vCenter-servern eller vSphere ESXi-värden, för att automatisera VM-identifiering
> * Förbereda ett konto för automatisk installation av mobilitetstjänsten på virtuella VMware-datorer
> * Granska kraven för VMware-servern och den virtuella datorn
> * Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling



## <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Identifiera automatiskt virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som till exempel att skapa och ta bort diskar samt starta virtuella datorer.

Skapa kontot enligt följande:

1. Om du vill använda ett särskilt konto skapar du en roll på vCenter-nivå. Ge rollen ett namn som t.ex. **Azure_Site_Recovery**.
2. Tilldela rollen behörigheterna som sammanfattas i tabellen nedan.
3. Skapa en användare på vCenter-servern eller vSphere-värden. Tilldela rollen till användaren.

### <a name="vmware-account-permissions"></a>Behörighet för VMware-konto

**Aktivitet** | **Roll/behörigheter** | **Detaljer**
--- | --- | ---
**VM-identifiering** | Minst en skrivskyddad användare<br/><br/> Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).
**Fullständig replikering, redundans och återställning efter fel** |  Skapa en roll (Azure_Site_Recovery) med behörigheterna som krävs och tilldela sedan rollen till en VMware-användare eller grupp<br/><br/> Datacenterobjekt –> Sprid till underordnat objekt, roll=Azure_Site_Recovery<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste vara installerad på datorer som du vill replikera. Site Recovery kan göra en push-installation av den här tjänsten när du aktiverar replikering för en dator eller så kan du installera den manuellt eller med installationsverktyg.

- I den här självstudien, installerar vi mobilitetstjänsten med push-installation.
- För den här push-installationen behöver du förbereda ett konto som Site Recovery kan använda för att komma åt den virtuella datorn. Du anger det här kontot när du konfigurerar haveriberedskap i Azure-konsolen.

Förbered kontot enligt följande:

Förbereda en domän eller ett lokalt konto med behörighet för att installera på den virtuella datorn.

- **Virtuella Windows-datorer**: Om du vill installera på virtuella Windows-datorer och inte använder ett domänkonto, inaktiverar du åtkomstkontroll för fjärranvändare på den lokala datorn. Du gör det genom att gå till registret > **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** och lägga till DWORD-posten **LocalAccountTokenFilterPolicy**, med värdet 1.
- **Virtuella Linux-datorer**: För att installera på virtuella Linux-datorer, förbereder du ett rotkonto på Linux-källservern.


## <a name="check-vmware-requirements"></a>Kontrollera VMware-kraven

Kontrollera att VMware-servrar och virtuella datorer uppfyller kraven.

1. [Kontrollera](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) VMware-serverkraven.
2. För virtuella Linux-datorer [kontrollerar](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) du filsystem- och lagringskraven. 
3. Kontrollera stödet för lokalt [nätverk](vmware-physical-azure-support-matrix.md#network) och [lagring](vmware-physical-azure-support-matrix.md#storage). 
4. Kontrollera vad som stöds när det gäller [Azure-nätverk](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [lagring](vmware-physical-azure-support-matrix.md#azure-storage) och [compute](vmware-physical-azure-support-matrix.md#azure-compute) efter redundansväxling.
5. Dina lokala virtuella datorer som du replikerar till Azure måste uppfylla [kraven för virtuella Azure-datorer](vmware-physical-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Efter redundansväxling, kan du vilja ansluta till virtuella Azure-datorer från ditt lokala nätverk.

Om du vill ansluta till virtuella Windows-datorer med RDP efter en redundans, gör du följande:

- **Internetåtkomst**. Innan redundans, aktiverar du RDP på den lokala virtuella datorn före redundans. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.
- **Plats-till-plats VPN-åtkomst**:
    - Innan redundans, aktiverar du RDP på den lokala datorn.
    - RDP ska tillåtas i **Windows-brandväggen** -> **Tillåtna appar och funktioner** för nätverken **Domän och privat**.
    - Kontrollera att operativsystemets SAN-princip har angetts till **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).
- Det får inte finnas några väntande Windows-uppdateringar på den virtuella datorn när du utlöser en redundans. Om det finns det kan du inte logga in på den virtuella datorn förrän uppdateringen är klar.
- Efter en redundans av en virtuell Windows Azure-dator, kontrollerar du att **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om du inte kan ansluta kontrollerar du att den virtuella datorn körs. Granska sedan dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Om du vill ansluta till virtuella Linux-datorer med SSH efter en redundans, gör du följande:

- Kontrollera att Secure Shell-tjänsten är inställd att starta automatiskt vid systemstart på den lokala datorn före redundans.
- Kontrollera att brandväggsreglerna tillåter en SSH-anslutning.
- Den virtuella Azure-datorn måste tillåta inkommande anslutningar till SSH-porten efter redundans för reglerna för nätverkssäkerhetsgrupper på den redundansväxlade virtuella datorn och Azure-undernätet som den är ansluten till.
- [Lägg till en offentlig IP-adress](site-recovery-monitoring-and-troubleshooting.md) för den virtuella datorn.
- Du kan kontrollera att **Startdiagnostik** visar en skärmbild av den virtuella datorn.


## <a name="failback-requirements"></a>Krav för återställning efter fel
Om du planerar att växla över till lokalt behöver du även se till att vissa [krav är uppfyllda](vmware-azure-reprotect.md##before-you-begin). Dessa **krävs dock inte för att komma igång med haveriberedskap** för dina virtuella datorer och kan även utföras efter en redundansväxling till Azure.

## <a name="useful-links"></a>Användbara länkar

Om du replikerar flera virtuella datorer bör du planera kapacitet och distribution innan du börjar. [Läs mer](site-recovery-deployment-planner.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap till Azure för virtuella VMware-datorer](vmware-azure-tutorial.md)
