---
title: Konfigurera VMware VM-haveriberedskap till Azure med Azure Site Recovery
description: Lär dig att konfigurera haveriberedskap i Azure för lokala virtuella VMware-datorer med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 37fdd42adf66ebcb11b357ece6ea63384630d9f4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238869"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer

I den här artikeln beskrivs hur du aktiverar replikering för lokala virtuella datorer med VMware, för haveriberedskap till Azure med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md)

Det här är den tredje självstudien i en serie som visar hur du konfigurerar haveriberedskap till Azure för lokala virtuella datorer med VMware. I den föregående självstudien har vi [förberett den lokala VMware-miljön](vmware-azure-tutorial-prepare-on-premises.md) för haveriberedskap till Azure.


I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera inställningarna för källreplikering och en lokal konfigurationsserver för platsåterställning.
> * Ställ in replikeringsmålinställningarna.
> * Skapar en replikeringsprincip.
> * Aktivera replikering för en virtuell VMware-dator.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i artikeln i avsnittet Så här gör du i innehållsförteckningen för webbplats.

## <a name="before-you-start"></a>Innan du börjar

Slutför de tidigare självstudierna:
1. Kontrollera att du har [konfigurerat Azure](tutorial-prepare-azure.md) för lokal VMware-haveriberedskap till Azure.
2. Följ [dessa steg](vmware-azure-tutorial-prepare-on-premises.md) för att förbereda din lokala VMware-distribution för haveriberedskap till Azure.
3. I den här självstudien visar vi hur du replikerar en enda virtuell dator. Om du distribuerar flera virtuella datorer med VMware bör du använda [verktyget distributionsplanerare](https://aka.ms/asr-deployment-planner). [Läs mer](site-recovery-deployment-planner.md) om det här verktyget.
4. Den här självstudien använder ett antal alternativ som du kanske vill göra annorlunda:
    - Självstudien använder en OVA-mall för att skapa konfigurationsservern VMware VM. Om du inte kan göra detta av någon anledning följer du dessa instruktioner för att konfigurera [konfigurationsservern](physical-manage-configuration-server.md) manuellt.
    - I den här självstudien hämtar och installerar Site Recovery automatiskt MySQL till konfigurationsservern. Om du vill kan du ställa in den manuellt i stället. [Läs mer](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>Välj ett skyddsmål

1. Välj valvets namn i **Recovery Services-valv**. I det här scenariot använder vi namnet **ContosoVMVault**.
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina maskiner**väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere Hypervisor**. Välj sedan **OK**.



## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

I källmiljön behöver du en enda, mycket tillgänglig, lokal dator för att vara värd för dessa lokala site recovery-komponenter:

- **Konfigurationsserver:** Konfigurationsservern samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- **Processserver:** Processservern fungerar som en replikeringsgateway. Den tar emot replikeringsdata. optimerar den med cachelagring, komprimering och kryptering och skickar den till ett cachelagringskonto i Azure. Processservern installerar också Mobilitetsserviceagenten på virtuella datorer som du vill replikera och utför automatisk identifiering av lokala virtuella datorer med VMware.
- **Huvudmålserver:** Huvudmålservern hanterar replikeringsdata under återställning efter fel från Azure.


Alla dessa komponenter installeras tillsammans på de lokala datorer som kallas *konfigurationsservern*. Som standard, för VMware haveriberedskap, ställer vi in konfigurationsservern som en högtillgänglig virtuell virtuell dator för VMware. För att göra detta hämtar du en förberedd OVA-mall (Open Virtualization Application) och importerar mallen till VMware för att skapa den virtuella datorn. 

- Den senaste versionen av konfigurationsservern är tillgänglig i portalen. Du kan också hämta den direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Om du av någon anledning inte kan använda en OVA-mall för att konfigurera en virtuell dator följer du dessa instruktioner för att konfigurera [konfigurationsservern](physical-manage-configuration-server.md) manuellt.
- Licensen som medföljer OVF-mallen är en utvärderingslicens som är giltig i 180 dagar. Windows som körs på den virtuella datorn måste aktiveras med den nödvändiga licensen. 


### <a name="download-the-vm-template"></a>Ladda ned VM-mallen

1. Gå till Förbered **infrastrukturkälla** > **i**valvet .
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Ladda ner OVF-mallen för konfigurationsservern.



## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware


1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med VMWare vSphere-klienten.
2. På **Arkiv-menyn** väljer du **Distribuera OVF-mall** för att starta **guiden Distribuera OVF-mall**. 

     ![OVF-mall](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. På **Välj källa** anger du platsen för den nedladdade OVF:en.
4. På **Detaljer för recensionen** väljer du **Nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration** accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. På **Klar att slutföras** väljer du, om du vill konfigurera den virtuella datorn med standardinställningarna, **Slå på strömmen efter distributionen** > **Slutför**.

   > [!TIP]
   > Om du vill lägga till ytterligare ett nätverkskort avmarkerar du **Power on efter att distributionen** > **har avslutats**. Mallen innehåller som standard en enda NIC. Du kan lägga till ytterligare nätverkskort efter distributionen.

## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till ytterligare ett nätverkskort på konfigurationsservern lägger du till det innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** väljer du **Lägg till** > **Ethernet-kort**. Välj sedan **Nästa**.
3. Välj en adaptertyp och ett nätverk. 
4. Om du vill ansluta det virtuella nätverkskortet när den virtuella datorn är påslagen väljer du **Connect at power on** (Anslut när strömmen är på). Välj **Nästa** > **slut**. Välj sedan **OK**.


## <a name="register-the-configuration-server"></a>Registrera konfigurationsservern 

När konfigurationsservern har konfigurerats registrerar du den i valvet.

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Första gången du loggar in startas Azure Site Recovery-konfigurationsverktyget inom några sekunder.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern. Kontrollera att nödvändiga [roller](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements) tilldelas den här användaren.
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt om några sekunder.


### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurera inställningar och lägga till VMware-servern

Slutför konfigurationsservern. Innan du fortsätter, se till att alla förutsättningar uppfylls för en lyckad konfiguration av [konfigurationsservern.](vmware-azure-deploy-configuration-server.md#prerequisites)


1. Välj Konfigurera anslutning i guiden Hantering av **konfigurationsserver**. I listrutan väljer du först det nätverkskort som den inbyggda processservern använder för identifiering och push-installation av mobilitetstjänsten på källdatorer och väljer sedan det nätverkskort som Configuration Server använder för anslutning till Azure. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Återställningstjänster väljer**du din Azure-prenumeration och relevant resursgrupp och valv.
3. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server. Om du har placerat MySQL i sökvägen kan det här steget hoppas över. Läs [mer](vmware-azure-deploy-configuration-server.md#configure-settings)
4. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
5. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
6. Ange de uppgifter för användarautentisering som ska användas av konfigurationsservern för att ansluta till VMware-servern. Kontrollera att användarnamn och lösenord är korrekta och att de ingår i gruppen Administratörer på den virtuella datorn som ska skyddas. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **Lägg till** och välj sedan **Fortsätt**.
7. I **Konfigurera autentiseringsuppgifter för virtuell dator** anger du det användarnamn och lösenord som ska användas för att automatiskt installera mobilitetstjänsten på virtuella datorer, när replikering har aktiverats.
    - För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera.
    - För Linux anger du information för rotkontot.
8. Välj **Slutför konfigurationen** för att slutföra registreringen.
9. När registreringen är klar öppnar du Azure-portalen och kontrollerar att konfigurationsservern och VMware-servern finns med på **Recovery Services Vault** > **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.


När konfigurationsservern har registrerats ansluter Site Recovery till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

> [!NOTE]
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart väljer du**Uppdatera*****servernamn*** > för **konfigurationsservrar.** > 

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Välj **Förbered infrastrukturmål** > **Target**. Ange den prenumeration som du vill använda. Vi använder en Resource Manager-modell.
2. Site Recovery kontrollerar att du har ett eller flera virtuella nätverk. Du bör ha dessa när du konfigurerar Azure komponenterna i den [första självstudien](tutorial-prepare-azure.md) i den här serien med självstudier.

   ![Fliken Mål](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Öppna [Azure-portalen](https://portal.azure.com). Sök efter och välj **Recovery Services-valv**.
2. Välj Recovery Services-valvet (**ContosoVMVault** i den här självstudien).
3. Om du vill skapa en replikeringsprincip väljer du**Replikeringsprinciper för platsåterställningsinfrastruktur** >  **Site Recovery infrastructure** > **+Replikeringsprincipen**.
4. I **Skapa replikeringsprincip** anger du principnamnet. Vi använder **VMwareRepPolicy**.
5. I **Tröskelvärde för återställningspunktmål** använder du standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
6. I **Återställningspunkt för kvarhållning** anger du hur länge varje återställningspunkt finns kvar. I den här självstudien använder vi 72 timmar. Replikerade virtuella datorer kan återställas till valfri punkt i ett kvarhållningsfönster.
7. I **Frekvens för programkonsekventa ögonblicksbilder** anger du hur ofta programkonsekventa ögonblicksbilder skapas. Vi använder standardinställningen 60 minuter. Välj **OK** för att skapa principen.

   ![Skapa replikeringsprincip](./media/vmware-azure-tutorial/replication-policy.png)

- Principen associeras automatiskt med konfigurationsservern.
- Som standard skapas automatiskt en matchande princip för återställning efter fel. Om replikeringsprincipen till exempel är **rep-policy** är failback-principen **rep-policy-failback**. Den här principen används inte förrän du initierar en återställning efter fel från Azure.

## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering för virtuella datorer enligt följande:

1. Välj **Replikera programkälla** > **Source**.
2. I **Källa**väljer du **Lokalt** och väljer konfigurationsservern i **källplats**.
3. I **Datortyp** väljer du **Virtual Machines**.
4. I **vCenter/vSphere Hypervisor** väljer du vSphere-värden eller den vCenter-server som hanterar värden.
5. Välj processerver (installeras som standard på konfigurationsserverns virtuella dator). Välj sedan **OK**. Hälsostatus för varje processserver anges enligt rekommenderade gränser och andra parametrar. Välj en felfri processserver. Det går inte att välja en [kritisk](vmware-physical-azure-monitor-process-server.md#process-server-alerts) processserver. Du kan antingen [felsöka och lösa](vmware-physical-azure-troubleshoot-process-server.md) felen **eller** konfigurera en [utskalningsprocessserver](vmware-azure-set-up-process-server-scale.md).
6. I **Mål** väljer du den prenumeration och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Vi använder Resource Manager-distributionsmodellen. 
7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
8. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla virtuella datorer som du aktiverat replikering för. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator.
9. I **Virtuella datorer** > **Välj virtuella datorer**väljer du varje dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Välj sedan **OK**. Om du inte kan visa/välja en viss virtuell dator [kan du läsa mer](https://aka.ms/doc-plugin-VM-not-showing) om hur du löser problemet.
10. I **Egenskaper** > **Konfigurera egenskaper**väljer du det konto som ska användas av processservern för att automatiskt installera Mobilitetstjänsten på datorn.
11. Kontrollera att rätt replikeringsprincip är markerad i **Replikeringsinställningar** > Konfigurera**replikeringsinställningar.**
12. Välj **Aktivera replikering**. Site Recovery installerar mobilitetstjänsten när replikering är aktiverad för en virtuell dator.
13. Du kan spåra förloppet för jobbet **Aktivera skydd** i**Jobs** > **Jobbplatsåterställningsjobb**för **inställningar.** >  När jobbet **Slutför skydd** körs och en generering av återställningspunkter har slutförts är datorn klar för redundans.
14. Det kan ta 15 minuter eller längre innan ändringarna träder i kraft och visas på portalen.
15. Om du vill övervaka virtuella datorer som du lägger till kontrollerar du den senast identifierade tiden för virtuella datorer i **konfigurationsservrar** > **senaste kontakt vid**. Om du vill lägga till virtuella datorer utan att vänta på den schemalagda identifieringen markerar du konfigurationsservern (välj den inte) och väljer **Uppdatera**.

## <a name="next-steps"></a>Nästa steg
När du har aktiverat replikering kör du en övning för att se till att allt fungerar som förväntat.
> [!div class="nextstepaction"]
> [Köra ett återställningstest](site-recovery-test-failover-to-azure.md)
