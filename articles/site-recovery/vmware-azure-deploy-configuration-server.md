---
title: Distribuera konfigurationsservern för VMware-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du distribuerar en konfigurationsserver för VMware-haveriberedskap med Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2019
ms.author: ramamill
ms.openlocfilehash: c25ca8c27b84f34b025ec5abce00c8d8c70e5df6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125703"
---
# <a name="deploy-a-configuration-server"></a>Distribuera en konfigurationsserver

Du distribuerar en lokal konfigurationsserver när du använder [Azure Site Recovery](site-recovery-overview.md) för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure. Den configuration server koordinater kommunikationen mellan lokala VMware- och Azure. Den hanterar också replikering av data. Den här artikeln vägleder dig igenom de steg som krävs för att distribuera configuration server när du replikerar virtuella VMware-datorer till Azure. [Följ den här artikeln](physical-azure-set-up-source.md) om du vill ställa in en konfigurationsserver för fysisk serverreplikering.

> [!TIP]
> Du kan lära dig om rollen för konfigurationsservern som en del av Azure Site Recovery-arkitekturen [här](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Distribution av konfigurationsservern med OVA-mallen

Konfigurationsservern måste ställas in som en högtillgänglig VMware VM med vissa minimikraven på maskinvara och storlekskraven. Distribution av praktiskt och enkelt kan tillhandahåller Site Recovery en nedladdningsbar OVA (Open Virtualization program)-mall för att konfigurera konfigurationsservern som uppfyller alla föreskrivet krav som anges nedan.

## <a name="prerequisites"></a>Nödvändiga komponenter

Maskinvarukraven för en konfigurationsserver sammanfattas i tabellen nedan.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Krav för Azure Active Directory-behörighet

Du behöver en användare med **något av följande** behörighet i AAD (Azure Active Directory) för att kunna registrera konfigurationsservern med Azure Site Recovery-tjänster.

1. Användare ska ha ”programutvecklare” roll för att skapa program.
   1. Om du vill kontrollera, logga in på Azure-portalen</br>
   1. Gå till Azure Active Directory > roller och administratörer</br>
   1. Kontrollera om ”programutvecklare” rollen tilldelas användaren. Om den inte använda en användare med den här behörigheten, eller kontakta [administratör för att ge behörigheten](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
1. Om inte kan tilldelas rollen ”programutvecklare”, se till att ”användare kan registrera program” flaggan anges till true för användare att skapa identitet. Aktivera ovan behörigheter
   1. Logga in på Azure-portalen
   1. Gå till Azure Active Directory > användarinställningar
   1. Under ** appregistreringar ”,” användare kan registrera program ”ska väljas som” Ja ”.

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services(ADFS) är **stöds inte**. Använd ett konto som hanteras via [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="capacity-planning"></a>Kapacitetsplanering

Storlekskraven för konfigurationsservern är beroende av potentiella förändringstakten för data. Använd den här tabellen som vägledning.

| **CPU** | **Minne** | **Cachestorleken för disk** | **Dataändringshastigheten** | **Skyddade datorer** |
| --- | --- | --- | --- | --- |
| 8 virtuella processorer (2 platser * 4 kärnor \@ 2,5 GHz) |16 GB |300 GB |500 GB eller mindre |Replikera färre än 100 virtuella datorer. |
| 12 virtuella processorer (2 platser * 6 kärnor \@ 2,5 GHz) |18 GB |600 GB |500 GB till 1 TB |Replikera 100 150 datorer. |
| 16 virtuella processorer (2 platser * 8 kärnor \@ 2,5 GHz) |32 GB |1 TB |1 TB till 2 TB |Replikera 150 – 200 datorer. |

Om du replikerar mer än en VMware VM läsa [överväganden vid kapacitetsplanering](site-recovery-plan-capacity-vmware.md). Kör den [verktyget Distributionshanteraren](site-recovery-deployment-planner.md) för VMWare-replikering.

## <a name="download-the-template"></a>Ladda ned mallen

1. I valvet går du till **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Ladda ned mallen Open Virtualization program (OVA) för konfigurationsservern.

   > [!TIP]
   >Du kan också hämta den senaste versionen av konfigurationsservermallen direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Licensnumret du fått med OVA mall är en utvärderingslicens som är giltig i 180 dagar. Efter måste denna period kunden aktivera windows med en upphandlade licens.

## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med hjälp av VMWare vSphere-klienten.
2. I menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden Distribuera OVF-mall.

     ![OVF-mall](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. I **Välj källa**, ange platsen för den nedladdade OVF.
4. I **detaljer för recensionen**väljer **nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration**, accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**. Användning av tunn etableringsalternativ kan påverka prestandan av konfigurationsservern.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **Klart att slutföras**:

    * Om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **Power on after deployment** (Slå på strömmen efter distributionen) > **Slutför**.

    * Om du vill lägga till ytterligare ett nätverksgränssnitt, rensa **slå på strömmen efter distributionen**, och välj sedan **Slutför**. Konfigurationsservermallen distribueras som standard med ett enda nätverkskort. Du kan lägga till ytterligare nätverkskort efter distributionen.

> [!IMPORTANT]
> Ändra inte resurskonfigurationer (minne/kärnor/CPU-begränsning), ändra/ta bort installerade tjänster eller filer på konfigurationsservern efter distributionen. Detta påverkar registrering av konfigurationsservern med Azure-tjänster och prestanda för konfigurationsservern.

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
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration.</br>
    a. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.</br>
    b. Kontrollera att valda användarkontot har behörighet att skapa ett program i Azure. Om du vill aktivera behörigheter som krävs, följer du riktlinjerna [här](#azure-active-directory-permission-requirements).
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. I guiden Konfigurera serverhantering startar **automatiskt** i några sekunder.

### <a name="configure-settings"></a>Konfigurera inställningar

1. I guiden Konfigurera serverhantering, väljer **Ställ in anslutning**. Först väljer du det nätverkskort som inbyggd processervern som används för identifiering och push-installation av mobilitetstjänsten på källdatorer listrutor, och sedan väljer du det nätverkskort som konfigurationsservern använder för anslutning till Azure. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats. Det rekommenderas starkt att inte ändra IP-adressen för en konfigurationsserver. Se till att IP-Adressen som tilldelats till konfigurationsservern är statisk IP-adress och inte DHCP IP.
2. I **Välj Recovery Services-valv**, logga in på Microsoft Azure med autentiseringsuppgifter som används i **steg 6** av ”[registrera konfigurationsservern med Azure Site Recovery Services](#register-the-configuration-server-with-azure-site-recovery-services)” .
3. Efter inloggningen, väljer du din Azure-prenumeration samt relevant resursgrupp och valv.

    > [!NOTE]
    > När du registrerat, finns det ingen möjlighet att ändra recovery services-valvet.
    > Föränderliga recovery services-valv kräver disassociation av konfigurationsservern från aktuella valvet och replikering av alla skyddade virtuella datorer under configuration server har stoppats. Läs [mer](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. I **installera programvara från tredje part**,

    |Scenario   |Steg att följa  |
    |---------|---------|
    |Kan jag hämta och installera MySQL manuellt?     |  Ja. Ladda ned MySQL-program och placera den i mappen **C:\Temp\ASRSetup**, installerar manuellt. Nu när du godkänner licensvillkoren > Klicka på **ladda ned och installera**, portalen säger *redan installerat*. Du kan gå vidare till nästa steg.       |
    |Kan jag undvika att ladda ned MySQL online?     |   Ja. Placera din MySQL-installationsprogrammet i mappen **C:\Temp\ASRSetup**. Acceptera villkoren > Klicka på **ladda ned och installera**, portalen använder du lägger till installationsprogrammet och installerar programmet. Du kan fortsätta till nästa steg efter installationen.    |
    |Jag vill hämta och installera MySQL via Azure Site Recovery     |  Godkänn licensavtalet och klicka på **ladda ned och installera**. Du kan sedan fortsätta till nästa steg efter installationen.       |

5. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
6. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
7. Ange de autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **lägga till**, och sedan **fortsätta**. De autentiseringsuppgifter som anges här sparas lokalt.
8. I **konfigurera autentiseringsuppgifter för virtuell dator**, ange användarnamn och lösenord för virtuella datorer för att automatiskt installera Mobilitetstjänsten under replikeringen. För **Windows** datorer, kontot måste ha lokal administratörsbehörighet på de datorer som du vill replikera. För **Linux**, ange information för rotkontot.
9. Välj **Slutför konfigurationen** för att slutföra registreringen.
10. När registreringen är klar öppnar du Azure-portalen, kontrollerar du att konfigurationsservern och VMware-servern visas på **Recovery Services-valv** > **hantera**  >  **Site Recovery-infrastruktur** > **Konfigurationsservrar**.

## <a name="upgrade-the-configuration-server"></a>Uppgradera konfigurationsservern

Om du vill uppgradera konfigurationsservern till den senaste versionen, följer du dessa [steg](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Detaljerade anvisningar om hur du uppgraderar alla Site Recovery-komponenter, klickar du på [här](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Hantera konfigurationsservern

Se till att IP-adressen för konfigurationsservern inte ändras när konfigurationsservern har registrerats till ett valv för att undvika avbrott i pågående replikering. Du kan lära dig mer om vanliga hanteringsaktiviteter för configuration server [här](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

1. Hur lång tid är licensen som tillhandahålls på konfigurationsservern som distribueras via OVF är giltig? Vad händer om jag inte återaktivera licensen?

    Licensnumret du fått med OVA mall är en utvärderingslicens som är giltig i 180 dagar. Innan certifikatet går ut måste du aktivera licensen. Annars kan detta leda till frekventa avstängning av konfigurationsservern och därmed orsaka hinderance replikering aktiviteter.

2. Kan jag använda den virtuella datorn, där konfigurationsservern installeras för olika syften?

    **Inte**, rekommenderar vi att du använder den virtuella datorn för uteslutande av konfigurationsservern. Se till att du följer de specifikationer som nämns i [krav](#prerequisites) för effektiv hantering av katastrofåterställning.
3. Kan jag byta valvet redan registrerad på konfigurationsservern med ett nyligen skapade valv?

    **Inte**, när ett valv har registrerats med konfigurationsservern är den inte kan ändras.
4. Kan jag använda samma konfigurationsserver för att skydda både fysiska och virtuella datorer?

    **Ja**, samma konfigurationsserver kan användas för att replikera fysiska och virtuella datorer. Dock fysisk dator kan att växlas tillbaka endast till en VMware-VM.
5. Vad är syftet med en konfigurationsserver och var används den?

    Referera till [arkitektur för Azure-replikering för VMware till](vmware-azure-architecture.md) mer information om konfigurationsservern och dess funktioner.
6. Var hittar jag den senaste versionen av konfigurationsservern

    Anvisningar om att uppgradera konfigurationsservern via portalen finns i [uppgradera konfigurationsservern](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Mer instruktioner om hur du uppgraderar alla Site Recovery-komponenter [här](https://aka.ms/asr_how_to_upgrade).
7. Var kan jag hämta lösenfrasen för konfigurationsservern?

    Referera till [i den här artikeln](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) att hämta lösenfrasen.
8. Kan jag ändra lösenfrasen?

    **Inte**, du är **du inte ändra lösenfrasen** av konfigurationsservern. Ändring i lösenfras bryter replikering av skyddade datorer och leder till kritiskt hälsotillstånd.
9. Var kan jag hämta valv registreringsnycklar?

    I den **Recovery Services-valv**, **hantera** > **Site Recovery-infrastruktur** > **Konfigurationsservrar**. Välj i servrar, **ladda ned Registreringsnyckeln** att hämta valvautentiseringsfilen.
10. Kan jag klona en befintlig Configuration Server och använda det för dirigering av replikering?

    **Inte**, användning av klonade konfigurationsservern komponent stöds inte. Kloning av skalbar processerver är också ett scenario som inte stöds. Kloning av Site Recovery påverka komponenter pågående replikering.

11. Kan jag ändra IP-Adressen för konfigurationsservern?

    **Inte**, rekommenderas att inte ändra IP-adressen för en konfigurationsserver. Se till att alla IP-adresser tilldelade till konfigurationsservern är STATISKA IP-adresser och inte DHCP IP-adresser.
12. Kan jag konfigurera konfigurationsservern på Azure?

    Du rekommenderas att ställa in konfigurationsservern på en lokal miljö med direct rad med för att se med v Center- och minimera svarstider för överföring av data. Du kan skapa schemalagda säkerhetskopieringar av konfigurationsservern för [återställning efter fel syften](vmware-azure-manage-configuration-server.md#failback-requirements).

Fler vanliga frågor och svar på konfigurationsservern, finns i vår [dokumentation om vanliga frågor om configuration server](vmware-azure-common-questions.md#configuration-server) .

## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Nästa steg

Konfigurera haveriberedskap för [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.
