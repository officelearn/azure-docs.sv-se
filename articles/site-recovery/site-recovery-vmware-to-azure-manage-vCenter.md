---
title: " Hantera en VMware vCenter-server i Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur lägga till och hantera VMware vCenter i Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 091f0884417535427c52beee7bcdc5ed1dd83315
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>Hantera VMware vCenter-servern i Azure Site Recovery
Den här artikeln beskrivs de olika Site Recovery-åtgärder som kan utföras på en VMware vCenter.

## <a name="prerequisites"></a>Krav

**Stöd för VMware vCenter- och VMware vSphere ESX-värd** | **Detaljer** |
|--- | --- |
|**Lokal VMware-servrar** | En eller flera VMware vSphere-servrar, kör 6.0, 5.5, 5.1 med senaste uppdateringarna. Servrarna måste finnas i samma nätverk som konfigurationsservern (eller separat processerver).<br/><br/> Vi rekommenderar en vCenter-server för att hantera värdar som kör 6.0 eller 5.5 med de senaste uppdateringarna. Funktioner som är tillgängliga i 5.5 stöds när du distribuerar version 6.0.|

## <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering
Site Recovery behöver åtkomst till VMware för processerver för att automatiskt identifiera virtuella datorer och för redundans och återställning av virtuella datorer.

* **Migrera**: Om du endast vill migrera virtuella VMware-datorer till Azure, utan att någonsin återställas dem. Du kan använda en VMware-konto med en skrivskyddad roll. Sådana rollen kan köra växling vid fel, men det går inte att stänga av skyddade källdatorer. Detta är inte nödvändigt för migrering.
* **Replikera eller återställa**: Om du vill distribuera fullständig replikering (replikering, redundans, återställning) kontot måste kunna köra åtgärder som till exempel skapa och ta bort diskar, startar på den virtuella datorn.
* **Automatisk identifiering**: krävs minst ett konto för skrivskyddad.


|**Uppgifter** | **Nödvändiga konto-rollen** | **Behörigheter** | **Detaljer**|
|--- | --- | --- | ---|
|**Processervern identifierar automatiskt virtuella VMware-datorer** | Du behöver minst en skrivskyddad användare | Data Center objektet –> Sprid till underordnade objekt rollen = skrivskyddad | Användaren tilldelas på nivån för datacenter och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten, tilldela den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datastores, virtuella datorer och nätverk).|
|**Växling vid fel** | Du behöver minst en skrivskyddad användare | Data Center objektet –> Sprid till underordnade objekt rollen = skrivskyddad | Användaren tilldelas på nivån för datacenter och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten, tilldela den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datastores, virtuella datorer och nätverk).<br/><br/> Användbart för migrering, men inte fullständig replikering, redundans och återställning efter fel.|
|**Redundans och återställning efter fel** | Vi rekommenderar att du skapar en roll (AzureSiteRecoveryRole) med behörigheterna som krävs och sedan tilldela rollen till en VMware-användare eller grupp | Data Center objektet –> Sprid till underordnade objekt rollen = AzureSiteRecoveryRole<br/><br/> DataStore -> allokera utrymme, bläddra datalagret, låg nivå filåtgärder, ta bort filen och uppdatera filer för virtuella datorer<br/><br/> Nätverk -> nätverk tilldela<br/><br/> Resurs -> Tilldela VM resurspool, migrera är avstängt VM, migrera driven på den virtuella datorn<br/><br/> Aktiviteter -> Skapa uppgift, uppdatera uppgift<br/><br/> Konfiguration av virtuell dator -><br/><br/> Virtual machine -> interagera -> fråga enhetsanslutning, konfigurera CD-skivor, konfigurera diskettenheter media, stänga av, slå på strömmen, installera för VMware-verktyg<br/><br/> Virtual machine -> Lager -> Skapa, registrera, avregistrera<br/><br/> Etablering av virtuell dator -> -> Tillåt virtuella hämtning, tillåter Överför filer för virtuella datorer<br/><br/> Virtual machine -> ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på nivån för datacenter och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten, tilldela den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datastores, virtuella datorer och nätverk).|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>Skapa ett konto för att ansluta till VMware vCenter-Server / EXSi VMware vSphere-värd
1. Logga in på konfigurationsservern och starta cspsconfigtool.exe med genvägen på skrivbordet.
2. Klicka på **Lägg till konto** på den **Hantera konto** fliken.

  ![Lägg till konto](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Ange kontoinformation och klicka på OK om du vill lägga till kontot. Kontot bör ha de behörigheter som anges i den [förbereda ett konto för automatisk upptäckt](#prepare-an-account-for-automatic-discovery) avsnitt.

  >[!NOTE]
  Det tar ungefär 15 minuter för kontoinformationen synkroniseras in med Site Recovery-tjänsten.


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>Associera en VMware vCenter / vSphere VMware ESX-värd (Lägg till vCenter)
* På Azure-portalen går du till *YourRecoveryServicesVault* > **Site Recovery-infrastruktur** > **Configuration servrarna**  >  *ConfigurationServer*
* I den konfigurationsservern informationssidan klickar du på den + vCenter-knappen.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>Ändra autentiseringsuppgifter som används för att ansluta till vCenter-server / vSphere ESXi-värd

1. Logga in på konfigurationsservern och starta cspsconfigtool.exe
2. Klicka på **Lägg till konto** på den **Hantera konto** fliken.

  ![Lägg till konto](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Ange de nya kontouppgifterna och klicka på OK om du vill lägga till kontot. Kontot bör ha de behörigheter som anges i den [förbereda ett konto för automatisk upptäckt](#prepare-an-account-for-automatic-discovery) avsnitt.
4. På Azure-portalen går du till *YourRecoveryServicesVault* > **Site Recovery-infrastruktur** > **Configuration servrarna**  >  *ConfigurationServer*
5. I den konfigurationsservern informationssidan klickar du på den **uppdatera Server** knappen.
6. Välj vCenter-servern för att öppna sidan vCenter sammanfattning när servern uppdateringsjobb har slutförts.
7. Markera det nya kontot i den **vCenter server/vSphere värdkontot** och klicka på den **spara** knappen.

  ![Ändra konto](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>Ta bort en vCenter i Azure Site Recovery
1. På Azure-portalen går du till *YourRecoveryServicesVault* > **Site Recovery-infrastruktur** > **Configuration servrarna**  >  *ConfigurationServer*
2. Sidan Välj vCenter-servern för att öppna sidan vCenter sammanfattning i konfigurationen serverinformation.
3. Klicka på den **ta bort** för att ta bort vCenter

  ![ta bort konto](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Om du behöver ändra Vcenter IP-adress/FQDN, Port information måste du ta bort vCenter-servern och lägga till den igen.
