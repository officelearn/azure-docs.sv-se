---
title: Distribuera konfigurationsservern för VMware-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du distribuerar en konfigurationsserver för VMware-haveriberedskap med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 240f5270d083fa5f4742f3ed2cd61feee2b635ec
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38718965"
---
# <a name="deploy-a-configuration-server"></a>Distribuera en konfigurationsserver

Du distribuerar en lokal konfigurationsserver när du använder [Azure Site Recovery](site-recovery-overview.md) för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure. Den configuration server koordinater kommunikationen mellan lokala VMware- och Azure. Den hanterar också replikering av data. Den här artikeln vägleder dig igenom de steg som krävs för att distribuera configuration server när du replikerar virtuella VMware-datorer till Azure. [Följ den här artikeln](physical-azure-set-up-source.md) om du vill ställa in en konfigurationsserver för fysisk serverreplikering.

>[!TIP]
Du kan lära dig om rollen för konfigurationsservern som en del av Azure Site Recovery-arkitekturen [här](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Distribution av konfigurationsservern med OVA-mallen

Konfigurationsservern måste ställas in som en högtillgänglig VMware VM med vissa minimikraven på maskinvara och storlekskraven. Distribution av praktiskt och enkelt kan tillhandahåller Site Recovery en nedladdningsbar OVA (Open Virtualization program)-mall för att konfigurera konfigurationsservern som uppfyller alla föreskrivet krav som anges nedan.

## <a name="prerequisites"></a>Förutsättningar

Maskinvarukraven för en konfigurationsserver sammanfattas i tabellen nedan.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="capacity-planning"></a>Kapacitetsplanering

Storlekskraven för konfigurationsservern är beroende av potentiella förändringstakten för data. Använd den här tabellen som vägledning.

| **CPU** | **Minne** | **Cachestorleken för disk** | **Dataändringshastigheten** | **Skyddade datorer** |
| --- | --- | --- | --- | --- |
| 8 virtuella processorer (2 platser * 4 kärnor @ 2,5 GHz) |16 GB |300 GB |500 GB eller mindre |Replikera färre än 100 virtuella datorer. |
| 12 virtuella processorer (2 platser * @ 2,5 GHz-6 kärnor) |18 GB |600 GB |500 GB till 1 TB |Replikera 100 150 datorer. |
| 16 virtuella processorer (2 platser * 8 kärnor @ 2,5 GHz) |32 GB |1 TB |1 TB till 2 TB |Replikera 150 – 200 datorer. |

Om du replikerar mer än en VMware VM läsa [överväganden vid kapacitetsplanering](/site-recovery-plan-capacity-vmware.md). Kör den [verktyget Distributionshanteraren](site-recovery-deployment-planner.md) för VMWare-replikering.

## <a name="download-the-template"></a>Ladda ned mallen

1. I valvet går du till **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Ladda ned mallen Open Virtualization program (OVA) för konfigurationsservern.

  > [!TIP]
>Du kan också hämta den senaste versionen av konfigurationsservermallen direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

>[!NOTE]
Licensen som medföljer mallen för OVA är en licens för utvärdering som är giltig i 180 dagar. Efter måste denna period kunden aktivera windows med upphandlade licens.

## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med hjälp av VMWare vSphere-klienten.
2. I menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden Distribuera OVF-mall.

     ![OVF-mall](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. I **Välj källa**, ange platsen för den nedladdade OVF.
4. I **detaljer för recensionen**väljer **nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration**, accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **Klart att slutföras**:

    * Om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **Power on after deployment** (Slå på strömmen efter distributionen) > **Slutför**.

    * Om du vill lägga till ytterligare ett nätverksgränssnitt, rensa **slå på strömmen efter distributionen**, och välj sedan **Slutför**. Konfigurationsservermallen distribueras som standard med ett enda nätverkskort. Du kan lägga till ytterligare nätverkskort efter distributionen.

## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till ett extra nätverkskort i konfigurationsservern, lägger du till den innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** väljer du **Lägg till** > **Ethernet-kort**. Välj sedan **Nästa**.
3. Välj en adaptertyp och ett nätverk. 
4. Anslut det virtuella nätverkskortet när den virtuella datorn är påslagen, att välja **Connect på ström på**. Välj sedan **nästa** > **Slutför** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrera konfigurationsservern med Azure Site Recovery-tjänster

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Första gången du loggar in, inom några sekunder Azure Site Recovery-konfigurationsverktyget startar.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in på din Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. I guiden Konfigurera serverhantering startar **automatiskt** i några sekunder.

### <a name="configure-settings"></a>Konfigurera inställningar

1. I konfigurationsguiden för serverhantering väljer du **Ställ in anslutning** och väljer sedan det nätverkskort som processervern använder för att ta emot replikeringstrafik från virtuella datorer. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Recovery Services-valv**, logga in på Microsoft Azure, Välj din Azure-prenumeration samt relevant resursgrupp och valv.

    > [!NOTE]
    > När du registrerat, finns det ingen möjlighet att ändra recovery services-valvet.
    
3. I **installera programvara från tredje part**,

    |Scenario   |Steg att följa  |
    |---------|---------|
    |Kan jag hämta och installera MySQL manuellt?     |  Ja. Ladda ned MySQL-program och placera den i mappen **C:\Temp\ASRSetup**, installerar manuellt. Nu när du godkänner licensvillkoren > Klicka på **ladda ned och installera**, portalen säger *redan installerat*. Du kan gå vidare till nästa steg.       |
    |Kan jag undvika att ladda ned MySQL online?     |   Ja. Placera din MySQL-installationsprogrammet i mappen **C:\Temp\ASRSetup**. Acceptera villkoren > Klicka på **ladda ned och installera**, portalen använder du lägger till installationsprogrammet och installerar programmet. Du kan fortsätta till nästa steg efter installationen.    |
    |Jag vill hämta och installera MySQL via Azure Site Recovery     |  Godkänn licensavtalet och klicka på **ladda ned och installera**. Du kan sedan fortsätta till nästa steg efter installationen.       |
4. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
5. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
6. Ange de autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **lägga till**, och sedan **fortsätta**. De autentiseringsuppgifter som anges här sparas lokalt.
7. I **konfigurera autentiseringsuppgifter för virtuell dator**, ange användarnamn och lösenord för virtuella datorer för att automatiskt installera Mobilitetstjänsten under replikeringen. För **Windows** datorer, kontot måste ha lokal administratörsbehörighet på de datorer som du vill replikera. För **Linux**, ange information för rotkontot.
8. Välj **Slutför konfigurationen** för att slutföra registreringen.
9. När registreringen är klar öppnar du Azure-portalen, kontrollerar du att konfigurationsservern och VMware-servern visas på **Recovery Services-valv** > **hantera**  >  **Site Recovery-infrastruktur** > **Konfigurationsservrar**.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

1. Kan jag använda den virtuella datorn, där konfigurationsservern installeras för olika syften?

    **Inte**, rekommenderar vi att du använder den virtuella datorn för uteslutande av konfigurationsservern. Se till att följa de specifikationer som nämns i den [föregående avsnitt](vmware-azure-deploy-configuration-server.md#Prerequisites) för effektiv hantering av katastrofåterställning.
2. Kan jag byta valvet redan registrerad på konfigurationsservern med ett nyligen skapade valv?

    **Inte**, när ett valv har registrerats med konfigurationsservern är den inte kan ändras.
3. Kan jag använda samma konfigurationsserver för att skydda både fysiska och virtuella datorer?

    **Ja**, samma konfigurationsservern kan användas för att replikera fysiska och virtuella datorer. Dock fysisk dator kan att växlas tillbaka endast till en VMware-VM.
4. Vad är syftet med en konfigurationsserver och var används den?

    Referera till vår Azure Site Recovery-arkitektur [här](vmware-azure-architecture.md) mer information om konfigurationsservern och dess funktioner.
5. Var hittar jag den senaste versionen av konfigurationsservern

    Finns i artikeln på stegen för att uppgradera konfigurationsservern [via portalen](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Du kan också direkt ladda ned det från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
6. Var kan jag hämta lösenfrasen för konfigurationsservern?

    Referera till [i den här artikeln](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) att hämta lösenfrasen.
7. Var kan jag hämta valv registreringsnycklar?

    I den **Recovery Services-valv**, **hantera** > **Site Recovery-infrastruktur** > **Konfigurationsservrar**. Välj i servrar, **ladda ned Registreringsnyckeln** att hämta valvautentiseringsfilen.

## <a name="upgrade-the-configuration-server"></a>Uppgradera konfigurationsservern

Läs anvisningarna för att uppgradera konfigurationsservern till den senaste versionen, [här](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)

## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Nästa steg

Konfigurera haveriberedskap för [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.
