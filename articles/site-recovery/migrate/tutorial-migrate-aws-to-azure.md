---
title: "Migrera virtuella datorer från AWS till Azure med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du migrerar virtuella datorer som körs i Amazon Web Services (AWS) till Azure, med hjälp av Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/27/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9b1aabcd88ce93ff9316d3ee99619eccaeb6a38e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrera virtuella AWS-datorer (Amazon Web Services) till Azure

I de här självstudierna får du lära dig hur du migrerar virtuella AWS-datorer (Amazon Web Services) till virtuella Azure-datorer med Site Recovery. När du migrerar EC2 instanser till Azure, VMsare behandlas som om de är fysiska, lokala datorer. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Förbereda Azure-resurser
> * Förbereda AWS EC2-instanser för migrering
> * Distribuera en konfigurationsserver
> * Aktivera replikering för virtuella datorer
> * Köra ett redundanstest för att kontrollera att allt fungerar
> * Kör en engångsredundans till Azure

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure-resources"></a>Förbereda Azure-resurser 

Du måste ha några resurser klara i Azure som de migrerade EC2-instanserna kan använda. Detta innefattar ett lagringskonto, ett valv och ett virtuellt nätverk.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Avbildningar av replikerade datorer lagras i Azure-lagringen. Virtuella Azure-datorer skapas från lagringsplatsen vid redundans från lokalt till Azure.

