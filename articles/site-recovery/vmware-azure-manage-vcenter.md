---
title: Hantera VMware vCenter-servrar i Azure Site Recovery
description: I den här artikeln beskrivs hur du lägger till och hanterar VMware vCenter för haveriberedskap för virtuella virtuella datorer med VMware till Azure med Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257269"
---
# <a name="manage-vmware-vcenter-server"></a>Hantera VMware vCenter Server

I den här artikeln sammanfattas hanteringsåtgärder på en VMware vCenter Server i [Azure Site Recovery](site-recovery-overview.md).

## <a name="verify-prerequisites-for-vcenter-server"></a>Verifiera förutsättningar för vCenter Server

Förutsättningarna för vCenter-servrar och virtuella datorer vid haveriberedskap av virtuella datorer med VMware till Azure visas i [supportmatrisen](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="set-up-an-account-for-automatic-discovery"></a>Skapa ett konto för automatisk identifiering

När du ställer in haveriberedskap för lokala virtuella datorer med VMware behöver site recovery åtkomst till vCenter Server/vSphere-värden. Site Recovery process server kan sedan automatiskt identifiera virtuella datorer och växla över dem efter behov. Som standard körs processservern på konfigurationsservern för platsåterställning. Lägg till ett konto för konfigurationsservern för att ansluta till vCenter Server/vSphere-värden enligt följande:

1. Logga in på konfigurationsservern.
1. Öppna konfigurationsserververktyget _(cspsconfigtool.exe)_ med genvägen Till skrivbordet.
1. Klicka på **Lägg till konto**på fliken Hantera **konto** .

   ![lägg till konto](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Ange kontoinformation och klicka på **OK** för att lägga till den. Kontot ska ha behörigheterna sammanfattade i tabellen kontobehörigheter.

   > [!NOTE]
   > Det tar ungefär 15 minuter att synkronisera kontoinformation med Site Recovery.

### <a name="account-permissions"></a>Kontobehörigheter

|**Aktivitet** | **Konto** | **Behörigheter** | **Detaljer**|
|--- | --- | --- | ---|
|**Vm-identifiering/migrering (utan återställning)** | Åtminstone ett skrivskyddat användarkonto. | Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Propagate till det underordnade** objektet till de underordnade objekten (vSphere-värdar, datalager, virtuella datorer och nätverk).|
|**Replikering/redundans** | Åtminstone ett skrivskyddat användarkonto. | Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Propagate till underordnade** objekt till de underordnade objekten (vSphere-värdar, datalager, virtuella datorer och nätverk).<br/><br/> Användbart för migreringsändamål, men inte fullständig replikering, redundans, återställning efter fel.|
|**Replikering/redundans/redundans** | Vi föreslår att du skapar en roll (AzureSiteRecoveryRole) med de behörigheter som krävs och sedan tilldelar rollen till en VMware-användare eller grupp. | Data Center-objekt – > sprida till underordnade objekt, roll=AzureSiteRecoveryRole<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Propagate till det underordnade** objektet till de underordnade objekten (vSphere-värdar, datalager, virtuella datorer och nätverk).|

## <a name="add-vmware-server-to-the-vault"></a>Lägg till VMware-server i valvet

När du ställer in haveriberedskap för lokala virtuella datorer med VMware lägger du till den vCenter Server/vSphere-värd där du upptäcker virtuella datorer i valvet för platsåterställning enligt följande:

1. Öppna konfigurationsservern i > konfiguration > av infrastruktur för **platsåterställning.****Configuration Severs**
1. Klicka på **vCenter**på sidan **Information** .
1. I **Lägg till vCenter**anger du ett eget namn för vSphere-värden eller vCenter-servern.
1. Ange serverns IP-adress eller FQDN.
1. Lämna porten som 443 om inte dina VMware-servrar är konfigurerade för att lyssna efter begäranden på en annan port.
1. Välj det konto som används för att ansluta till VMware vCenter eller vSphere ESXi-servern. Klicka sedan på **OK**.

## <a name="modify-credentials"></a>Ändra autentiseringsuppgifter

Om det behövs kan du ändra de autentiseringsuppgifter som används för att ansluta till vCenter Server/vSphere-värden enligt följande:

1. Logga in på konfigurationsservern.
1. Öppna konfigurationsserververktyget _(cspsconfigtool.exe)_ med genvägen Till skrivbordet.
1. Klicka på **Lägg till konto** på fliken Hantera **konto.**

   ![lägg till konto](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Ange de nya kontouppgifterna och klicka på **OK**. Kontot behöver de behörigheter som anges i tabellen [kontobehörigheter.](#account-permissions)
1. Öppna konfigurationsservern i valv > konfiguration av infrastruktur > för **platsåterställningsinfrastruktur.****Configuration Severs**
1. Klicka på **Uppdatera server**i **Information**.
1. När jobbet Uppdatera server har slutförts väljer du vCenter-servern.
1. I **Sammanfattning**väljer du det nyligen tillagda kontot i **vCenter-server/vSphere-värdkontot**och klickar på **Spara**.

   ![ändra-konto](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Ta bort en vCenter-server

1. Öppna konfigurationsservern i valv > konfiguration av infrastruktur > för **platsåterställningsinfrastruktur.****Configuration Severs**
1. Välj vCenter-servern på sidan **Information.**
1. Klicka på knappen **Ta bort.**

   ![ta bort-konto](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Ändra IP-adress och -port

Du kan ändra IP-adressen för vCenter-servern eller de portar som används för kommunikation mellan servern och platsåterställning. Som standard kommer site recovery till åtkomst till vCenter Server/vSphere-värdinformation via port 443.

1. Klicka på konfigurationsservern som vCenter-servern läggs till i > **Site Recovery Infrastructure** > valvdatabasens**konfigurationsinfrastrukturservrar**i valvet.
1. I **vCenter-servrar**klickar du på den vCenter-server som du vill ändra.
1. I **Sammanfattning**uppdaterar du IP-adressen och porten och sparar ändringarna.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. Vänta i 15 minuter för att ändringarna ska träda i kraft eller [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Migrera alla virtuella datorer till en ny server

Om du vill migrera alla virtuella datorer för att använda en ny vCenter-server behöver du bara uppdatera IP-adressen som tilldelats vCenter-servern. Lägg inte till ett VMware-konto, eftersom det kan leda till dubblettposter. Uppdatera adressen enligt följande:

1. Klicka på konfigurationsservern som vCenter-servern läggs till i > **Site Recovery Infrastructure** > valvdatabasens**konfigurationsinfrastrukturservrar**i valvet.
1. Klicka på den vCenter-server som du vill migrera från i avsnittet **vCenter-servrar.**
1. I **Sammanfattning**uppdaterar du IP-adressen till den nya vCenter-servern och sparar ändringarna.
1. Så snart IP-adressen har uppdaterats börjar Site Recovery ta emot vm-identifieringsinformation från den nya vCenter-servern. Detta påverkar inte pågående replikeringsaktiviteter.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Migrera några virtuella datorer till en ny server

Om du bara vill migrera några av dina replikerings virtuella datorer till en ny vCenter-server gör du följande:

1. [Lägg till](#add-vmware-server-to-the-vault) den nya vCenter-servern på konfigurationsservern.
1. [Inaktivera replikering](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för virtuella datorer som flyttas till den nya servern.
1. I VMware migrerar du de virtuella datorerna till den nya vCenter-servern.
1. [Aktivera replikering](vmware-azure-tutorial.md#enable-replication) för de migrerade virtuella datorerna igen och välj den nya vCenter-servern.

## <a name="migrate-most-vms-to-a-new-server"></a>Migrera de flesta virtuella datorer till en ny server

Om antalet virtuella datorer som du vill migrera till en ny vCenter-server är högre än antalet virtuella datorer som ska finnas kvar på den ursprungliga vCenter-servern gör du följande:

1. [Uppdatera IP-adressen](#modify-the-ip-address-and-port) som tilldelats vCenter-servern i konfigurationsserverinställningarna till adressen till den nya vCenter-servern.
1. [Inaktivera replikering](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för de få virtuella datorer som finns kvar på den gamla servern.
1. [Lägg till den gamla vCenter-servern](#add-vmware-server-to-the-vault) och dess IP-adress på konfigurationsservern.
1. [Aktivera replikering](vmware-azure-tutorial.md#enable-replication) för de virtuella datorer som finns kvar på den gamla servern.

## <a name="next-steps"></a>Nästa steg

Om du har några problem läser [du Felsöka identifieringsfel för vCenter Server](vmware-azure-troubleshoot-vcenter-discovery-failures.md).
