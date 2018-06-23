---
title: Distribuera konfigurationsservern för VMware katastrofåterställning med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du distribuerar en konfigurationsserver för VMware katastrofåterställning med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/06/2018
ms.author: raynew
ms.openlocfilehash: 841176d8c5f215d18edf25b1f191792b37555fa9
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318127"
---
# <a name="deploy-a-configuration-server"></a>Distribuera en konfigurationsserver

Du distribuerar ett lokalt konfigurationsservern när du använder [Azure Site Recovery](site-recovery-overview.md) för katastrofåterställning av virtuella VMware-datorer och fysiska servrar till Azure. Konfiguration av servern koordinater kommunikation mellan lokal VMware och Azure. Den hanterar också datareplikering. Den här artikeln vägleder dig igenom de steg som krävs för att distribuera konfigurationsservern när du replikera virtuella VMware-datorer till Azure. [Följ den här artikeln](physical-azure-set-up-source.md) om du behöver konfigurera en konfigurationsserver för fysisk serverreplikering.

>[!TIP]
Du kan lära dig om rollen konfigurationsservern som en del av Azure Site Recovery-arkitekturen [här](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Distribution av konfigurationsservern med ägg mall

Konfigurationsservern måste konfigureras som en högtillgänglig VMware VM vissa minimikraven på maskinvara och krav för storleksändring. Distribution av praktiskt och enkelt kan tillhandahåller Site Recovery en nedladdningsbar ägg (Open Virtualization program)-mall för att ställa in konfigurationsservern som uppfyller alla de behöriga krav som anges nedan.

## <a name="prerequisites"></a>Förutsättningar

Maskinvarukraven för en server configuration sammanfattas i följande tabell.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>Kapacitetsplanering

Storleksändring kraven för konfigurationsservern är beroende av potentiella förändringstakten för data. Använd den här tabellen som en vägledning.

| **CPU** | **Minne** | **Cachestorleken för disk** | **Dataändringshastighet** | **Skyddade datorer** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 sockets * 4 kärnor @ 2,5 GHz) |16 GB |300 GB |500 GB eller mindre |Replikera färre än 100 datorer. |
| 12 vCPUs (2 sockets * @ 2,5 GHz-6 kärnor) |18 GB |600 GB |500 GB till 1 TB |Replikera 100 150 datorer. |
| 16 vCPUs (2 sockets * 8 kärnor @ 2,5 GHz) |32 GB |1 TB |1 TB till 2 TB |Replikera 150 200 datorer. |

Om du replikerar mer än en VMware VM läsa [kapacitetsplaneringsöverväganden](/site-recovery-plan-capacity-vmware.md). Kör den [planner distributionsverktyget](site-recovery-deployment-planner.md) för VMWare-replikering.

## <a name="download-the-template"></a>Hämta en mall

1. I valvet går du till **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Hämta mallen Open Virtualization program (ägg) för konfigurationsservern.

  > [!TIP]
>Du kan också hämta den senaste versionen av configuration server-mallen direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

>[!NOTE]
Licensen som medföljer ägg mallen är en licens för utvärdering som är giltig i 180 dagar. Efter måste denna tid kunden aktivera windows med upphandlade licens.

## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med hjälp av VMWare vSphere-klienten.
2. I menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden Distribuera OVF-mall.

     ![OVF-mall](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. I **Välj källa**, ange platsen för den hämtade OVF.
4. I **information**väljer **nästa**.
5. I **Välj namn och mappen** och **Select configuration**, accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **Klart att slutföras**:

    * Om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **Power on after deployment** (Slå på strömmen efter distributionen) > **Slutför**.

    * Om du vill lägga till ytterligare ett nätverksgränssnitt, rensa **slå på strömmen efter distributionen**, och välj sedan **Slutför**. Konfigurationsservermallen distribueras som standard med ett enda nätverkskort. Du kan lägga till ytterligare nätverkskort efter distributionen.

## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till en extra NIC på konfigurationsservern lägger du till den innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** väljer du **Lägg till** > **Ethernet-kort**. Välj sedan **Nästa**.
3. Välj en adaptertyp och ett nätverk. 
4. Om du vill ansluta det virtuella nätverkskortet när den virtuella datorn är påslagen, Välj **Anslut på ström på**. Välj sedan **nästa** > **Slutför** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrera konfigurationsservern med Azure Site Recovery services

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Första gången du loggar in, inom några sekunder konfigurationsverktyget för Azure Site Recovery startar.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in på din Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Startar guiden Konfigurera server management **automatiskt** i några sekunder.

### <a name="configure-settings"></a>Konfigurera inställningar

1. I konfigurationsguiden för serverhantering väljer du **Ställ in anslutning** och väljer sedan det nätverkskort som processervern använder för att ta emot replikeringstrafik från virtuella datorer. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Recovery Services-valvet**, logga in på Microsoft Azure, väljer din Azure-prenumeration och relevant resursgrupp och valvet.
    >[!NOTE]
    > När registreringen är klar finns det ingen möjlighet att ändra recovery services-valvet.
3. I **installera programvara från tredje part**,

    |Scenario   |Steg att följa  |
    |---------|---------|
    |Kan jag hämta och installera MySQL manuellt?     |  Ja. Hämta MySQL program & Placera den i mappen **C:\Temp\ASRSetup**, installerar manuellt. Nu när du har accepterat villkoren > klickar du på **ladda ned och installera**, portalen står *installerad*. Du kan fortsätta till nästa steg.       |
    |Kan jag undvika hämtningen av MySQL online?     |   Ja. Placera din MySQL installer-program i mappen **C:\Temp\ASRSetup**. Acceptera villkoren > klickar du på **ladda ned och installera**, portalen använder lagts till av du installationsprogrammet och installerar programmet. Du kan fortsätta till nästa steg efter installationen.    |
    |Jag vill hämta och installera MySQL via Azure Site Recovery     |  Godkänn licensavtalet och klicka på **ladda ned och installera**. Du kan sedan fortsätta till nästa steg efter installationen.       |
4. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
5. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
6. Ange de autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **lägga till**, och sedan **fortsätta**. De autentiseringsuppgifter som anges här sparas lokalt.
7. I **Konfigurera virtuella autentiseringsuppgifter**, ange användarnamn och lösenord för virtuella datorer för att installera Mobilitetstjänsten automatiskt vid replikering. För **Windows** datorer, måste kontot lokal administratörsbehörighet på de datorer som du vill replikera. För **Linux**, ange information för rotkontot.
8. Välj **Slutför konfigurationen** för att slutföra registreringen.
9. När registreringen är klar öppnar du Azure-portalen, kontrollera att konfigurationsservern och VMware-server visas på **Återställningstjänstvalvet** > **hantera**  >  **Site Recovery-infrastruktur** > **Konfigurationsservrar**.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

1. Kan jag använda den virtuella datorn där konfigurationsservern är installerad för olika ändamål? **Inte**konfigurationsservern måste vara en enda syfte server och använder den som en delad server stöds inte.
2. Kan jag byta valvet som redan har registrerats i konfigurationsservern med ett nyskapat valv? **Inte**, kan inte ändras när ett valv registreras med konfigurationsservern.
3. Kan jag använda samma konfigurationsserver för att skydda både fysiska och virtuella datorer? **Ja**, samma konfigurationsservern kan användas för att replikera fysiska och virtuella datorer. Dock stöds återställning till en fysisk dator inte.
4. Där konfigurationsservern används? Referera till vår Azure Site Recovery-arkitekturen [här](vmware-azure-architecture.md) lära dig mer om konfigurationsservern och dess funktioner.
5. Var hittar den senaste versionen av konfigurationsservern? Du kan hämta det från direkt [Microsoft Download Center](https://aka.ms/asrconfigurationserver). Information finns i artikeln på stegen för att uppgradera konfigurationsservern [här](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).

## <a name="upgrade-the-configuration-server"></a>Uppgradera konfigurationsservern

Läs anvisningarna för att uppgradera konfigurationsservern till den senaste versionen, [här](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)

## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Nästa steg

Ställ in återställning av [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.