1. På menyn [Azure Portal](https://portal.azure.com) klickar du på **Skapa en resurs** -> **Lagring** -> **Lagringskonto**.
2. Ange ett namn för lagringskontot. För dessa självstudier använder vi namnet **awsmigrated2017**. Namnet måste vara unikt i Azure och mellan 3 och 24 tecken långt. Det får endast bestå av siffror och gemener.
3. Behåll standardinställningarna för **Distributionsmodell**, **Typ av konto**, **Prestanda** och **Säker överföring krävs**.
5. Välj standardvärdet **RA-GRS** för **Replikering**.
6. Välj den prenumeration du vill använda för dessa självstudier.
7. För **Resursgrupp** väljer du **Skapa ny**. I det här exemplet använder vi **migrationRG** som namn.
8. Välj **Europa, västra** som plats. 
9. Skapa lagringskontot genom att klicka på **Skapa**.

### <a name="create-a-vault"></a>Skapa ett valv

1. I [Azure Portal](https://portal.azure.com), i det vänstra navigeringsfönstret klickar du på **Fler tjänster** och letar upp och väljer **Recovery Services-valv**.
2. På sidan Recovery Services-valv klickar du **+ Lägg till** i det övre vänstra hörnet på sidan.
3. För **Namn** anger du *myVault*. 
4. För **Prenumeration** väljer du lämplig prenumeration.
4. För **Resursgrupp** väljer du **Använd befintlig** och sedan *migrationRG*. 
5. För **Plats** väljer du *Europa, västra*. 
5. För att snabbt komma åt det nya valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen**.
7. Klicka på **Skapa** när du är klar.

Det nya valvet visas på **Instrumentpanelen** > **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

När de virtuella Azure-datorerna skapats efter migreringen (redundans) är de anslutna till det här nätverket.

1. I den [Azure-portalen](https://portal.azure.com), klickar du på **skapar du en resurs** > **nätverk** >
    **virtuellt nätverk**
3. För **Namn** anger du *myMigrationNetwork*.
4. Ändra inte standardvärdet för **Adressutrymme**.
5. För **Prenumeration** väljer du lämplig prenumeration.
6. För **Resursgrupp** väljer du **Använd befintlig**  och *migrationRG* i listrutan.
7. För **Plats** väljer du **Europa, västra**. 
8. Lämna standardinställningarna för **Undernät** som de är, både **Namn** och **IP-intervall**.
9. Låt **Tjänstens slutpunkter** vara inaktiverat.
10. Klicka på **Skapa** när du är klar.


## <a name="prepare-the-ec2-instances"></a>Förbereda EC2-instanserna

Du behöver en eller flera virtuella datorer som du vill migrera. Dessa EC2-instanser bör köra 64-bitarsversionen av Windows Server 2008 R2 SP1 eller senare, Windows Server 2012, Windows Server 2012 R2 eller Red Hat Enterprise Linux 6.7 (endast HVM virtualiserade instanser). Servern får bara ha Citrix PV- eller AWS PV-drivrutiner. Instanser som kör RedHat PV-drivrutiner stöds inte.

Mobilitetstjänsten måste installeras på varje virtuell dator du vill replikera. Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för den virtuella datorn. För automatisk installation måste du förbereda ett konto på EC2-instanserna som Site Recovery använder för att komma åt den virtuella datorn.

Du kan använda en domän eller lokalt konto. För Linux-datorer måste kontot vara en rot på Linux-källservern. För virtuella Windows-datorer måste du, om du inte använder ett domänkonto, inaktivera kontroll av åtkomst för fjärranvändare på den lokala datorn:

  - I registret, under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, lägger du till DWORD-posten **LocalAccountTokenFilterPolicy** och sätter värdet till 1.
    
Du behöver också en separat EC2-instans som du kan använda som Site Recovery konfigurationsserver. Den här instansen måste köra Windows Server 2012 R2. 
    

## <a name="prepare-the-infrastructure"></a>Förbered infrastrukturen 

På portalsidan för ditt valv väljer du **Site Recovery** från avsnittet **Komma igång** och klickar sedan på **Förbered infrastruktur**.

### <a name="1-protection-goal"></a>1 Skyddsmål

Välj följande värden på sidan **Skyddsmål**:
   
|    |  | 
|---------|-----------|
| Var finns dina datorer? | **Lokalt**|
| Till vilken plats ska dina datorer replikeras? |**Till Azure**|
| Är dina datorer virtualiserade? | **Inte virtualiserad/övrigt**|

När du är klar klickar du på **OK** för att fortsätta till nästa avsnitt.

### <a name="2-source-prepare"></a>2 Förbered källa 

På sidan **Förbered källa** klickar du på **+ Konfigurationsserver**. 

1. Använd en EC2-instans som kör Windows Server 2012 R2 för att skapa en konfigurationsserver och registrera den med återställningsvalvet.

2. Konfigurera proxyn på den virtuella datorn med EC2-instansen som du använder som konfigurationsserver så att den kan komma åt [tjänst-URL:erna](../site-recovery-support-matrix-to-azure.md).

3. Ladda ned det [enhetliga installationsprogrammet för Microsoft Azure Site Recovery](http://aka.ms/unifiedinstaller_wus). Du kan ladda ned det till din lokala dator och sedan kopiera över det till den virtuella dator du använder som konfigurationsserver.

4. Klicka på knappen **Ladda ned** för att hämta valvregistreringsnyckeln. Kopiera över den nedladdade filen till den virtuella dator du använder som konfigurationsserver.

5. På den virtuella datorn högerklickar du på installationsprogrammet du hämtade för det **enhetliga installationsprogrammet för Microsoft Azure Site Recovery** och väljer **Kör som administratör**. 

    1. I **Before you begin** (Innan du börjar) väljer du **Install the configuration server and process server** (Installera konfigurationsservern och processervern) och klickar på **Next** (Nästa).
    2. I **Third-Party Software License** (Licens för tredjeparts programvara) väljer du **I accept the third-party license agreement.** (Jag accepterar licensavtalet från tredje part.) och klickar sedan på **Next** (Nästa).
    3. I **Registrering** klickar du på Bläddra och navigerar till den plats där du vill lägga nyckelfilen för valvregistrering och klickar sedan på **Next** (Nästa).
    4. I **Internet Settings** (Internetinställningar) väljer du **Connect to Azure Site Recovery without a proxy server.** (Anslut till Azure Site Recovery utan proxyserver.) och klickar sedan på **Next** (Nästa).
    5. I sidan **Kravkontroll** körs kontroller av flera objekt. När detta är klart klickar du på **Nästa**. 
    6. I **MySQL Configuration** (MySQL-konfiguration) anger du lösenorden och klickar sedan på **Next** (Nästa).
    7. I **Miljöinformation** väljer du **Nej**. Du behöver inte skydda de virtuella VMware-datorerna. Klicka sedan på **Nästa**.
    8. I **Installationsplats** klickar du på **Nästa** för att acceptera standardinställningarna.
    9. I **Val av nätverk** klickar du på **Nästa** för att acceptera standardinställningarna.
    10. I **Sammanfattning** klickar du på **Installera**.
    11. I **Installationsförlopp** visas information om hur långt installationen kommit. När den är klar klickar du på **Slutför**. Ett popup-meddelande om att du eventuellt måste starta om visas. Klicka på **OK**. Ett popup-meddelande med lösenfrasen för konfigurationsserveranslutningen visas också, kopiera lösenfrasen till Urklipp och spara den på ett säkert ställe.
    
6. Kör **cspsconfigtool.exe** på den virtuella datorn för att skapa ett eller flera hanteringskonton på konfigurationsservern. Se till att hanteringskontona har administratörsbehörighet på de EC2-instanser som du vill migrera. 

När du är klar med konfigurationen av konfigurationsservern går du tillbaka till portalen och väljer den server du nyss skapade för **konfigurationsservern** och klickar på *OK** för att gå vidare till steg 3 Förbered mål.

### <a name="3-target-prepare"></a>3 Förbered mål 

I det här avsnittet anger du information om resurserna som du skapade när du gick igenom avsnittet [Förbereda Azure-resurser](#prepare-azure-resources) tidigare i självstudierna.

1. I **Prenumeration** väljer du den Azure-prenumeration du använde för självstudierna [Förbered Azure](../tutorial-prepare-azure.md).
2. Välj **Resource Manager** som distributionsmodell.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk. Detta ska vara de resurser som du skapade när du gick igenom avsnittet [Förbereda Azure-resurser](#prepare-azure-resources) tidigare i självstudierna
4. Klicka på **OK** när du är klar.


### <a name="4-replication-settings-prepare"></a>4 Förbered replikeringsinställningar 

Du måste skapa en replikeringsprincip innan du kan aktivera replikering

1. Klicka på **+ Replikera och associera**.
2. I **Namn** anger du **myReplicationPolicy**.
3. Låt resten av standardinställningarna vara som de är och klicka på **OK** för att skapa principen. Den nya principen associeras automatiskt med konfigurationsservern. 

### <a name="5-deployment-planning-select"></a>5 Starta distributionsplanering 

I **Har du planerat distributionen?** väljer du **Jag gör det senare** från listrutan och klickar sedan på **OK**.

När du är klar med alla 5 delar av **Förbered infrastruktur** klickar du på **OK**.


## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering för alla virtuella datorer du vill migrera. När replikeringen har aktiverats installerar Site Recovery automatiskt mobilitetstjänsten. 

1. Öppna [Azure-portalen](htts://portal.azure.com).
1. På sidan för ditt valv, under **Komma igång** klickar du på **Site Recovery**.
2. Under **För lokala datorer och Azure-VM:ar** klickar du på **Steg 1: Replikera program**. Slutför guiden med följande information och klicka på **OK** på varje sida när du är klar:
    - 1 Konfigurera källa:
      
    |  |  |
    |-----|-----|
    | Källa: | **Lokalt**|
    | Källplats:| Namnet på konfigurationsservern för EC2-instansen.|
    |Typ av dator: | **Fysiska datorer**|
    | Processerver: | Välj konfigurationsserver i listrutan.|
    
    - 2 Konfigurera mål
        
    |  |  |
    |-----|-----|
    | Mål: | Låt standardvärdet vara kvar.|
    | Prenumeration: | Välj den prenumeration du har använt.|
    | Postredundant resursgrupp:| Använd den resursgrupp du skapade i avsnittet [Förbereda Azure-resurser](#prepare-azure-resources).|
    | Postredundant distributionsmodell: | Välj **Resource Manager**|
    | Lagringskonto: | Välj det lagringskonto du skapade i avsnittet [Förbereda Azure-resurser](#prepare-azure-resources).|
    | Azure-nätverk: | Välj **Konfigurera nu för valda datorer**|
    | Postredundant Azure-nätverk: | Välj det nätverk du skapade i avsnittet [Förbereda Azure-resurser](#prepare-azure-resources).|
    | Undernät: | Välj **standardinställningen** i listrutan.|
    
    - 3 Välj fysiska datorer
        
        Klicka på **+ Fysisk dator** och ange sedan **namn**, **IP-adress** och **OS-typ** för den EC2-instans du vill migrera och klicka på **OK**.
        
    - 4 Konfigurera egenskaper
        
        Välj det konto du skapade på konfigurationsservern från listrutan och klicka på **OK**.
        
    - 5 Konfigurera replikeringsinställningar
    
        Kontrollera att den replikeringsprincip som valts i listrutan är **myReplicationPolicy** och klicka sedan på **OK**.
        
3. När guiden är klar klickar du på **Aktivera replikering**.
        

Du kan följa förloppet för jobbet **Aktivera skydd** i **Övervakningar och rapporter** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.        
        
När du aktiverar replikering för en virtuell dator kan det ta 15 minuter eller längre innan ändringarna träder i kraft och visas på portalen.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

När du kör ett redundanstest händer följande:

1. En kravkontroll körs för att säkerställa att alla de villkor som krävs för redundans är på plats.
2. Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
3. En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.

Kör redundanstestet på följande sätt i portalen:

1. På sidan för ditt valv går du till **Skyddade objekt** > **Replikerade objekt**> och klickar på den virtuella datorn > **+ Redundanstest**.

2. Välj en återställningspunkt som ska användas för redundans:
    - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt RTO (mål för återställningstid).
    - **Senaste app-konsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste app-konsekventa återställningspunkten. Tidsstämpeln visas.
    - **Anpassad**: Välj annan återställningspunkt.
3. I **Redundanstest** väljer du det Azure-målnätverk som de virtuella Azure-datorerna ska ansluta till efter redundans. Det ska vara det nätverk du skapade i avsnittet [Förbered Azure-resurser](#prepare-azure-resources).
4. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Redundanstest** på sidan för ditt valv i **Övervakning och rapporter** > **Jobb** >
   **Site Recovery-jobb**.
5. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn har rätt storlek, att den är ansluten till rätt nätverk och att den körs.
6. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure.
7. För att ta bort virtuella Azure-datorer som skapades under redundanstestningen klickar du på **Rensa redundanstestning** på återställningsplanen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. 


## <a name="migrate-to-azure"></a>Migrera till Azure

Kör en riktig redundansväxling för EC2-instanserna för att migrera dem till virtuella Azure-datorer. 

1. I **Skyddade objekt** > **Replikerade objekt** klickar du på AWS-instanser > **Redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Välj den senaste återställningspunkten.
3. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery för att stänga av virtuella källdatorer innan du utlöser redundansväxling. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**.
4. Kontrollera att den virtuella datorn visas i **Replikerade objekt**. 
5. Högerklicka på varje virtuell dator > **Slutför migrering**. Detta avslutar migreringsprocessen, stoppar replikeringen för virtuella datorer i AWS och stoppar Site Recovery-debitering för den virtuella datorn.

    ![Slutföra migrering](./media/tutorial-migrate-aws-to-azure/complete-migration.png)

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Innan redundansen startas så stoppas replikeringen av den virtuella datorn. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.  


    

## <a name="next-steps"></a>Nästa steg

I det här avsnittet har du lärt dig hur du migrerar AWS EC2-instanser till virtuella Azure-datorer. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudierna för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](../../virtual-machines/windows/tutorial-manage-vm.md)
