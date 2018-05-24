---
title: Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer med Azure Site Recovery | Microsoft Docs
description: Lär dig att konfigurera haveriberedskap till Azure för lokala virtuella VMware-datorer med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 173e31e3b1f855d488f7f8baf6659b1521ea7aa5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer

Den här kursen visar hur du konfigurerar katastrofåterställning till Azure för lokala virtuella VMware-datorer som kör Windows. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ange replikeringskälla och mål.
> * Konfigurera källmiljön för replikering, inklusive lokala Azure Site Recovery-komponenter och målmiljön för replikeringen.
> * Skapar en replikeringsprincip.
> * Aktiverar replikering för en virtuell dator.

Den här självstudien är den tredje i en serie. Självstudien förutsätter att du slutförde uppgifterna i de föregående självstudierna:

* [Förbereda Azure](tutorial-prepare-azure.md). I den här självstudien beskrivs hur du konfigurerar ett Azure Storage-konto och -nätverk, kontrollerar att ditt Azure-konto har rätt behörigheter och skapar ett Recovery Services-valv.
* [Förbereda lokal VMware](vmware-azure-tutorial-prepare-on-premises.md). I den här självstudien förbereder du konton så att Site Recovery kan få åtkomst till VMware-servrar för att upptäcka virtuella datorer, och för att även göra en push-installation av Site Recovery-mobilitetstjänstkomponenten när du aktiverar replikering för en virtuell dator. Du ska också kontrollera att dina VMware-servrar och virtuella datorer uppfyller kraven för Site Recovery.

Innan du börjar är det bra att [granska arkitekturen](vmware-azure-architecture.md) för haveriberedskap.


## <a name="select-a-replication-goal"></a>Välj ett replikeringsmål

1. I **Recovery Services-valven** väljer du valvnamnet **ContosoVMVault**.
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina datorer?** väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere Hypervisor**. Välj sedan **OK**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön


Om du vill konfigurera källmiljön behöver du en fristående lokal dator med hög tillgänglighet och en lokal värd med Site Recovery-komponenter. Komponenterna är konfigurationsservern, processervern och huvudmålservern:

- Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- Processervern fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.
- Huvudmålservern hanterar replikeringsdata vid återställning efter fel från Azure.

Om du vill konfigurera konfigurationsservern som en virtuell VMware-dator med hög tillgänglighet laddar du ned en förberedd Open Virtualization Format-mall (OVF) och importerar mallen till VMware för att skapa den virtuella datorn. När du har konfigurerat konfigurationsservern kan du registrera den i valvet. Site Recovery identifierar lokala virtuella VMware-datorer efter registreringen.

> [!TIP]
> I den här självstudien används en OVF-mall för att skapa konfigurationsserverns virtuella VMware-dator. Om du inte kan göra det kan du köra en [manuell konfiguration](physical-manage-configuration-server.md). 


### <a name="download-the-vm-template"></a>Ladda ned VM-mallen

1. I valvet går du till **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** väljer du **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Ladda ner OVF-mallen för konfigurationsservern.

  > [!TIP]
  Du kan ladda ned den senaste versionen av konfigurationsservermallen direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med hjälp av VMWare vSphere-klienten.
