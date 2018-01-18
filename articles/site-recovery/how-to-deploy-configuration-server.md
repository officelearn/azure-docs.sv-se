---
title: " Distribuera konfigurationsservern för VMware katastrofåterställning med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du distribuerar en konfigurationsserver för VMware katastrofåterställning med Azure Site Recovery"
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2018
ms.author: anoopkv
ms.openlocfilehash: e257ede08ac46ad863b4883b10399058e6f59f1f
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="deploy-a-configuration-server"></a>Distribuera en konfigurationsserver

Du distribuerar ett lokalt konfigurationsservern när du använder den [Azure Site Recovery](site-recovery-overview.md) tjänsten för katastrofåterställning av virtuella VMware-datorer och fysiska servrar till Azure. WThe konfigurationsservern samordnar kommunikationen mellan lokala VMware och Azure och hanterar datareplikering. Den här artikeln vägleder dig igenom de steg som krävs för att distribuera konfigurationsservern.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du distribuerar konfigurationsservern som en högtillgänglig VMware VM. För fysisk serverreplikering kan konfigurationsservern ställas in på en fysisk dator. Minsta maskinvarukrav sammanfattas i följande tabell.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]




## <a name="capacity-planning"></a>Kapacitetsplanering

Storleksändring kraven för konfigurationsservern är beroende av potentiella förändringstakten för data. Använd den här tabellen som en vägledning.

| **CPU** | **Minne** | **Cachestorleken för disk** | **Dataändringshastighet** | **Skyddade datorer** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 sockets * 4 kärnor @ 2,5 GHz) |16 GB |300 GB |500 GB eller mindre |Replikera färre än 100 datorer. |
| 12 vCPUs (2 sockets * @ 2,5 GHz-6 kärnor) |18 GB |600 GB |500 GB till 1 TB |Replikera mellan 100 150 datorer. |
| 16 vCPUs (2 sockets * 8 kärnor @ 2,5 GHz) |32 GB |1 TB |1 TB till 2 TB |Replikera mellan 150 200 datorer. |


Om du replikerar virtuella VMware-datorer, Läs mer om [kapacitetsplaneringsöverväganden](/site-recovery-plan-capacity-vmware.md), och kör den [planner distributionsverktyget](site-recovery-deployment-planner.md) för VMWare-replikering.



## <a name="download-the-template"></a>Hämta en mall

Site Recovery tillhandahåller en nedladdningsbar mall för att ställa in konfigurationsservern som en högtillgänglig VMware VM. 

1. I valvet, går du till **Förbered infrastrukturen** > **källa**.
2. I **Förbered källa**, klickar du på **+ konfigurationsservern**.
3. I **Lägg till Server**, kontrollera att **konfigurationsservern för VMware** visas i **servertyp**.
4. Hämta Open Virtualization Format OVF ()-mall för konfigurationsservern.

  > [!TIP]
  Den senaste versionen av configuration server-mall kan hämtas direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver)


## <a name="import-the-template-in-vmware"></a>Importera VMware-mallen


1. Logga in på VMware vCenter server eller vSphere ESXi värden med VMWare vSphere klienten.
2. På den **filen** väljer du **distribuera OVF mallen**, för att starta guiden Distribuera OVF-mall.  

     ![OVF-mall](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. I **Välj källa**, ange platsen för den hämtade OVF.
4. I **information**, klickar du på **nästa**.
5. I **Välj namn och mappen**, och **Select configuration**, accepterar du standardinställningarna.
6. I **Välj lagring**, för bästa prestanda väljer **tjock etablera gärna nollställs** i **väljer virtuella diskformatet**.
4. Acceptera standardinställningarna i resten av sidorna i guiden.
5. I **redo att slutföra**:
  - Om du vill konfigurera den virtuella datorn med standardinställningarna, Välj **slå på strömmen efter distributionen** > **Slutför**.
  - Ta bort om du vill lägga till ytterligare ett nätverksgränssnitt **slå på strömmen efter distributionen**, och välj sedan **Slutför**. Mallen configuration server distribueras med ett enda nätverkskort som standard, men du kan lägga till ytterligare nätverkskort efter distributionen.


## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till en extra NIC konfigurationsservern kan du göra det innan du registrerar servern i valvet. Att lägga till fler nätverkskort stöds inte efter registreringen.

1. I vSphere klienten lagret, högerklicka på den virtuella datorn och välj **redigera inställningar för**.
2. I **maskinvara**, klickar du på **Lägg till** > **Ethernet-nätverkskort**. Klicka sedan på **Nästa**.
3. Välj och typ av nätverkskort och ett nätverk. 
4. Om du vill ansluta det virtuella nätverkskortet när den virtuella datorn är påslagen, Välj **ansluta vid effekt på**. Klicka på **nästa** > **Slutför**, och klicka sedan på **OK**.
 

## <a name="register-the-configuration-server"></a>Registrera konfigurationsservern 

1. Aktivera den virtuella datorn från konsolen VMWare vSphere-klienten.
2. Den virtuella datorn startar i en Windows Server 2016-installationsproceduren. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar, inloggning till den virtuella datorn som administratör.
4. Startar för första gången du loggar in Azure Site Recovery-konfigurationsverktyget.
5. Ange ett namn som används för att registrera konfigurationsservern med Site Recovery. Klicka sedan på **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats, klickar du på **logga in**, för att logga in på Azure-prenumerationen. Autentiseringsuppgifterna måste ha åtkomst till valvet som du vill registrera konfigurationsservern.
7. Verktyget utför vissa konfigurationsåtgärder och startar om.
8. Logga in på datorn igen. Guiden Konfigurera server management startas automatiskt.

### <a name="configure-settings"></a>Konfigurera inställningar

1. I guiden Konfigurera server management > **Konfigurera anslutningen**, markera nätverkskortet som ska ta emot replikeringstrafik. Klicka sedan på **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Recovery Services-valvet**, Välj din Azure-prenumeration och relevant resursgrupp och valvet.
3. I **installera programvara från tredje part**accepterar licensen agreeemtn och klicka på **ladda ned och installera**, för att installera MySQL-servern.
4. Klicka på **installera VMware PowerLCI**. Kontrollera att alla fönster stängs innan du gör detta. Klicka på **Fortsätt**
5. I **verifiera installation configuration**, kommer att verifieras krav innan du fortsätter.
6. I **konfigurera vCenter Server/vSphere ESXi-servern**, anger du FQDN eller IP-adressen för vCenter-servern eller vSphere-värd som du vill replikera på vilka virtuella datorer är placerade. Ange den port som servern lyssnar och ett eget namn som ska användas för VMware-servern i valvet.
7. Ange autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Klicka på **Lägg till**, och klicka sedan på **Fortsätt**.
8. I **Konfigurera virtuella autentiseringsuppgifter**, ange användarnamn och lösenord som används för att automatiskt installera mobilitetstjänsten på datorer, om replikering har aktiverats. För Windows-datorer måste kontot lokal administratörsbehörighet på de datorer som du vill replikera. Ange information för Linux för rotkontot.
9. Klicka på **Slutför konfigurationen** och slutföra registreringen. 
10. När registreringen är klar i Azure-portalen, kontrollera att konfigurationsservern och VMware-server visas på den **källa** i valvet. Klicka på **OK** att konfigurera inställningar för target.


## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Nästa steg

Granska självstudier för att ställa in katastrofåterställning av [virtuella VMware-datorer](tutorial-vmware-to-azure.md) och [fysiska servrar](tutorial-physical-to-azure.md) till Azure.
