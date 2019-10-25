---
title: Distribuera konfigurations servern för VMware haveri beredskap med Azure Site Recovery | Microsoft Docs
description: I den här artikeln beskrivs hur du distribuerar en konfigurations Server för katastrof återställning i VMware med Azure Site Recovery
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: f5fe49130742d116775b75f17c726b56150c574f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792354"
---
# <a name="deploy-a-configuration-server"></a>Distribuera en konfigurationsserver

Du distribuerar en lokal konfigurations server när du använder [Azure Site Recovery](site-recovery-overview.md) för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure. Konfigurations servern samordnar kommunikationen mellan lokala VMware och Azure. Den hanterar också datareplikering. Den här artikeln vägleder dig genom de steg som krävs för att distribuera konfigurations servern när du replikerar virtuella VMware-datorer till Azure. [Följ den här artikeln](physical-azure-set-up-source.md) om du behöver konfigurera en konfigurations Server för fysisk server-replikering.

> [!TIP]
> Du kan lära dig mer om rollen konfigurations server som en del av Azure Site Recovery arkitekturen [här](vmware-azure-architecture.md).

## <a name="deployment-of-configuration-server-through-ova-template"></a>Distribution av konfigurations servern via en ägg-mall

Konfigurations servern måste vara konfigurerad som en virtuell VMware-dator med hög tillgänglighet och vissa minimi krav för maskin vara och storlek. För bekväm och enkel distribution tillhandahåller Site Recovery en fil med hämtnings bara filer (öppen virtualiseringsprogramvara) för att konfigurera konfigurations servern som uppfyller alla krav som anges nedan.

## <a name="prerequisites"></a>Krav

De minsta maskin varu kraven för en konfigurations Server sammanfattas i följande avsnitt.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Krav för Azure Active Directory behörighet

Du behöver en användare med **någon av följande** behörigheter som anges i AAD (Azure Active Directory) för att registrera konfigurations servern med Azure Site Recovery Services.

1. Användaren ska ha rollen Application Developer för att skapa program.
    - För att verifiera loggar du in på Azure Portal</br>
    - Navigera till Azure Active Directory > roller och administratörer</br>
    - Kontrol lera att användaren har tilldelats rollen Application Developer. Annars kan du använda en användare med den här behörigheten eller kontakta [administratören för att aktivera behörigheten](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles).
    