2. På menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden Distribuera OVF-mall. 

     ![OVF-mall](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. På **Välj källa** anger du platsen för den nedladdade OVF:en.
4. På **Detaljer för recensionen** väljer du **Nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration** accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **Klart att slutföras**:

    * Om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **Power on after deployment** (Slå på strömmen efter distributionen) > **Slutför**.

    * Om du vill lägga till ytterligare ett nätverksgränssnitt avmarkerar du alternativet för att **slå på strömmen efter distributionen**. Välj sedan **Slutför**. Konfigurationsservermallen distribueras som standard med ett enda nätverkskort. Du kan lägga till ytterligare nätverkskort efter distributionen.

## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till ett extra nätverkskort i konfigurationsservern gör du det innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** väljer du **Lägg till** > **Ethernet-kort**. Välj sedan **Nästa**.
3. Välj en adaptertyp och ett nätverk. 
4. Om du vill ansluta det virtuella nätverkskortet när den virtuella datorn är påslagen väljer du **Connect at power on** (Anslut när strömmen är på). Välj **Nästa** > **Slutför**. Välj sedan **OK**.


## <a name="register-the-configuration-server"></a>Registrera konfigurationsservern 

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Första gången du loggar in startas Azure Site Recovery-konfigurationsverktyget.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt.

### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurera inställningar och lägga till VMware-servern

1. I konfigurationsguiden för serverhantering väljer du **Ställ in anslutning** och väljer sedan det nätverkskort som processervern använder för att ta emot replikeringstrafik från virtuella datorer. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Recovery Services-valv** väljer du din Azure-prenumeration samt relevant resursgrupp och valv.
3. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server.
4. Välj **Installera VMware PowerCLI**. Kontrollera att alla webbläsarfönster är stängda innan du gör detta. Välj sedan **Fortsätt**.
5. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
6. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
7. Ange de autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **Lägg till** och välj sedan **Fortsätt**.
8. I **Konfigurera autentiseringsuppgifter för virtuell dator** anger du det användarnamn och lösenord som används för att automatiskt installera mobilitetstjänsten på datorer, när replikering har aktiverats. För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera. För Linux anger du information för rotkontot.
9. Välj **Slutför konfigurationen** för att slutföra registreringen. 
10. När registreringen är klar i Azure-portalen, kontrollerar du att konfigurationsservern och VMware-servern visas på sidan **Källa** i valvet. Välj sedan **OK** för att konfigurera målinställningarna.


Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

> [!NOTE]
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart väljer du **Konfigurationsservrar** > ***servernamn*** > **Uppdatera server**.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Välj **Förbered infrastrukturen** > **Mål**. Ange den prenumeration som du vill använda.
2. Ange om din måldistributionsmodell baseras på Azure Resource Manager eller om den är klassisk.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![Fliken Mål](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Öppna [Azure Portal](https://portal.azure.com) och välj **Alla resurser**.
2. Välj det Recovery Service-valv som har namnet **ContosoVMVault**.
3. Om du vill skapa en replikeringsprincip väljer du **Site Recovery-infrastruktur** > **Replikeringsprinciper** > **+Replikeringsprincip**.
4. I **Skapa replikeringsprincip** anger du principnamnet **VMwareRepPolicy**.
5. I **Tröskelvärde för återställningspunktmål** använder du standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
6. I **Återställningspunkt för kvarhållning** anger du standardvärdet 24 timmar som kvarhållningsperiod för varje återställningspunkt. Använd 72 timmar i den här självstudien. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
7. I **Appkompatibel ögonblicksbildsfrekvens** använder du standardvärdet 60 minuter som frekvens när programkonsekventa ögonblicksbilder skapas. Välj **OK** för att skapa principen.

   ![Skapa replikeringsprincip](./media/vmware-azure-tutorial/replication-policy.png)

Principen associeras automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om replikeringsprincipen till exempel är **rep-policy** är failback-principen **rep-policy-failback**. Den här principen används inte förrän du initierar en återställning efter fel från Azure.

## <a name="enable-replication"></a>Aktivera replikering

Site Recovery installerar mobilitetstjänsten när replikering är aktiverad för en virtuell dator. Det kan ta 15 minuter eller längre innan ändringarna träder i kraft och visas på portalen.

Aktivera replikering på följande sätt:

1. Välj **Replikera program** > **Källa**.
2. I **Källa** väljer du konfigurationsservern.
3. I **Datortyp** väljer du **Virtual Machines**.
4. I **vCenter/vSphere Hypervisor** väljer du den vCenter-server som hanterar vSphere-värden, eller så väljer du värden.
5. Välj processerver (konfigurationsserver). Välj sedan **OK**.
6. I **Mål** väljer du den prenumeration och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Välj den distributionsmodell som du vill använda i Azure (klassisk eller Resource Manager) för de redundansväxlade virtuella datorerna.
7. Välj det Azure-lagringskonto som du vill använda för att replikera data.
8. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
9. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator.
10. I **Virtual Machines** > **Välj virtuella datorer** väljer du de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Välj sedan **OK**.
11. I **Egenskaper** > **Konfigurera egenskaper** väljer du det konto som ska användas av processervern för att automatiskt installera mobilitetstjänsten på datorn.
12. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip har valts.
13. Välj **Aktivera replikering**.

Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.

Om du vill övervaka de virtuella datorer som du lägger till, kan du se när de senast identifierades i **Konfigurationsservrar** > **Senaste kontakt**. Om du vill lägga till virtuella datorer utan att vänta på den schemalagda identifieringen markerar du konfigurationsservern (välj den inte) och väljer **Uppdatera**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra ett återställningstest](site-recovery-test-failover-to-azure.md)
