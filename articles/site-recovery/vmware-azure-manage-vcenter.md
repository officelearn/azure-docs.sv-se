---
title: " Hantera VMware vCenter-servrar i Azure Site Recovery | Microsoft Docs"
description: Den här artikeln beskrivs hur lägga till och hantera VMware vcenter-Server i Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: ramamill
ms.openlocfilehash: 6f3edf8e5d7a6fda1795991ac0a21cc316c29414
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950452"
---
# <a name="manage-vmware-vcenter-servers"></a>Hantera VMware vCenter-servrar 

Den här artikeln beskrivs de olika Site Recovery-åtgärder som kan utföras på en VMware vCenter. Kontrollera den [krav](vmware-physical-azure-support-matrix.md#replicated-machines) innan du börjar.


## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurera ett konto för automatisk identifiering

Site Recovery behöver åtkomst till VMware för processervern automatiskt identifiera virtuella datorer och för redundans och återställning efter fel för virtuella datorer. Skapa ett konto för åtkomst enligt följande:

1. Logga in på configuration server-dator.
2. Öppna Start cspsconfigtool.exe med hjälp av genvägen på skrivbordet.
3. Klicka på **Lägg till konto** på den **Hantera konto** fliken.

  ![Lägg till konto](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Anger information om kontot och klickar på **OK** att lägga till den.  Konton som ska ha de behörigheter som sammanfattas i tabellen nedan. 

Det tar cirka 15 minuter för informationen om att synkronisera upp med Site Recovery-tjänsten.

### <a name="account-permissions"></a>Behörigheterna

|**Aktivitet** | **Konto** | **Behörigheter** | **Detaljer**|
|--- | --- | --- | ---|
|**Automatisk identifiering/migrera (utan återställning)** | Du behöver minst en skrivskyddad användare | Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> För att begränsa åtkomsten tilldelar den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).|
|**Replikering/redundans** | Du behöver minst en skrivskyddad användare| Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> För att begränsa åtkomsten tilldelar den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).<br/><br/> Användbart för migrering, men inte fullständig replikering, redundans och återställning efter fel.|
|**Replikering/redundans/återställning** | Vi rekommenderar att du skapar en roll (AzureSiteRecoveryRole) med behörigheterna som krävs och tilldela rollen till en VMware-användare eller grupp | Datacenter-objekt – > Sprid till underordnat objekt, roll = AzureSiteRecoveryRole<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> För att begränsa åtkomsten tilldelar den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).|


## <a name="add-vmware-server-to-the-vault"></a>Lägga till VMware-servern i valvet

1. Öppna ditt valv på Azure-portalen > **Site Recovery-infrastruktur** > **Configuration servrar**, och öppna konfigurationsservern.
2. På den **information** klickar du på **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Ändra autentiseringsuppgifter

Ändra de autentiseringsuppgifter som används för att ansluta till vCenter-servern eller ESXi-värden på följande sätt:

1. Logga in på konfigurationsservern och starta cspsconfigtool.exe från skrivbordet.
2. Klicka på **Lägg till konto** på den **Hantera konto** fliken.

  ![Lägg till konto](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Anger de nya kontouppgifterna och klickar på **OK** att lägga till den. Konton som ska ha de behörigheter som listas [ovan](#account-permissions).
4. Öppna valvet på Azure-portalen > **Site Recovery-infrastruktur** > **Configuration servrar**, och öppna konfigurationsservern.
5. I den **information** klickar du på **uppdatera Server**.
6. När uppdatera Server-jobbet har slutförts väljer vCenter-servern för att öppna vCenter **sammanfattning** sidan.
7. Välj kontot som nyligen har lagts till i den **värdkonto för vCenter server/vSphere** fältet och klickar på **spara**.

    ![Ändra konto](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Ta bort en vCenter-server

1. Öppna ditt valv i Azure-portalen > **Site Recovery-infrastruktur** > **Configuration servrar**, och öppna konfigurationsservern.
2. På den **information** väljer vCenter-servern.
3. Klicka på den **ta bort** knappen.

  ![ta bort konto](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Om du behöver ändra IP-adress för vCenter, FQDN eller port måste ta bort vCenter-servern och lägga till den tillbaka till portalen.
