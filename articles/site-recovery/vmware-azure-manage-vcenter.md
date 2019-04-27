---
title: Hantera VMware vCenter-servrar för haveriberedskap för virtuella VMware-datorer till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskrivs hur lägga till och hantera VMware vCenter för haveriberedskap för virtuella VMware-datorer till Azure med Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 9694c682f171ab715812b05fed2064c9bbcd36b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60600416"
---
# <a name="manage-vmware-vcenter-server"></a>Hantera VMware vCenter-server

Den här artikeln beskrivs de olika Site Recovery-åtgärder som kan utföras på en VMware vCenter. Kontrollera den [krav](vmware-physical-azure-support-matrix.md#replicated-machines) innan du börjar.


## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurera ett konto för automatisk identifiering

Site Recovery behöver åtkomst till VMware för processervern automatiskt identifiera virtuella datorer och för redundans och återställning efter fel för virtuella datorer. Skapa ett konto för åtkomst enligt följande:

1. Logga in på configuration server-dator.
2. Öppna Start cspsconfigtool.exe med hjälp av genvägen på skrivbordet.
3. Klicka på **Lägg till konto** på den **Hantera konto** fliken.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Anger information om kontot och klickar på **OK** att lägga till den.  Konton som ska ha de behörigheter som sammanfattas i tabellen nedan. 

Det tar cirka 15 minuter för informationen om att synkronisera upp med Site Recovery-tjänsten.

### <a name="account-permissions"></a>Behörigheterna

|**Aktivitet** | **Konto** | **Behörigheter** | **Detaljer**|
|--- | --- | --- | ---|
|**Automatisk identifiering/migrera (utan återställning)** | Du behöver minst en skrivskyddad användare | Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> För att begränsa åtkomsten tilldelar den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).|
|**Replikering/redundans** | Du behöver minst en skrivskyddad användare| Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> För att begränsa åtkomsten tilldelar den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).<br/><br/> Användbart för migrering, men inte fullständig replikering, redundans och återställning efter fel.|
|**Replikering/redundans/återställning** | Vi rekommenderar att du skapar en roll (AzureSiteRecoveryRole) med behörigheterna som krävs och tilldela rollen till en VMware-användare eller grupp | Data Center object –> Propagate to Child Object, role=AzureSiteRecoveryRole<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> För att begränsa åtkomsten tilldelar den **ingen åtkomst** roll med den **Sprid till underordnad** objekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).|


## <a name="add-vmware-server-to-the-vault"></a>Lägga till VMware-servern i valvet

1. Öppna ditt valv i Azure-portalen > **Site Recovery-infrastruktur** > **Configuration servrar**, och öppna konfigurationsservern.
2. På den **information** klickar du på **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Ändra autentiseringsuppgifter

Ändra de autentiseringsuppgifter som används för att ansluta till vCenter-servern eller ESXi-värden på följande sätt:

1. Logga in på konfigurationsservern och starta cspsconfigtool.exe från skrivbordet.
2. Klicka på **Lägg till konto** på den **Hantera konto** fliken.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Anger de nya kontouppgifterna och klickar på **OK** att lägga till den. Konton som ska ha de behörigheter som listas [ovan](#account-permissions).
4. Öppna valvet på Azure-portalen > **Site Recovery-infrastruktur** > **Configuration servrar**, och öppna konfigurationsservern.
5. I den **information** klickar du på **uppdatera Server**.
6. När uppdatera Server-jobbet har slutförts väljer vCenter-servern för att öppna vCenter **sammanfattning** sidan.
7. Välj kontot som nyligen har lagts till i den **värdkonto för vCenter server/vSphere** fältet och klickar på **spara**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Ta bort en vCenter-server

1. Öppna ditt valv i Azure-portalen > **Site Recovery-infrastruktur** > **Configuration servrar**, och öppna konfigurationsservern.
2. På den **information** väljer vCenter-servern.
3. Klicka på den **ta bort** knappen.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Ändra vCenter IP-adress och port

1. Logga in på Azure Portal.
2. Gå till **Recovery Services-valv** > **Site Recovery-infrastruktur** > **Konfigurationsservrar**.
3. Klicka på konfigurationsservern vCenter har tilldelats.
4. I den **vCenter-servrar** klickar du på det vCenter som du vill ändra.
5. Uppdatera IP-adressen och porten för vCenter i respektive fält på sammanfattningssidan vCenter och spara sedan ändringarna.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Vänta i 15 minuter för att ändringarna ska träda i kraft, eller [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migrera alla skyddade virtuella datorer till en ny vCenter

Lägg inte till ett annat vCenter-konto för att migrera alla virtuella datorer till den nya vCenter. Detta kan leda till dubbla poster. Bara uppdatera IP-adressen för den nya vCenter:

1. Logga in på Azure Portal.
2. Gå till **Recovery Services-valv** > **Site Recovery-infrastruktur** > **Konfigurationsservrar**.
3. Klicka på konfigurationsservern gamla vCenter har tilldelats.
4. I den **vCenter-servrar** klickar du på det vCenter som du planerar att migrera från.
5. Uppdatera IP-adressen för nya vCenter i fältet på sammanfattningssidan vCenter **vCenter server/vSphere värdnamn eller IP-adress**. Spara ändringarna.

När IP-adressen uppdateras, börjar Site Recovery-komponenter ta emot information om identifiering av virtuella datorer från den nya vCenter. Detta påverkar inte aktiviteter för pågående replikering.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrera några skyddade virtuella datorer till en ny vCenter

> [!NOTE]
> Det här avsnittet gäller bara när du migrerar några av de skyddade virtuella datorerna till en ny vCenter. Om du vill skydda en ny uppsättning virtuella datorer från en ny vCenter [lägga till nya vCenter-information till konfigurationsservern](#add-vmware-server-to-the-vault) och börja med  **[Skyddsaktivering](vmware-azure-tutorial.md#enable-replication)**.

Flytta några virtuella datorer till en ny vCenter:

1. [Lägg till ny vCenter-information till konfigurationsservern](#add-vmware-server-to-the-vault).
2. [Inaktivera replikering av virtuella datorer](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) du planerar att migrera.
3. Slutföra migreringen av den valda virtuella datorerna till den nya vCenter.
4. Nu kan skydda den migrerade virtuella datorer med [att välja ny vCenter när du aktiverar skydd](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Om antalet virtuella datorer som migreras är **högre** att antalet virtuella datorer kvar i den gamla vCenter uppdaterar IP-adressen för den nya vCenter med hjälp av anvisningarna här. För några virtuella datorer som ska finnas kvar på den gamla vCenter [inaktivera replikering](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [lägga till nya vCenter-information till konfigurationsservern](#add-vmware-server-to-the-vault), och starta  **[Skyddsaktivering](vmware-azure-tutorial.md#enable-replication)**.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

1. Om skyddade virtuella datorer flyttas från en ESXi-värd till en annan, kommer det att påverka replikering?

    Nej, detta påverkar inte den pågående replikeringen. Dock [se till att distribuera huvudmålservern med rätt behörighet](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Vilka är portnummer som används för att för kommunikation mellan vCenter och andra Site Recovery komponenter?

    Standardporten är 443. Konfigurationsservern kommer åt vCenter/vSphere-värdens information via den här porten. Om du vill uppdatera den här informationen klickar du på [här](#modify-the-vcenter-ip-address-and-port).
