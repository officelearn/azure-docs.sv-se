---
title: Migrera virtuella AWS-datorer till Azure med Azure Site Recovery-tjänsten | Microsoft Docs
description: Den här artikeln beskriver hur du migrerar virtuella Windows-datorer som körs i Amazon Web Services (AWS) till Azure med hjälp av Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b2ff67e207f8a3a2b79635b080c78021162f0ac6
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519239"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrera virtuella AWS-datorer (Amazon Web Services) till Azure

I de här självstudien får du lära dig hur du migrerar virtuella Amazon Web Services-datorer (AWS) till virtuella Azure-datorer med Azure Site Recovery. När du migrerar AWS EC2-instanser till Azure, behandlas de virtuella datorerna som fysiska, lokala datorer. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera förutsättningar
> * Förbereda Azure-resurser
> * Förbered AWS EC2-instanser för migrering
> * Distribuera en konfigurationsserver
> * Aktivera replikering för virtuella datorer
> * Köra ett redundanstest för att kontrollera att allt fungerar
> * Kör en engångsredundans till Azure

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
- Kontrollera att de virtuella datorer du vill migrera körs på en OS-version som stöds. Versioner som stöds inkluderar: 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - 64-bitarsversionen av Windows Server 2008 R2 SP1 eller senare
  - Red Hat Enterprise Linux 6.4 till 6.10, 7.1 till 7.6 (endast HVM-virtualiserade instanser)  *(Instanser som kör RedHat PV-drivrutiner stöds inte.)*
  - CentOS 6.4 till 6.10, 7.1 till 7.6 (endast HVM-virtualiserade instanser)
 
- Mobilitetstjänsten måste installeras på varje virtuell dator som du vill replikera. 

    > [!IMPORTANT]
    > Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för den virtuella datorn. För automatisk installation, måste du förbereda ett konto på EC2-instanserna som Site Recovery använder för att komma åt den virtuella datorn. Du kan använda en domän eller lokalt konto. 
    > - För Linux-datorer måste kontot vara en rot på Linux-källservern. 
    > - För virtuella Windows-datorer måste du, om du inte använder ett domänkonto, inaktivera kontroll av åtkomst för fjärranvändare på den lokala datorn:
    >
    >      I registret, under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, lägger du till DWORD-posten **LocalAccountTokenFilterPolicy** och sätter värdet till **1**.

- Du behöver en separat EC2-instans som du kan använda som Site Recovery-konfigurationsserver. Den här instansen måste köra Windows Server 2012 R2.

## <a name="prepare-azure-resources"></a>Förbereda Azure-resurser

Du måste ha några resurser klara i Azure som de migrerade EC2-instanserna kan använda. Detta innefattar ett lagringskonto, ett valv och ett virtuellt nätverk.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Avbildningar av replikerade datorer lagras i Azure Storage. Virtuella Azure-datorer skapas från lagringen vid redundansväxling från lokalt till Azure.

