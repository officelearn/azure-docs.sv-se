---
title: Konfigurera den källa miljön och konfigurationen för kommunikationssession haveriberedskap för virtuella VMware-datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in din lokala miljö och konfigurationsservern för haveriberedskap för virtuella VMware-datorer till Azure med Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566320"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>Konfigurera miljön och konfigurationen för källservern

Den här artikeln beskriver hur du ställer in din källmiljö på plats för haveriberedskap för virtuella VMware-datorer till Azure med [Azure Site Recovery](site-recovery-overview.md). Den inkluderar steg för att konfigurera en lokal dator som konfigurationsserver för Site Recovery och automatiskt identifiera lokala virtuella datorer. 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du har gjort följande

- Planerat din disaster recovery distributionen med hjälp av den [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). Planeringsverktyget hjälper till att du har tillräckligt med resurser och bandbredd för disaster recovery och de mål för återställningspunkt (RPO) i din organisation.
- [Konfigurera Azure-resurser](tutorial-prepare-azure.md) i den [Azure-portalen](http://portal.azure.com).
- [Konfigurera en lokal VMware](vmware-azure-tutorial-prepare-on-premises.md) resurser, inklusive ett särskilt konto för automatisk identifiering av lokala virtuella VMware-datorer.
- Du kan [granska frågor](vmware-azure-common-questions.md#configuration-server) om configuration server-distribution och hantering.


## <a name="choose-protection-goals"></a>Välj skyddsmål

1. Välj valvets namn i **Recovery Services-valv**. 
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina datorer?** väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere Hypervisor**. Välj sedan **OK**.

## <a name="about-the-configuration-server"></a>Om configuration server

Du distribuerar en lokal konfigurationsserver när du konfigurerar haveriberedskap för virtuella VMware-datorer till Azure.

- Den configuration server koordinater kommunikationen mellan lokala VMware- och Azure. Den hanterar också replikering av data.
- Du kan distribuera konfigurationsservern som en VMware-VM.
- Site Recovery tillhandahåller en OVA-mall som du hämtar från Azure-portalen och importera till vCenter-servern och konfigurera konfigurationsservern VM.
- [Läs mer](vmware-azure-architecture.md) om konfigurationsserverns komponenter och processer.


>[!NOTE]
Använd inte de här instruktionerna om du distribuerar konfigurationsservern för haveriberedskap för lokala fysiska datorer till Azure. Det här scenariot kan du följa [i den här artikeln](physical-azure-set-up-source.md).


## <a name="before-you-deploy-the-configuration-server"></a>Innan du distribuerar configuration server

Om du installerar konfigurationsservern som en VMware VM med hjälp av OVA-mall måste kommer den virtuella datorn att installeras med alla nödvändiga komponenter. Använd följande artiklar om du vill gå igenom kraven.

- [Lär dig mer om](vmware-azure-configuration-server-requirements.md) maskinvara, programvara och kapacitetsbehov för konfigurationsservern.
- Om du replikerar flera virtuella VMware-datorer, bör du granska den [överväganden vid kapacitetsplanering](site-recovery-plan-capacity-vmware.md), och kör den [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) verktyget för VMWare-replikering.
- Windows-licens för OVA-mallen är en utvärderingslicens som är giltig i 180 dagar. Efter den tiden kan behöva du aktivera Windows med en giltig licens. 
- OVA-mallen innehåller ett enkelt sätt att konfigurera konfigurationsservern som en VMware VM. Om du behöver konfigurera VMware-VM utan mallen av någon anledning, granska de nödvändiga förutsättningarna och följ manuella instruktionerna i [i den här artikeln](physical-azure-set-up-source.md).
- Azure-kontot måste ha behörighet att skapa Azure AD-appar.


>[!IMPORTANT]
Konfigurationsservern måste distribueras enligt beskrivningen i den här artikeln. Kopiera eller klona en befintlig configuration server stöds inte.

### <a name="set-up-azure-account-permissions"></a>Konfigurera behörigheter för Azure-konto

En klient/global administratör kan tilldela behörigheter för att skapa Azure AD-appar till kontot eller tilldela rollen programutvecklare (som har behörigheter) till kontot.


Klient/global administratör kan bevilja behörigheter för kontot enligt följande:

1. I Azure AD-klient/globala administratören bör gå till **Azure Active Directory** > **användare** > **användarinställningar**.
2. Administratören bör ange **appregistreringar** till **Ja**.

 > [!NOTE]
 > Det här är en standardinställning som inte är känsliga. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Du kan också har klient/global administratör behörighet att tilldela rollen till kontot. [Läs mer](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

 


## <a name="download-the-ova-template"></a>Hämta OVA-mall

1. I valvet går du till **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Ladda ned mallen Open Virtualization program (OVA) för konfigurationsservern.

  > [!TIP]
>Du kan också hämta den senaste versionen av konfigurationsservermallen från den [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


## <a name="import-the-ova-template-in-vmware"></a>Importera OVA-mallen i VMware

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med VMWare vSphere-klienten.
2. På menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden Distribuera OVF-mall.

     ![OVF-mall](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. I **Välj källa**, ange platsen för den nedladdade OVF.
4. I **detaljer för recensionen**väljer **nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration**, accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **klart att slutföras**, för att konfigurera den virtuella datorn med standardinställningarna väljer **slå på strömmen efter distributionen** > **Slutför**.
9. Som standard distribueras den virtuella datorn med ett enda nätverkskort. Om du vill lägga till ett tillägg NIC avmarkerar **slå på strömmen efter distributionen**, och klicka på **Slutför**. Följ sedan nästa procedur.

## <a name="add-an-adapter-to-the-configuration-server"></a>Lägga till ett nätverkskort i konfigurationsservern

Om du vill lägga till ett extra nätverkskort i konfigurationsservern, lägger du till den innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** väljer du **Lägg till** > **Ethernet-kort**. Välj sedan **Nästa**.
3. Välj en adaptertyp och ett nätverk.
4. Anslut det virtuella nätverkskortet när den virtuella datorn är påslagen, att välja **Connect på ström på**. Välj sedan **nästa** > **Slutför** > **OK**.

## <a name="register-the-configuration-server"></a>Registrera konfigurationsservern 
Aktivera den virtuella datorn och registrera konfigurationsservern i Site Recovery-valvet.

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startar i Windows Server 2016-installationsproceduren. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.



## <a name="set-up-the-configuration-server"></a>Ställ in konfigurationsservern

Som en del av distributionen kan behöva du installera MySQL på konfigurationsservern VM. Du kan göra det på ett antal olika sätt:

- Kan Site Recovery ladda ned och installera MySQL. Om du vill använda det här alternativet behöver du inte göra något innan du startar installationsprogrammet för configuration server.
- Hämta och installera MySQL manuellt: innan du ställer in konfigurationsservern, ladda ned MySQL-installationsprogrammet och placera den i **C:\Temp\ASRSetup** mapp. Nu installera MySQL. 
- Ladda ned manuellt och låt Site Recovery installerar. Innan du ställer in konfigurationsservern, ladda ned MySQL-installationsprogrammet och placera den i den **C:\Temp\ASRSetup** mapp.


1. Första gången du loggar in på den virtuella datorn startar för Azure Site Recovery-konfigurationsverktyget.
2. Ange ett namn som används för att registrera konfigurationsservern i Site Recovery-valvet. Välj sedan **Nästa**.
3. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration. Observera att kontot måste ha åtkomst till det valv som du vill registrera konfigurationsservern.
4. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
5. Logga in på datorn igen. I guiden Konfigurera serverhantering startar automatiskt inom några sekunder.
6. I guiden väljer du **Ställ in anslutning**.
7. Väljer du det nätverkskort som den processerver (körs som standard på konfigurationsservern) använder för att ta emot replikeringstrafik från virtuella datorer.
8. Välj sedan **Spara**. Observera att du inte kan ändra valvinställningar när konfigurationsservern är registrerad. 
9. I **Välj Recovery Services-valv**, logga in på Microsoft Azure, Välj din Azure-prenumeration samt relevant resursgrupp och valv. 
10. I **installera programvara från tredje part**, installera MySQL:
     - Om Site Recovery hanterar MySQL nedladdning och installation, klickar du på **ladda ned och installera**. Vänta tills installationen har slutförts och sedan fortsätta med guiden.
     - Om du har laddat ned MySQL och Site Recovery installerar den, klickar du på **ladda ned och installera**. Vänta tills installationen är klar och fortsätta med guiden.
     - Om du manuellt har hämtat och installerat MySQL, klickar du på **ladda ned och installera**. Appen visar i **redan installerat**. Fortsätta med guiden.
11. I **verifiera installationskonfiguration**, kraven kontrolleras innan du fortsätter. 
12. I **konfigurera vCenter Server/vSphere ESXi-server**, anger du FQDN eller IP-adressen för vCenter-servern eller vSphere-värden där de virtuella datorer du vill replikera är placerade. Ange den port som servern lyssnar och ange ett eget namn för VMware-servern i valvet.
13. Ange de autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern och automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **lägga till** >  **fortsätta**. Autentiseringsuppgifterna har sparats lokalt.
14. I **konfigurera autentiseringsuppgifter för virtuell dator**, ange de autentiseringsuppgifter som Site Recovery använder för att automatiskt installera Mobilitetstjänsten när du aktiverar replikering för en virtuell dator.
    - För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera.
    - För Linux anger du information för rotkontot.
15. Välj **Slutför konfigurationen** för att slutföra registreringen.
16. När registreringen är klar öppnar du Azure-portalen och kontrollera att konfigurationsservern och VMware-servern visas i **Recovery Services-valv** > **hantera**  >   **Site Recovery-infrastruktur** > **Konfigurationsservrar**.


## <a name="exclude-antivirus-on-the-configuration-server"></a>Exkludera antivirus på konfigurationsservern

Om antivirusprogrammet körs på konfigurationsservern VM, se till att följande mappar undantas från antivirus åtgärder. Detta säkerställer att replikering och anslutning fungerar som förväntat: 

- C:\Program Files\Microsoft Azure Recovery Services-agenten
- C:\Program Files\Microsoft Azure Site Recovery-providern
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager
- C:\Program Files\Microsoft Azure Site Recovery fel samling verktyg
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- C:\Program filer (x86) \MySQL
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\Inetpub
- Site Recovery-installationskatalogen. Till exempel: E:\Program filer (x86) \Microsoft Azure Site Recovery


## <a name="next-steps"></a>Nästa steg
- Om du stöter på problem, granska [vanliga frågor](vmware-azure-common-questions.md#configuration server) och [felsökning](vmware-azure-troubleshoot-configuration-server.md) för konfigurationsservern.
- Nu kan [konfigurera målmiljön](./vmware-azure-set-up-target.md) i Azure.
