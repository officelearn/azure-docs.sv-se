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
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2018
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
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Ladda ner OVF-mallen (Open Virtualization Format) för konfigurationsservern.

  > [!TIP]
  Du kan hämta den senaste versionen av configuration server-mallen direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware


1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med hjälp av VMWare vSphere-klienten.
2. På menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden Distribuera OVF-mall.

     ![OVF-mall](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. I **Välj källa**, ange platsen för den hämtade OVF.
4. I **information**väljer **nästa**.
5. I **Välj namn och mappen** och **Select configuration**, accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
4. Acceptera standardinställningarna på resten av sidorna i guiden.
5. I **Klart att slutföras**:

    * Om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **Power on after deployment** (Slå på strömmen efter distributionen) > **Slutför**.

    * Om du vill lägga till ytterligare ett nätverksgränssnitt, rensa **slå på strömmen efter distributionen**, och välj sedan **Slutför**. Konfigurationsservermallen distribueras som standard med ett enda nätverkskort. Du kan lägga till ytterligare nätverkskort efter distributionen.


## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till en extra NIC på konfigurationsservern lägger du till den innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** väljer du **Lägg till** > **Ethernet-kort**. Välj sedan **Nästa**.
3. Välj en adaptertyp och ett nätverk. 
4. Om du vill ansluta det virtuella nätverkskortet när den virtuella datorn är påslagen väljer du **Connect at power on** (Anslut när strömmen är på). Välj sedan **nästa** > **Slutför** > **OK**.
 

## <a name="register-the-configuration-server"></a>Registrera konfigurationsservern 

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Första gången du loggar in startas Azure Site Recovery-konfigurationsverktyget.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt.

### <a name="configure-settings"></a>Konfigurera inställningar

1. Välj i guiden Konfigurera server management **Konfigurera anslutningen**. Markera nätverkskortet att ta emot replikeringstrafik och välj sedan **spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Recovery Services-valv** väljer du din Azure-prenumeration samt relevant resursgrupp och valv.
3. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server.
4. Välj **installera VMware PowerLCI**. Kontrollera att alla fönster stängs innan du gör det här steget. Välj sedan **Fortsätt**.
5. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
6. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som avlyssnas av servern och ett eget namn för VMware-servern i valvet.
7. Ange de autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **Lägg till** och välj sedan **Fortsätt**.
8. I **Konfigurera virtuella autentiseringsuppgifter**, ange användarnamn och lösenord som används för att automatiskt installera Azure Site Recovery Mobility-tjänsten på datorer när replikeringen är aktiverad. För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera. För Linux anger du information för rotkontot.
9. Välj **Slutför konfigurationen** att slutföra registreringen. 
10. När registreringen är klar i Azure-portalen, kontrollerar du att konfigurationsservern och VMware-servern visas på sidan **Källa** i valvet. Välj sedan **OK** för att konfigurera målinställningarna.


## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Nästa steg

Ställ in återställning av [virtuella VMware-datorer](tutorial-vmware-to-azure.md) till Azure.