1. I [Azure-portalen](https://portal.azure.com) i den vänstra menyn, väljer du **Skapa en resurs** > **Storage** > **Storage-konto**.
2. Ange ett namn för lagringskontot. I de här självstudierna använder vi namnet **awsmigrated2017**. Namnet måste:
    - Vara unikt i Azure
    - Vara mellan 3 och 24 tecken
    - Innehålla enbart gemener, siffror och bindestreck
3. Behåll standardinställningarna för **Distributionsmodell**, **Typ av konto**, **Prestanda** och **Säker överföring krävs**.
5. För **Replikering**, väljer du standardvärdet **RA-GRS**.
6. Välj den prenumeration du vill använda för dessa självstudier.
7. För **Resursgrupp** väljer du **Skapa ny**. I det här exemplet använder vi **migrationRG** för resursgruppens namn.
8. För **Plats** väljer du **Europa, västra**.
9. Skapa lagringskontot genom att välja **Skapa**.

### <a name="create-a-vault"></a>Skapa ett valv

1. I [Azure-portalen](https://portal.azure.com), väljer du **Alla tjänster**. Sök efter och välj sedan **Recovery Services-valv**.
2. På sidan Azure Recovery Services-valv väljer du **Lägg till**.
3. Som **Namn** anger du **myVault**.
4. Som **Prenumeration** anger du den prenumeration som du vill använda.
4. För **Resursgrupp** väljer du **Använd befintlig** och sedan **migrationRG**.
5. För **Plats** väljer du **Europa, västra**.
5. Välj **Fäst till instrumentpanelen** för att snabbt kunna komma åt det nya valvet från instrumentpanelen.
7. När du är klar väljer du **Skapa**.

Om du vill se det nya valvet, går du till **Instrumentpanelen** > **Alla resurser**. Det nya valvet visas även på huvudsidan för **Recovery Services-valv**.

### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

När de virtuella Azure-datorerna skapats efter migreringen (redundans) ansluts de till det här Azure-nätverket.

1. I [Azure-portalen](https://portal.azure.com) väljer du **Skapa en resurs** > **Nätverk** >
   **Virtuellt nätverk**.
3. För **Namn** anger du **myMigrationNetwork**.
4. Ändra inte standardvärdet för **Adressutrymme**.
5. Som **Prenumeration** anger du den prenumeration som du vill använda.
6. För **Resursgrupp** väljer du **Använd befintlig** och sedan **migrationRG**.
7. För **Plats** väljer du **Europa, västra**.
8. Under **undernät** lämnar du standardvärdena för **Namn** och **IP-intervall**.
9. Lämna alternativet **Tjänsteslutpunkter** inaktiverat.
10. När du är klar väljer du **Skapa**.

## <a name="prepare-the-infrastructure"></a>Förbered infrastrukturen

På din portalsida för valvet i Azure-portalen i avsnittet **Kom igång** väljer du **Site Recovery** och klickar sedan på **Förbered infrastruktur**. Utför följande steg.

### <a name="1-protection-goal"></a>1: Skyddsmål

Välj följande värden på sidan **Skyddsmål**:

|    |  |
|---------|-----------|
| Var finns dina datorer? |Välj **Lokalt**.|
| Till vilken plats ska dina datorer replikeras? |Välj **till Azure**.|
| Är dina datorer virtualiserade? |Välj **Inte virtualiserad/övrigt**.|

När du är klar, väljerr du **OK** för att fortsätta till nästa avsnitt.

### <a name="2-select-deployment-planning"></a>2: Starta distributionsplanering

I **Har du slutfört distributionsplanering** väljer du **Jag gör det senare** och klickar sedan på **OK**.

### <a name="3-prepare-source"></a>3: Förbered källa

På sidan **Förbered källa** klickar du på **+ Konfigurationsserver**.

1. Använd en EC2-instans som kör Windows Server 2012 R2 för att skapa en konfigurationsserver och registrera den med återställningsvalvet.
2. Konfigurera proxyn på den virtuella datorn med EC2-instansen som du använder som konfigurationsserver så att den kan komma åt [tjänst-URL:erna](site-recovery-support-matrix-to-azure.md).
3. Hämta det [enhetliga installationsprogrammet för Microsoft Azure Site Recovery](https://aka.ms/unifiedinstaller_wus). Du kan hämta det till din lokala dator och sedan kopiera över det till den virtuella dator du använder som konfigurationsserver.
4. Klicka på knappen **Hämta** för att hämta valvregistreringsnyckeln. Kopiera den hämtade filen till den virtuella dator du använder som konfigurationsserver.
5. På den virtuella datorn högerklickar du på installationsprogrammet du hämtade för det enhetliga installationsprogrammet för Microsoft Azure Site Recovery och väljer sedan **Kör som administratör**.

    1. I **Innan du börjar** väljer du **Installera konfigurerationsservern och processervern** och väljer sedan **Nästa**.
    2. I **Licens för tredjeparts programvara** väljer du **Jag accepterar licensavtalet från tredje part** och väljer sedan **Nästa**.
    3. I **Registrering** väljer du **Bläddra** och går sedan där du lade nyckelfilen för valvregistrering. Välj **Nästa**.
    4. I **Internetinställningar** väljer du **Anslut till Azure Site Recovery utan en proxyserver** och därefter väljer du **Next**.
    5. Sidan **Kontroll av förhandskrav** kör kontroller för flera objekt. När den är klar väljer du **Nästa**.
    6. I **MySQL-konfiguration** anger du lösenorden och klickar sedan på **Nästa**.
    7. I **Miljöinformation** väljer du **Nej**. Du behöver inte skydda VMware-datorer. Välj sedan **Nästa**.
    8. I **Installationsplats** klickar du på **Nästa** för att acceptera standardinställningarna.
    9. I **Nätverksval** klickar du på **Nästa** för att acceptera standardinställningarna.
    10. I **Sammanfattning** väljer du **Installera**.
    11. **Installationsförloppet** visar dig information om installationsprocessen. När den är klar väljer du **Avsluta**. Ett fönster visar ett meddelande om en omstart. Välj **OK**. Därefter visar ett fönster ett meddelande om lösenfrasen för anslutning för konfigurationsservern. Kopiera lösenfrasen till Urklipp och spara den på en säker plats.
6. Kör cspsconfigtool.exe på den virtuella datorn för att skapa ett eller flera hanteringskonton på konfigurationsservern. Se till att hanteringskontona har administratörsbehörighet på de EC2-instanser som du vill migrera.

När du är klar med konfigurationen av konfigurationsservern går du tillbaka till portalen och väljer den server du skapade för **konfigurationsservern**. Välj **OK** för att gå till 3: Förbered målet.

### <a name="4-prepare-target"></a>4: Förbered målet

I det här avsnittet anger du information om de resurser som du skapade i [Förbered Azure-resurser](#prepare-azure-resources) tidigare i de här självstudierna.

1. I **Prenumeration** väljer du den Azure-prenumeration du använde för självstudierna [Förbered Azure](tutorial-prepare-azure.md).
2. Välj **Resource Manager** som distributionsmodell.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure Storage-konton och nätverk. Det ska vara de resurser som du skapade i [Förbered Azure-resurser](#prepare-azure-resources) tidigare i de här självstudierna.
4. När du är klar väljer du **Ok**.

### <a name="5-prepare-replication-settings"></a>5: Förbered replikeringsinställningarna

Innan du kan aktivera replikering, måste du skapa en replikeringsprincip.

1. Klicka på **Replikera och associera**.
2. I **Namn** anger du **myReplicationPolicy**.
3. Låt resten av standardinställningarna vara som de är och klicka på **OK** för att skapa principen. Den nya principen associeras automatiskt med konfigurationsservern.

När du är klar med alla fem avsnitt under **Förbered infrastrukturen** väljer du **OK**.

## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering för varje virtuell dator som du vill migrera. När replikeringen har aktiverats, installerar Site Recovery automatiskt mobilitetstjänsten.

1. Gå till [Azure-portalen](https://portal.azure.com).
1. På sidan för ditt valv, under **Kom igång**, klickar du på **Site Recovery**.
2. Under **För lokala datorer och virtuella Azure-datorer** klickar du på **Steg 1: Replikera program**. Slutför guidesidorna med följande information. Välj **OK** på varje sida när du är klar:
   - 1: Konfigurera källan

     |  |  |
     |-----|-----|
     | Källa: | Välj **Lokalt**.|
     | Källplats:| Ange namnet på din konfigurationsservers EC2-instans.|
     |Typ av dator: | Välj **Fysiska datorer**.|
     | Processerver: | Välj konfigurationsserver i listrutan.|

   - 2: Konfigurera målet

     |  |  |
     |-----|-----|
     | Mål: | Låt standardvärdet vara kvar.|
     | Prenumeration: | Välj den prenumeration som du har använt.|
     | Postredundant resursgrupp:| Använd den resursgrupp du skapade i avsnittet [Förbered Azure-resurser](#prepare-azure-resources).|
     | Postredundant distributionsmodell: | Välj **Resource Manager**.|
     | Lagringskonto: | Välj det Storage-konto du skapade i avsnittet [Förbered Azure-resurser](#prepare-azure-resources).|
     | Azure-nätverk: | Välj **Konfigurera nu för valda datorer**.|
     | Postredundant Azure-nätverk: | Välj det nätverk du skapade i avsnittet [Förbered Azure-resurser](#prepare-azure-resources).|
     | Undernät: | Välj **standardinställningen** i listrutan.|

   - 3: Välj fysiska datorer

     Välj **Fysisk dator** och ange värdena för **Namn**, **IP-adress** och **OS-typ** för den EC2-instans du vill migrera. Välj **OK**.

   - 4: Konfigurera egenskaper

     Välj det konto du skapade på konfigurationsservern och klicka på **OK**.

   - 5: Konfigurera replikeringsinställningar

     Kontrollera att den replikeringsprincip som valts i listrutan är **myReplicationPolicy** och klicka sedan på **OK**.

3. När guiden är klar klickar du på **Aktivera replikering**.

Du kan följa förloppet för jobbet **Aktivera skydd** genom att gå till **Övervakning och rapporter** > **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.        

När du aktiverar replikering för en virtuell dator kan det ta 15 minuter eller längre innan ändringarna träder i kraft och visas i portalen.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

När du kör ett redundanstest händer följande händelser:

- En kontroll av förhandskrav körs för att säkerställa att alla de villkor som krävs för redundans är på plats.
- Redundansen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten, skapas en återställningspunkt från data.
- En virtuell Azure-dator skapas med hjälp av de data som bearbetats i det föregående steget.

Kör redundanstestet i portalen:

1. På sidan för valvet, går du till **Skyddade objekt** > **Replikerade objekt**. Välj den virtuella datorn och välj sedan **Redundanstest**.
2. Välj en återställningspunkt som ska användas för redundans:
    - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data så den ger ett lågt mål för återställningstid (RTO).
    - **Senaste appkonsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
    - **Anpassat**: Välj en annan återställningspunkt.

3. I **Redundanstest** väljer du det Azure-målnätverk som de virtuella Azure-datorerna ska ansluta till efter redundans. Det här bör vara det nätverk du skapade i [Förbered Azure-resurser](#prepare-azure-resources).
4. Välj **OK** för att starta redundansväxlingen. Om du vill spåra förloppet klickar du på den virtuella datorn för visa dess egenskaper. Eller så kan du välja jobbet **Redundanstest** på sidan för ditt valv. Om du vill göra det, väljer du **Övervakning och rapporter** > **Jobb** >  **Site Recovery-jobb**.
5. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen. Om du vill visa den virtuella datorn, väljer du **Virtual Machines**. Kontrollera att den virtuella datorn har rätt storlek, att den är ansluten till rätt nätverk och att den är igång.
6. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure.
7. För att ta bort virtuella Azure-datorer som skapades under redundanstestningen klickar du på **Rensa redundanstestning** på återställningsplanen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.

I vissa scenarier kräver redundans ytterligare bearbetning. Bearbetningen tar 8 till 10 minuter att slutföra.

## <a name="migrate-to-azure"></a>Migrera till Azure

Kör en riktig redundansväxling för EC2-instanserna för att migrera dem till virtuella Azure-datorer:

1. I **Skyddade objekt** > **Replikerade objekt**, klickar du på AWS-instanser och därefter på **Redundans**.
2. I **Redundans** väljer du en **Återställningspunkt** att redundansväxla till. Välj den senaste återställningspunkten och starta redundansväxlingen. Du kan följa redundansförloppet på sidan **Jobb**.
1. Kontrollera att den virtuella datorn visas i **Replikerade objekt**.
2. Högerklicka på varje virtuell dator och välj sedan **Slutför migrering**. Det här gör följande:

   - Detta avslutar migreringsprocessen, stoppar replikeringen för virtuella datorer i AWS och stoppar Site Recovery-debitering för den virtuella datorn.
   - Det här steget rensar replikeringsdata. Men det raderar inte de migrerade virtuella datorerna. 

     ![Slutför migrering](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *Avbryt inte en redundansväxling som pågår*. Innan redundans startas stoppas den virtuella datorreplikeringen. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikera igen.  


## <a name="next-steps"></a>Nästa steg

I det här avsnittet har du lärt dig hur du migrerar AWS EC2-instanser till virtuella Azure-datorer. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudierna för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](../virtual-machines/windows/tutorial-manage-vm.md)