2. Om rollen "programutvecklare" inte kan tilldelas kontrollerar du att flaggan användare kan registrera program har angetts som true för att användaren ska kunna skapa identiteten. Om du vill aktivera ovanstående behörigheter
    - Logga in på Azure Portal
    - Navigera till Azure Active Directory > användar inställningar
    - Under * * Appregistreringar "," användare kan registrera program "ska väljas som" Ja ".

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory Federation Services (AD FS) (ADFS) **stöds inte**. Använd ett konto som hanteras via [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

## <a name="download-the-template"></a>Ladda ned mallen

1. I valvet går du till **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Hämta mallen för att öppna virtualiseringsvärdservrar (ägg) för konfigurations servern.

   > [!TIP]
   >Du kan också hämta den senaste versionen av konfigurations Server mal len direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

> [!NOTE]
> Den licens som tillhandahölls med en artikelmall-mall är en utvärderings licens som är giltig i 180 dagar. Publicera den här perioden måste kunden aktivera Windows med en tillskaffad licens.

## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med hjälp av VMWare vSphere-klienten.
2. I menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden Distribuera OVF-mall.

     ![OVF-mall](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. I **Välj källa**anger du platsen för den nedladdade OVF.
4. I **Granska information**väljer du **Nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration**accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**. Användningen av tunt etablerings alternativ kan påverka prestanda för konfigurations servern.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **Klart att slutföras**:

    * Om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **Power on after deployment** (Slå på strömmen efter distributionen) > **Slutför**.

    * Om du vill lägga till ytterligare ett nätverks gränssnitt avmarkerar du **Energis par efter distributionen**och väljer sedan **Slutför**. Konfigurationsservermallen distribueras som standard med ett enda nätverkskort. Du kan lägga till ytterligare nätverkskort efter distributionen.

> [!IMPORTANT]
> Ändra inte resursfiler (minne/kärnor/processor begränsning), ändra/ta bort installerade tjänster eller filer på konfigurations servern efter distributionen. Detta kommer att påverka registreringen av konfigurations servern med Azure-tjänster och prestanda för konfigurations servern.

## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

> [!NOTE]
> Två nätverkskort krävs om du planerar att behålla IP-adresserna för käll datorerna på redundansväxling och vill återställa efter fel till lokala platser senare. Ett nätverkskort kommer att anslutas till käll datorer och det andra NÄTVERKSKORTet kommer att användas för Azure-anslutning.

Om du vill lägga till ett extra nätverkskort i konfigurations servern lägger du till det innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** väljer du **Lägg till** > **Ethernet-kort**. Välj sedan **Nästa**.
3. Välj en adaptertyp och ett nätverk.
4. Om du vill ansluta det virtuella NÄTVERKSKORTet när den virtuella datorn är aktive rad väljer du **Anslut vid start**. Välj **nästa** > **Slutför** > **OK**.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Registrera konfigurations servern med Azure Site Recovery Services

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Första gången du loggar in, inom några sekunder, startas konfigurations verktyget för Azure Site Recovery.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in på din Azure-prenumeration.</br>
    a. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.</br>
    b. Se till att det valda användar kontot har behörighet att skapa ett program i Azure. Om du vill aktivera nödvändiga behörigheter följer du rikt linjerna [här](#azure-active-directory-permission-requirements).
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Guiden konfigurations Server hantering startar **automatiskt** om några sekunder.

### <a name="configure-settings"></a>Konfigurera inställningar

1. I guiden konfigurations Server hantering väljer du **Konfigurera anslutning**. I list rutorna väljer du först det nätverkskort som den inbyggda processervern använder för identifiering och push-installation av mobilitets tjänsten på käll datorer. Välj sedan det nätverkskort som konfigurations servern använder för anslutning med Azure. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har kon figurer ATS. Vi rekommenderar starkt att du inte ändrar IP-adressen för en konfigurations Server. Se till att den IP-adress som tilldelats konfigurations servern är statisk IP och inte DHCP IP.
2. Logga in på Microsoft Azure med autentiseringsuppgifter som användes i **steg 6** av "[Registrera konfigurations server med Azure Site Recovery tjänster](#register-the-configuration-server-with-azure-site-recovery-services)" i **Välj Recovery Services valv**.
3. När du har loggat in väljer du din Azure-prenumeration och relevant resurs grupp och valv.

    > [!NOTE]
    > När det är registrerat finns det ingen flexibilitet att ändra Recovery Services-valvet.
    > Att ändra Recovery Services-valvet kräver att konfigurations servern kopplas från det aktuella valvet, och replikeringen av alla skyddade virtuella datorer under konfigurations servern stoppas. Läs [mer](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

4. I **installera program vara från tredje part**,

    |Scenario   |Steg att följa  |
    |---------|---------|
    |Kan jag hämta & Installera MySQL manuellt?     |  Ja. Hämta MySQL-programmet & Placera det i mappen **C:\Temp\ASRSetup**och installera manuellt. När du nu accepterar villkoren > klickar på **Hämta och installera**, står det *redan installerat*på portalen. Du kan fortsätta till nästa steg.       |
    |Kan jag undvika att hämta MySQL online?     |   Ja. Placera ditt MySQL installations program i mappen **C:\Temp\ASRSetup**. Godkänn villkoren > Klicka på **Ladda ned och installera**. portalen använder installations programmet som lagts till av dig och installerar programmet. Du kan fortsätta till nästa steg efter installationen.    |
    |Jag vill ladda ned & Installera MySQL via Azure Site Recovery     |  Godkänn licens avtalet & Klicka på **Ladda ned och installera**. Sedan kan du fortsätta till nästa steg efter installationen.       |

5. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
6. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
7. Ange de autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **Lägg till**och **Fortsätt**sedan. De autentiseringsuppgifter som anges här sparas lokalt.
8. I **Konfigurera autentiseringsuppgifter för virtuell dator**anger du användar namn och lösen ord för virtuella datorer för att automatiskt installera mobilitets tjänsten under replikeringen. För **Windows** -datorer måste kontot ha lokal administratörs behörighet på de datorer som du vill replikera. För **Linux**anger du information om rot kontot.
9. Välj **Slutför konfigurationen** för att slutföra registreringen.
10. När registreringen är klar öppnar du Azure Portal, verifierar att konfigurations servern och VMware-servern visas på **Recovery Services valv** > **Hantera** > **Site Recovery infrastruktur** > -**konfiguration Servrar**.

## <a name="upgrade-the-configuration-server"></a>Uppgradera konfigurations servern

Följ dessa [steg](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)om du vill uppgradera konfigurations servern till den senaste versionen. Detaljerade anvisningar om hur du uppgraderar alla Site Recovery-komponenter finns i [hantering av tjänst uppdateringar](service-updates-how-to.md).

## <a name="manage-the-configuration-server"></a>Hantera konfigurationsservern

För att undvika avbrott i kontinuerlig replikering, kontrol lera att konfigurations serverns IP-adress inte ändras efter att konfigurations servern har registrerats i ett valv. Du kan läsa mer om vanliga konfigurations Server hanterings uppgifter [här](vmware-azure-manage-configuration-server.md).

## <a name="faq"></a>FAQ

1. Hur länge är licensen på konfigurations servern som distribueras via OVF giltig? Vad händer om jag inte återaktiverar licensen?

    Den licens som tillhandahölls med en artikelmall-mall är en utvärderings licens som är giltig i 180 dagar. Innan du går ut måste du aktivera licensen. Annars kan det leda till frekvent avstängning av konfigurations servern och därmed orsaka hinder för replikering av aktiviteter. Mer information finns i artikeln om att [Hantera konfigurations Server licens](vmware-azure-manage-configuration-server.md#update-windows-license).

2. Kan jag använda den virtuella datorn, där konfigurations servern är installerad, för olika syfte?

    **Nej**, vi rekommenderar att du använder den virtuella datorn för konfigurations serverns enda syfte. Se till att du följer alla specifikationer som anges i [krav](#prerequisites) för effektiv hantering av haveri beredskap.
3. Kan jag byta valvet som redan är registrerat på konfigurations servern med ett nyligen skapat valv?

    **Nej**, när ett valv har registrerats med konfigurations servern, kan det inte ändras.
4. Kan jag använda samma konfigurations Server för att skydda både fysiska och virtuella datorer?

    **Ja**, samma konfigurations Server kan användas för att replikera fysiska och virtuella datorer. Det går dock bara att återställa fysiska datorer till en virtuell VMware-dator.
5. Vad är syftet med en konfigurations Server och var används den?

    Se [VMware till Azure-replikering](vmware-azure-architecture.md) för att lära dig mer om konfigurations servern och dess funktioner.
6. Var hittar jag den senaste versionen av konfigurations servern?

    Anvisningar för hur du uppgraderar konfigurations servern via portalen finns i [Uppgradera konfigurations servern](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). Detaljerade anvisningar om hur du uppgraderar alla Site Recovery-komponenter finns [här](https://aka.ms/asr_how_to_upgrade).
7. Var kan jag hämta lösen frasen för konfigurations servern?

    Läs den [här artikeln](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) för att ladda ned lösen frasen.
8. Kan jag ändra lösen frasen?

    **Nej**, du **rekommenderar starkt att du inte ändrar lösen frasen** för konfigurations servern. Ändring i lösen frasen bryter replikeringen av skyddade datorer och leder till kritiskt hälso tillstånd.
9. Var kan jag hämta valv registrerings nycklar?

    I **Recovery Services-valvet** **hanterar**du  > **Site Recovery infrastruktur** > **konfigurations servrar**. I servrar väljer du **Ladda ned registrerings nyckel** för att ladda ned valv filen med autentiseringsuppgifter.
10. Kan jag klona en befintlig konfigurations Server och använda den för att dirigera om replikeringen?

    **Nej**, det finns inte stöd för att använda en komponent för klonad konfigurations Server. Det är också ett scenario som inte stöds för att klona en skalbar processerver. Kloning av Site Recovery-komponenter påverkar pågående replikeringar.

11. Kan jag ändra IP-adressen för konfigurations servern?

    **Nej**, vi rekommenderar starkt att inte ändra IP-adressen för en konfigurations Server. Se till att alla IP-adresser som tilldelats konfigurations servern är statiska IP-adresser och inte DHCP IP-adresser.
12. Kan jag konfigurera konfigurations servern på Azure?

    Vi rekommenderar att du konfigurerar konfigurations servern på en lokal miljö med direkt detaljerad information med v-Center och minimerar data överförings fördröjningen. Du kan göra schemalagda säkerhets kopieringar av konfigurations servern i [fel syfte](vmware-azure-manage-configuration-server.md#failback-requirements).

Fler vanliga frågor om konfigurations servrar finns i vår [dokumentation om vanliga frågor och svar om konfigurations servern](vmware-azure-common-questions.md#configuration-server) .

## <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>Nästa steg

Konfigurera katastrof återställning av [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.
