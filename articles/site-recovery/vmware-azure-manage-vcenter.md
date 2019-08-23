---
title: Hantera VMware vCenter-servrar för haveri beredskap för virtuella VMware-datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du lägger till och hanterar VMware vCenter för haveri beredskap för virtuella VMware-datorer till Azure med Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 59088d8351bf89c859312774e3e9e396be8dd532
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904255"
---
# <a name="manage-vmware-vcenter-server"></a>Hantera VMware vCenter Server

I den här artikeln beskrivs de olika Site Recovery åtgärder som kan utföras på en VMware vCenter. Kontrol lera [kraven](vmware-physical-azure-support-matrix.md#replicated-machines) innan du börjar.


## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurera ett konto för automatisk identifiering

Site Recovery behöver åtkomst till VMware för att processervern ska kunna identifiera virtuella datorer automatiskt och för redundans och återställning av virtuella datorer. Skapa ett konto för åtkomst enligt följande:

1. Logga in på Configuration Server-datorn.
2. Öppna starta cspsconfigtool. exe med hjälp av Skriv bords gen vägen.
3. Klicka på **Lägg till konto** på fliken **Hantera konto** .

   ![Lägg till konto](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Ange konto informationen och klicka på **OK** för att lägga till den.  Kontot måste ha behörigheterna sammanfattas i följande tabell. 

Det tar ungefär 15 minuter för konto informationen att synkroniseras med Site Recovery tjänsten.

### <a name="account-permissions"></a>Konto behörigheter

|**Aktivitet** | **Konto** | **Behörigheter** | **Detaljer**|
|--- | --- | --- | ---|
|**Automatisk identifiering/migrering (utan återställning efter fel)** | Du behöver minst en skrivskyddad användare | Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till** underordnad till underordnade objekt (vSphere-värdar, data lager, virtuella datorer och nätverk).|
|**Replikering/redundans** | Du behöver minst en skrivskyddad användare| Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med **spridning till** underordnat objekt till underordnade objekt (vSphere-värdar, data lager, virtuella datorer och nätverk).<br/><br/> Användbart för migrering, men inte fullständig replikering, redundans och återställning efter fel.|
|**Replikering/redundans/återställning** | Vi rekommenderar att du skapar en roll (AzureSiteRecoveryRole) med de behörigheter som krävs och sedan tilldelar rollen till en VMware-användare eller-grupp | Data Center objekt – > sprids till underordnat objekt, roll = AzureSiteRecoveryRole<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till** underordnad till underordnade objekt (vSphere-värdar, data lager, virtuella datorer och nätverk).|


## <a name="add-vmware-server-to-the-vault"></a>Lägg till VMware-servern i valvet

1. I Azure Portal öppnar du valvet > **Site Recovery infrastruktur** > **konfigurations**servrar och öppnar konfigurations servern.
2. Klicka på **+ vCenter**på sidan **information** .

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Ändra autentiseringsuppgifter

Ändra de autentiseringsuppgifter som används för att ansluta till vCenter-servern eller ESXi-värden på följande sätt:

1. Logga in på konfigurations servern och starta cspsconfigtool. exe från Skriv bordet.
2. Klicka på **Lägg till konto** på fliken **Hantera konto** .

   ![Lägg till konto](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Ange den nya konto informationen och klicka på **OK** för att lägga till den. Kontot måste ha behörigheterna som anges [ovan](#account-permissions).
4. På Azure Portal öppnar du valvet > **Site Recovery infrastruktur** > **konfigurations**servrar och öppnar konfigurations servern.
5. På sidan **information** klickar du på **Uppdatera server**.
6. När jobbet uppdaterings Server har slutförts väljer du vCenter Server för att öppna sidan vCenter- **Sammanfattning** .
7. Välj det nyligen tillagda kontot i fältet **vCenter Server/vSphere värd konto** och klicka på **Spara**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Ta bort en vCenter-Server

1. I Azure Portal öppnar du valvet > **Site Recovery infrastruktur** > **konfigurations**servrar och öppnar konfigurations servern.
2. På sidan **information** väljer du vCenter-servern.
3. Klicka på knappen **ta bort** .

   ![ta bort konto](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Ändra vCenter IP-adress och port

1. Logga in på Azure Portal.
2. Navigera till **Recovery Services valv** > **Site Recovery infrastruktur** > **konfigurations servrar**.
3. Klicka på konfigurations servern som vCenter har tilldelats.
4. I avsnittet **vCenter-servrar** klickar du på den vCenter som du vill ändra.
5. På sidan vCenter-Sammanfattning uppdaterar du IP-adressen och porten för vCenter i respektive fält och sparar sedan ändringarna.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. För att ändringarna ska börja gälla, vänta i 15 minuter eller [Uppdatera konfigurations servern](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migrera alla skyddade virtuella datorer till en ny vCenter

Om du vill migrera alla virtuella datorer till den nya vCenter ska du inte lägga till ett vCenter-konto. Detta kan leda till dubbla poster. Uppdatera bara IP-adressen för den nya vCenter:

1. Logga in på Azure Portal.
2. Navigera till **Recovery Services valv** > **Site Recovery infrastruktur** > **konfigurations servrar**.
3. Klicka på den konfigurations server som den gamla vCenter är tilldelad.
4. I avsnittet **vCenter-servrar** klickar du på den vCenter som du planerar att migrera från.
5. På sidan vCenter-Sammanfattning uppdaterar du IP-adressen för nya vCenter i fältet **vCenter Server/vSphere-värdnamn eller IP-adress**. Spara ändringarna.

Så fort IP-adressen uppdateras börjar Site Recovery-komponenter ta emot identifierings information för virtuella datorer från den nya vCenter. Detta påverkar inte pågående replikerings aktiviteter.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrera några skyddade virtuella datorer till en ny vCenter

> [!NOTE]
> Det här avsnittet gäller endast när du migrerar några av dina skyddade virtuella datorer till en ny vCenter. Om du vill skydda en ny uppsättning virtuella datorer från en ny vCenter [lägger du till nya vCenter-uppgifter till konfigurations servern](#add-vmware-server-to-the-vault) och börjar med **[Aktivera skydd](vmware-azure-tutorial.md#enable-replication)** .

Så här flyttar du några virtuella datorer till en ny vCenter:

1. [Lägg till den nya vCenter-informationen på konfigurations servern](#add-vmware-server-to-the-vault).
2. [Inaktivera replikering av de virtuella datorer](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) som du planerar att migrera.
3. Slutför migreringen av de valda virtuella datorerna till den nya vCenter.
4. Skydda nu migrerade virtuella datorer genom [att välja den nya vCenter när du aktiverar skydd](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Om antalet virtuella datorer som migreras är **högre** än antalet virtuella datorer som behålls i den gamla vCenter, uppdaterar du IP-adressen för den nya vcenteren med hjälp av anvisningarna här. [Inaktivera replikering](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)för några virtuella datorer som behålls på den gamla vcenteren. [Lägg till ny vCenter-information på konfigurations servern](#add-vmware-server-to-the-vault)och starta **[Aktivera skydd](vmware-azure-tutorial.md#enable-replication)** .

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

1. Om skyddade virtuella datorer flyttas från en ESXi-värd till en annan, kommer den att påverka replikeringen?

    Nej, detta påverkar inte den pågående replikeringen. Se dock [till att distribuera huvud mål servern med tillräckliga privilegier](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Vilka port nummer används för kommunikation mellan vCenter och andra Site Recovery-komponenter?

    Standard porten är 443. Konfigurations servern kommer att få åtkomst till vCenter/vSphere-värd information via den här porten. Klicka [här](#modify-the-vcenter-ip-address-and-port)om du vill uppdatera den här informationen.
