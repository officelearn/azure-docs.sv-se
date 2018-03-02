---
title: "Distribuera konfigurationsservern för VMware katastrofåterställning med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du distribuerar en konfigurationsserver för VMware katastrofåterställning med Azure Site Recovery"
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2018
ms.author: anoopkv
ms.openlocfilehash: f7ec02cb4723d8a4ea0847810b1a1822cf20e957
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="deploy-a-configuration-server"></a>Distribuera en konfigurationsserver

Du distribuerar ett lokalt konfigurationsservern när du använder [Azure Site Recovery](site-recovery-overview.md) för katastrofåterställning av virtuella VMware-datorer och fysiska servrar till Azure. Konfiguration av servern koordinater kommunikation mellan lokal VMware och Azure. Den hanterar också datareplikering. Den här artikeln vägleder dig igenom de steg som krävs för att distribuera konfigurationsservern när du replikera virtuella VMware-datorer till Azure. [Följ den här artikeln](site-recovery-set-up-physical-to-azure.md) om du behöver konfigurera en konfigurationsserver för fysisk serverreplikering.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du distribuerar konfigurationsservern som en högtillgänglig VMware VM. Minsta maskinvarukrav sammanfattas i följande tabell.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>Kapacitetsplanering

Storleksändring kraven för konfigurationsservern är beroende av potentiella förändringstakten för data. Använd den här tabellen som en vägledning.

| **CPU** | **Minne** | **Cachestorleken för disk** | **Dataändringshastighet** | **Skyddade datorer** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 sockets * 4 kärnor @ 2,5 GHz) |16 GB |300 GB |500 GB eller mindre |Replikera färre än 100 datorer. |
| 12 vCPUs (2 sockets * @ 2,5 GHz-6 kärnor) |18 GB |600 GB |500 GB till 1 TB |Replikera 100 150 datorer. |
| 16 vCPUs (2 sockets * 8 kärnor @ 2,5 GHz) |32 GB |1 TB |1 TB till 2 TB |Replikera 150 200 datorer. |


Om du replikerar virtuella VMware-datorer, Läs mer om [kapacitetsplaneringsöverväganden](/site-recovery-plan-capacity-vmware.md). Kör den [planner distributionsverktyget](site-recovery-deployment-planner.md) för VMWare-replikering.



## <a name="download-the-template"></a>Hämta en mall

Site Recovery tillhandahåller en nedladdningsbar mall för att ställa in konfigurationsservern som en högtillgänglig VMware VM. 

1. I valvet går du till **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa**väljer **+ konfigurationsservern**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Ladda ner OVF-mallen (Open Virtualization Format) för konfigurationsservern.

  > [!TIP]
  Du kan hämta den senaste versionen av configuration server-mallen direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware


1. Logga in på VMware vCenter-servern eller vSphere ESXi-värd med VMWare vSphere klienten.
2. På den **filen** väljer du **distribuera OVF mallen** att starta guiden Distribuera OVF-mall.

     ![OVF-mall](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. I **Välj källa**, ange platsen för den hämtade OVF.
4. I **information**väljer **nästa**.
5. I **Välj namn och mappen** och **Select configuration**, accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
4. Acceptera standardinställningarna på resten av sidorna i guiden.
5. I **Klart att slutföras**:

    * Om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **Power on after deployment** (Slå på strömmen efter distributionen) > **Slutför**.

    * Om du vill lägga till ytterligare ett nätverksgränssnitt, rensa **slå på strömmen efter distributionen**, och välj sedan **Slutför**. Som standard distribueras configuration server-mall med ett enda nätverkskort. Du kan lägga till ytterligare nätverkskort efter distributionen.


## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till en extra NIC på konfigurationsservern lägger du till den innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **maskinvara**väljer **Lägg till** > **Ethernet-nätverkskort**. Välj sedan **Nästa**.
3. Välj en typ av nätverkskort och ett nätverk. 
4. Om du vill ansluta det virtuella nätverkskortet när den virtuella datorn är påslagen väljer du **Connect at power on** (Anslut när strömmen är på). Välj sedan **nästa** > **Slutför** > **OK**.
 

## <a name="register-the-configuration-server"></a>Registrera konfigurationsservern 

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar, logga in den virtuella datorn som administratör.
4. Första gången du loggar in, startar för Azure Site Recovery-konfigurationsverktyget.
5. Ange ett namn som används för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats, Välj **inloggning** att logga in på Azure-prenumerationen. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Guiden Konfigurera server management startas automatiskt.

### <a name="configure-settings"></a>Konfigurera inställningar

1. Välj i guiden Konfigurera server management **Konfigurera anslutningen**. Markera nätverkskortet att ta emot replikeringstrafik och välj sedan **spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Recovery Services-valvet**din Azure-prenumeration och välj och relevant resursgrupp valvet.
3. I **installera programvara från tredje part**, Godkänn licensavtalet. Välj **ladda ned och installera** installera MySQL-Server.
4. Välj **installera VMware PowerLCI**. Kontrollera att alla fönster stängs innan du gör det här steget. Välj sedan **Fortsätt**.
5. I **verifiera installation configuration**, nödvändiga komponenter har verifierats innan du fortsätter.
6. I **konfigurera vCenter Server/vSphere ESXi-servern**, anger du FQDN eller IP-adressen för vCenter-servern eller vSphere-värd, där de virtuella datorerna som du vill replikera finns. Ange porten som avlyssnas av servern och ett eget namn för VMware-servern i valvet.
7. Ange autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **Lägg till**, och välj sedan **Fortsätt**.
8. I **Konfigurera virtuella autentiseringsuppgifter**, ange användarnamn och lösenord som används för att automatiskt installera Azure Site Recovery Mobility-tjänsten på datorer när replikeringen är aktiverad. För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera. För Linux anger du information för rotkontot.
9. Välj **Slutför konfigurationen** att slutföra registreringen. 
10. När registreringen är klar i Azure-portalen, kontrollerar du att konfigurationsservern och VMware-servern visas på sidan **Källa** i valvet. Välj sedan **OK** att konfigurera inställningar för target.


## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Nästa steg

Ställ in återställning av [virtuella VMware-datorer](tutorial-vmware-to-azure.md) till Azure.
