---
title: "Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer med Azure Site Recovery | Microsoft Docs"
description: "Lär dig att konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer med Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3d9248d2501c7fea0492bad2687b6bdfb0b903e8
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Konfigurera katastrofåterställning till Azure för lokala virtuella VMware-datorer

Den här kursen visar hur du konfigurerar katastrofåterställning till Azure för lokala virtuella VMware-datorer som kör Windows. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Ange replikeringskälla och mål.
> * Konfigurera källmiljön för replikering, inklusive lokala Site Recovery-komponenter och målmiljön för replikeringen.
> * Skapa replikeringsprincip
> * Aktivera replikering för en virtuell dator

Detta är den tredje självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokal VMware](tutorial-prepare-on-premises-vmware.md)

Innan du börjar är det bra att [granska arkitekturen](concepts-vmware-to-azure-architecture.md) för haveriberedskap.


## <a name="select-a-replication-goal"></a>Välj ett replikeringsmål

1. I **Recovery Services-valven** klickar du på valvnamnet **ContosoVMVault**.
2. I **Komma igång** klickar du på Site Recovery. Klicka sedan på **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina datorer?** väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere Hypervisor**. Klicka sedan på **OK**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Om du vill konfigurera källmiljön behöver du en fristående lokal dator med hög tillgänglighet och en lokal värd med Site Recovery-komponenter. Komponenterna är konfigurationsservern, processervern och huvudmålservern.

- Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- Processervern fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.
- Huvudmålservern hanterar replikeringsdata vid återställning efter fel från Azure.

Om du vill konfigurera konfigurationsservern som en virtuell VMware-dator med hög tillgänglighet, laddar du ner en förberedd OVF-mall och importerar mallen till VMware för att skapa den virtuella datorn. När du konfigurerat konfigurationsservern kan du registrera den i valvet. Site Recovery identifierar lokala virtuella VMware-datorer efter registreringen.

### <a name="download-the-vm-template"></a>Ladda ned VM-mallen

1. I valvet går du till **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** klickar du på **+Konfigurationsserver**.
3. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
4. Ladda ner OVF-mallen (Open Virtualization Format) för konfigurationsservern.

  > [!TIP]
  Den senaste versionen av konfigurationsservermallen kan laddas ner direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med VMWare vSphere-klienten.
