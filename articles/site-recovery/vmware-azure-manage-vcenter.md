---
title: " Hantera VMware vCenter-servrar i Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur lägga till och hantera VMware vCenter i Azure Site Recovery."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 21e1a7bee7bba2f410a2841505cb6a6f8673bac3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="manage-vmware-vcenter-servers"></a>Hantera VMware vCenter-servrar 

Den här artikeln beskrivs de olika Site Recovery-åtgärder som kan utföras på en VMware vCenter. Kontrollera den [krav](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)s innan du börjar.


## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurera ett konto för automatisk identifiering

Site Recovery behöver åtkomst till VMware för processerver för att automatiskt identifiera virtuella datorer och för redundans och återställning av virtuella datorer. Skapa ett konto för åtkomst enligt följande:

1. Logga in på serverdatorn konfiguration.
2. Öppna en start cspsconfigtool.exe med genvägen på skrivbordet.
3. Klicka på **Lägg till konto** på den **Hantera konto** fliken.

  ![Lägg till konto](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Ange Kontodetaljer och klicka på **OK** att lägga till den.  Kontot måste ha behörigheter som sammanfattas i följande tabell. 

Det tar ungefär 15 minuter för kontoinformationen synkroniseras in med Site Recovery-tjänsten.

### <a name="account-permissions"></a>Behörighet

|**Aktivitet** | **Konto** | **Behörigheter** | **Detaljer**|
|--- | --- | --- | ---|
|**Automatisk identifiering/migrera (utan återställning)** | Du behöver minst en skrivskyddad användare | Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten, tilldela den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datastores, virtuella datorer och nätverk).|
|**Replikerings-Failover** | Du behöver minst en skrivskyddad användare| Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten, tilldela den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datastores, virtuella datorer och nätverk).<br/><br/> Användbart för migrering, men inte fullständig replikering, redundans och återställning efter fel.|
|**Replikering/redundans/återställning efter fel** | Vi rekommenderar att du skapar en roll (AzureSiteRecoveryRole) med behörigheterna som krävs och sedan tilldela rollen till en VMware-användare eller grupp | Data Center objektet –> Sprid till underordnade objekt rollen = AzureSiteRecoveryRole<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten, tilldela den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datastores, virtuella datorer och nätverk).|


## <a name="add-vmware-server-to-the-vault"></a>Lägg till VMware-servern i valvet

1. Öppna ditt valv på Azure-portalen > **Site Recovery-infrastruktur** > **Configuration servrarna**, och öppna konfigurationsservern.
2. På den **information** klickar du på **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Ändra autentiseringsuppgifter

Ändra de autentiseringsuppgifter som används för att ansluta till vCenter-servern eller ESXi-värd på följande sätt:

1. Logga in på konfigurationsservern och starta cspsconfigtool.exe från skrivbordet.
2. Klicka på **Lägg till konto** på den **Hantera konto** fliken.

  ![Lägg till konto](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Anger de nya kontouppgifterna och klickar på **OK** att lägga till den. Kontot bör ha de behörigheter som visas [ovan](#account-permissions).
4. Öppna valvet på Azure-portalen > **Site Recovery-infrastruktur** > **Configuration servrarna**, och öppna konfigurationsservern.
5. I den **information** klickar du på **uppdatera Server**.
6. Efter att uppdatera Server-jobbet är slutfört, Välj vCenter-servern för att öppna vCenter **sammanfattning** sidan.
7. Markera det nya kontot i den **vCenter server/vSphere värdkontot** fältet och klickar på **spara**.

    ![Ändra konto](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Ta bort en vCenter-server

1. Öppna ditt valv i Azure-portalen > **Site Recovery-infrastruktur** > **Configuration servrarna**, och öppna konfigurationsservern.
2. På den **information** väljer vCenter-servern.
3. Klicka på den **ta bort** knappen.

  ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Om du behöver ändra vCenter IP-adress, FQDN eller port måste ta bort vCenter-servern och lägga till den tillbaka till portalen.