2. I menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden Distribuera OVF-mall.  

     ![OVF-mall](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. I **Välj källa** anger du platsen för den nedladdade OVF:en.
4. I **Detaljer för recensionen** klickar du på **Nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration** accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
4. Acceptera standardinställningarna på resten av sidorna i guiden.
5. I **Klart att slutföras**:
  - Om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **Power on after deployment** (Slå på strömmen efter distributionen) > **Slutför**.
  - Om du vill lägga till ytterligare ett nätverksgränssnitt avmarkerar du **Power on after deployment** (Slå på strömmen efter distributionen), och väljer sedan **Slutför**. Konfigurationsservermallen distribueras som standard med ett enda nätverkskort, men du kan lägga till ytterligare nätverkskort efter distributionen.

  
## <a name="add-an-additional-adapter"></a>Lägg till ytterligare en adapter

Om du vill lägga till ett extra nätverkskort i konfigurationsservern, gör du det innan du registrerar servern i valvet. Det går inte att lägga till fler adaptrar efter registreringen.

1. I vSphere-klientlagret högerklickar du på den virtuella datorn och väljer **Redigera inställningar**.
2. I **Maskinvara** klickar du på **Lägg till** > **Ethernet-kort**. Klicka sedan på **Nästa**.
3. Välj adaptertyp och ett nätverk. 
4. Om du vill ansluta det virtuella nätverkskortet när den virtuella datorn är påslagen väljer du **Connect at power on** (Anslut när strömmen är på). Klicka på **Nästa** > **Slutför** och sedan på **OK**.


## <a name="register-the-configuration-server"></a>Registrera konfigurationsservern 

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Den första gången du loggar in startas Azure Site Recovery-konfigurationsverktyget.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Klicka sedan på **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats klickar du på **Logga in** för att logga in på Azure-prenumerationen. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.
7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Guiden Konfigurera serverhantering startas automatiskt.

### <a name="configure-settings-and-connect-to-vmware"></a>Konfigurera inställningarna och anslut till VMware

1. I guiden Konfigurera serverhantering > **Ställ in anslutning**, väljer du det nätverkskort som ska ta emot replikeringstrafiken. Klicka sedan på **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Recovery Services-valv** väljer du din Azure-prenumeration samt relevant resursgrupp och valv.
3. I **Installera programvara från tredje part** godkänner du licensavtalet. Klicka på **Ladda ned och installera** för att installera MySQL-servern.
4. Klicka på **Installera VMware PowerCLI**. Kontrollera att alla webbläsarfönster är stängda innan du gör detta. Klicka sedan på **Fortsätt**
5. I **Verifiera installationskonfiguration** kommer förutsättningarna att verifieras innan du fortsätter.
6. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange den port som servern lyssnar efter och ett namn som ska användas för VMware-servern i valvet.
7. Ange de autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Klicka på **Lägg till** och sedan på **Fortsätt**.
8. I **Konfigurera autentiseringsuppgifter för virtuell dator** anger du det användarnamn och lösenord som används för att automatiskt installera mobilitetstjänsten på datorer, när replikering har aktiverats. För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera. För Linux anger du information för rotkontot.
9. Klicka på **Slutför konfigurationen** för att slutföra registreringen. 
10. När registreringen är klar i Azure-portalen, kontrollerar du att konfigurationsservern och VMware-servern visas på sidan **Källa** i valvet. Klicka sedan på **OK** för att konfigurera målinställningarna.


Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

> [!NOTE]
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart klickar du på **Konfigurationsservrar** > ***servernamn*** > **Uppdatera server**.

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange om din måldistributionsmodell är Resource Manager-baserad eller klassisk.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![Mål](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Öppna [Azure-portalen](https://portal.azure.com) och klicka på **Alla resurser**.
2. Klicka på det Recovery Service-valv som har namnet **ContosoVMVault**.
3. Om du vill skapa en replikeringsprincip klickar du på **Site Recovery-infrastruktur** > **Replikeringsprinciper** > **+Replikeringsprincip**.
4. I **Skapa replikeringsprincip** anger du principnamnet **VMwareRepPolicy**.
5. I **Tröskelvärde för återställningspunktmål** använder du standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
6. I **Återställningspunkt för kvarhållning** anger du standardvärdet 24 timmar som kvarhållningsperiod för varje återställningspunkt. I den här självstudien väljer vi 72 timmar. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
7. I **Appkompatibel ögonblicksbildsfrekvens** använder du standardvärdet 60 minuter som frekvens när programkonsekventa ögonblicksbilder skapas. Klicka på **OK** för att skapa principen.

   ![Princip](./media/tutorial-vmware-to-azure/replication-policy.png)

Principen associeras automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om exempelvis replikeringsprincipen är **rep-policy** kommer failback-principen vara **rep-policy-failback**. Den här principen används inte förrän du initierar en återställning efter fel från Azure.

## <a name="enable-replication"></a>Aktivera replikering

Site Recovery installerar mobilitetstjänsten när replikering är aktiverad för en virtuell dator. Det kan ta 15 minuter eller längre innan ändringarna träder i kraft och visas på portalen.

Aktivera replikering på följande sätt:

1. Klicka på **Replikera program** > **Källa**.
2. I **Källa** väljer du konfigurationsservern.
3. I **Datortyp** väljer du **Virtual Machines**.
4. I **vCenter/vSphere Hypervisor** väljer du den vCenter-server som hanterar vSphere-värden, eller så väljer du värden.
5. Välj processerver (konfigurationsserver). Klicka sedan på **OK**.
6. I **Mål** väljer du den prenumeration och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Välj den distributionsmodell som du vill använda i Azure (klassisk eller resurshantering) för de redundansväxlade virtuella datorerna.
7. Välj Azure-lagringskonto som du vill använda när du replikerar data.
8. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
9. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator.
10. I **Virtual Machines** > **Välj virtuella datorer** klickar du på och väljer de datorer som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.
11. I **Egenskaper** > **Konfigurera egenskaper** väljer du det konto som ska användas av processervern till att automatiskt installera mobilitetstjänsten på datorn.
12. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip har valts.
13. Klicka på **Aktivera replikering**.

Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.

Om du vill övervaka de virtuella datorer som du lägger till, kan du när de senast identifierades i **Konfigurationsservrar**
> **Senaste kontakt**. Om du vill lägga till virtuella datorer utan att vänta på den schemalagda identifieringen, markerar du konfigurationsservern (klicka inte på den) och klickar på **Uppdatera**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra ett återställningstest](site-recovery-test-failover-to-azure.md)
