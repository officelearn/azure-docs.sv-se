---
title: "Migrera virtuella datorer från AWS till Azure med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du migrerar virtuella datorer körs i Amazon Web Services (AWS) till Azure, med hjälp av Azure Site Recovery."
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
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6a10cbf1d3c8043ddcac9a725373fc30a3ecee20
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrera Amazon Web Services (AWS) virtuella datorer till Azure

Den här kursen lär du dig hur du migrerar Amazon Web Services (AWS) virtuella datorer (VM) till virtuella Azure-datorer med Site Recovery. När du migrerar EC2 instanser till Azure, VMsare behandlas som om de är fysiska, lokala datorer. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Förbered Azure-resurser
> * Förbereda AWS EC2 instanser för migrering
> * Distribuera en konfigurationsserver
> * Aktivera replikering för virtuella datorer
> * Testa redundansen för att se till att allt fungerar
> * Kör en redundansväxling till Azure

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prepare-azure-resources"></a>Förbered Azure-resurser 

Du behöver ha några resurser som är klar i Azure för de migrerade instanserna EC2 ska användas. Dessa inkluderar ett lagringskonto, ett valv och ett virtuellt nätverk.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Bilder av replikerade datorer lagras i Azure storage. Virtuella Azure-datorer skapas från lagring när växling från lokal till Azure.

1. I den [Azure-portalen](https://portal.azure.com) -menyn klickar du på **ny** -> **lagring** -> **lagringskonto**.
2. Ange ett namn för lagringskontot. Dessa självstudier använder vi namnet **awsmigrated2017**. Namnet måste vara unikt i Azure och vara mellan 3 och 24 tecken, endast siffror och gemener.
3. Behåll standardinställningarna för **distributionsmodellen**, **konto kind**, **prestanda**, och **säker överföring krävs**.
5. Välj standard **RA-GRS** för **replikering**.
6. Välj den prenumeration som du vill använda för den här självstudiekursen.
7. För **resursgruppen**väljer **Skapa nytt**. I det här exemplet använder vi **migrationRG** som namn.
8. Välj **Västeuropa** som platsen. 
9. Skapa lagringskontot genom att klicka på **Skapa**.

### <a name="create-a-vault"></a>Skapa ett valv

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret klickar du på **fler tjänster** , Sök efter och välj **Recovery Services-valv**.
2. Klicka på sidan Recovery Services-valv **+ Lägg till** i det övre vänstra hörnet på sidan.
3. För **namn**, typen *myVault*. 
4. För **prenumeration**, Välj lämplig prenumerationen.
4. För **resursgruppen**väljer **använda befintliga** och välj *migrationRG*. 
5. I **plats**väljer *Västeuropa*. 
5. För att snabbt komma åt det nya valvet från instrumentpanelen, Välj **fäst på instrumentpanelen**.
7. När du är klar klickar du på **skapa**.

Det nya valvet visas på den **instrumentpanelen** > **alla resurser**, och på primära **Recovery Services-valv** sidan.

### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

När den virtuella Azure-datorer skapas efter migreringen (failover), är de anslutna till nätverket.

1. I den [Azure-portalen](https://portal.azure.com), klickar du på **ny** > **nätverk** >
    **virtuellt nätverk**
3. För **namn**, typen *myMigrationNetwork*.
4. Låt standardvärdet för **adressutrymmet**.
5. För **prenumeration**, Välj lämplig prenumerationen.
6. För **resursgruppen**väljer **Använd befintliga** och välj *migrationRG* från listrutan.
7. För **plats**väljer **Västeuropa**. 
8. Lämnar du standardinställningarna för **undernät**, både den **namn** och **IP-intervall**.
9. Lämna **Tjänsteslutpunkter** inaktiverad.
10. När du är klar klickar du på **skapa**.


## <a name="prepare-the-ec2-instances"></a>Förbereda EC2-instanser

Du behöver en eller flera virtuella datorer som du vill migrera. Dessa EC2-instansen bör köra 64-bitarsversionen av Windows Server 2008 R2 SP1 eller senare, Windows Server 2012, Windows Server 2012 R2 eller Red Hat Enterprise Linux 6.7 (endast HVM virtualiserade instanser). Servern måste ha endast Citrix NV eller AWS NV drivrutiner. Kör RedHat NV drivrutiner molninstanser stöds inte.

Mobilitetstjänsten måste installeras på varje virtuell dator som du vill replikera. Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för den virtuella datorn. För automatisk installation måste du förbereda ett konto för EC2-instanser som Site Recovery för att komma åt den virtuella datorn.

Du kan använda en domän eller lokalt konto. För Linux virtuella datorer, måste kontot vara rot på källservern för Linux. För virtuella Windows-datorer, om du inte använder ett domänkonto, inaktivera kontroll av fjärråtkomst till användare på den lokala datorn:

  - I registret under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, lägga till DWORD-posten **LocalAccountTokenFilterPolicy** och ange värdet till 1.
    
Du måste också en separat EC2-instans som du kan använda som konfigurationsservern Site Recovery. Den här instansen måste köra Windows Server 2012 R2. 
    

## <a name="prepare-the-infrastructure"></a>Förbered infrastrukturen 

Välj på sidan företagsportal för ditt valv **Site Recovery** från den **komma igång** avsnittet och klicka sedan på **Förbered infrastrukturen**.

### <a name="1-protection-goal"></a>1 skyddsmål

Välj följande värden på den **Skyddsmål** sidan:
   
|    |  | 
|---------|-----------|
| Var finns dina datorer? | **Lokalt**|
| Om du vill replikera dina datorer? |**Till Azure**|
| Är dina datorer virtualiserade? | **Inte virtualiserade / andra**|

När du är klar klickar du på **OK** att flytta till nästa avsnitt.

### <a name="2-source-prepare"></a>Förbereda 2 källa 

På den **Förbered källa** klickar du på **+ konfigurationsservern**. 

1. Använd en EC2-instans som kör Windows Server 2012 R2 för att skapa en konfigurationsservern och registrera den med recovery-valvet.

2. Konfigurerar du proxyservern på EC2 instans VM som du använder som konfigurationsservern så att den kan komma åt den [webbadresserna](site-recovery-support-matrix-to-azure.md).

3. Hämta den [installationsprogram för Microsoft Azure Site Recovery enhetlig](http://aka.ms/unifiedinstaller_wus) program. Du kan ladda ned den till din lokala dator och sedan kopiera den över till den virtuella datorn du använder som konfigurationsservern.

4. Klicka på den **hämta** för att hämta valvregistreringsnyckeln. Kopiera den hämta filen över till den virtuella datorn du använder som konfigurationsservern.

5. Högerklicka på den virtuella datorn installer som du hämtade för den **installationsprogram för Microsoft Azure Site Recovery enhetlig** och välj **kör som administratör**. 

    1. I **innan du börjar**väljer **installera konfigurationsservern och processervern** och klicka sedan på **nästa**.
    2. I **licensvillkoren för programvara från tredje parts**väljer **acceptera licensavtalet från tredje part.** och klicka sedan på **nästa**.
    3. I **registrering**, klicka på Bläddra och navigera till där du placera valvnyckelfilen för registrering och klicka sedan på **nästa**.
    4. I **Internetinställningar**väljer **Anslut till Azure Site Recovery utan en proxyserver.** och klicka sedan på **nästa**.
    5. I den **Kravkontroll** sida, körs det kontrollerar om det finns flera artiklar. När den är klar klickar du på **nästa**. 
    6. I **MySQL Configuration**, lösenorden och klickar sedan på **nästa**.
    7. I **miljö information**väljer **nr**, behöver du inte skyddar VMware-datorer och klicka sedan på **nästa**.
    8. I **installera platsen**, klickar du på **nästa** att acceptera standardinställningarna.
    9. I **val av nätverk**, klickar du på **nästa** att acceptera standardinställningarna.
    10. I **sammanfattning** klickar du på **installera**.
    11. **Installationsförlopp** visar du information om under installationen. När den är klar klickar du på **Slutför**. Du får ett popup-fönster om du behöver en möjliga omstart, klickar på **OK**. Du också få ett popup-fönster om anslutningen för Konfigurationsserverns lösenfras, kopiera lösenfrasen till Urklipp och spara den någonstans säker.
    
6. På den virtuella datorn kör **cspsconfigtool.exe** att skapa en eller flera hanteringskonton på konfigurationsservern. Se till att hanteringskontona har administratörsbehörighet på de EC2-instanser som du vill migrera. 

När du är klar konfigurerar konfigurationsservern gå tillbaka till portalen och välj den server som du nyss skapade för **konfigurationsservern** och på *OK** att gå vidare till steg 3 mål Förbered.

### <a name="3-target-prepare"></a>Förbereda 3 mål 

I det här avsnittet anger du information om resurserna som du skapade när du har gått igenom de [förbereda Azure-resurser](#prepare-azure-resources) tidigare i den här kursen.

1. I **prenumeration**, Välj den Azure-prenumeration som du använde för den [förbereda Azure](tutorial-prepare-azure.md) kursen.
2. Välj **Resource Manager** som distributionsmodell.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk. De resurser som du skapade när du har gått igenom bör vara den [förbereda Azure-resurser](#prepare-azure-resources) tidigare i den här självstudiekursen
4. Klicka på **OK** när du är klar.


### <a name="4-replication-settings-prepare"></a>4 replikeringsinställningarna Förbered 

Du måste skapa en replikeringsprincip innan du kan aktivera replikering

1. Klicka på **+ replikera och associera**.
2. I **namn**, typen **myReplicationPolicy**.
3. Lämna resten av standardinställningarna och klickar på **OK** vill skapa principen. Ny princip associeras automatiskt med konfigurationsservern. 

### <a name="5-deployment-planning-select"></a>5 distributionen planerar väljer 

I **har du slutfört distributionsplanering?**väljer **jag gör det senare** från listrutan och klicka sedan på **OK**.

När du har alla utfört alla 5 delar av **Förbered infrastruktur**, klickar du på **OK**.


## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering för varje virtuell dator som du vill migrera. När replikeringen är aktiverad, installeras Site Recovery mobilitetstjänsten automatiskt. 

1. Öppna [Azure-portalen](htts://portal.azure.com).
1. På sidan för ditt valv under **komma igång**, klickar du på **Site Recovery**.
2. Under **för lokala datorer och virtuella Azure-datorer**, klickar du på **steg 1:Replicate programmet**. Slutför guidesidor med följande information och klickar på **OK** på varje sida när du är klar:
    - 1 källa konfigurera:
      
    |  |  |
    |-----|-----|
    | Källa: | **Lokalt**|
    | Källplats:| Namnet på din server EC2 configuration-instans.|
    |Typ av dator: | **Fysiska datorer**|
    | Processervern: | Välj konfigurationsservern från den nedrullningsbara listan.|
    
    - Konfigurera 2 mål
        
    |  |  |
    |-----|-----|
    | Mål: | Låt standardvärdet.|
    | Prenumerationen: | Välj den prenumeration som du har använt.|
    | Postredundans resursgrupp:| Använda den resursgrupp som du skapade i den [förbereda Azure-resurser](#prepare-azure-resources) avsnitt.|
    | Distributionsmodell för postredundans: | Välj **Resource Manager**|
    | Storage-konto: | Välj lagring kontot du skapade i den [förbereda Azure-resurser](#prepare-azure-resources) avsnitt.|
    | Azure-nätverk: | Välj **konfigurera nu för valda datorer**|
    | Postredundans Azure-nätverk: | Välj vilket nätverk som du skapade i den [förbereda Azure-resurser](#prepare-azure-resources) avsnitt.|
    | Undernät: | Välj den **standard** från listrutan.|
    
    - Välj 3 fysiska datorer
        
        Klicka på **+ fysisk dator** och ange sedan den **namn**, **IP-adress** och **OS-typen** på EC2-instans som du vill migrera och Klicka på **OK**.
        
    - Konfigurera egenskaper för 4 egenskaper
        
        Välj det konto som du skapade på konfigurationsservern från listrutan och klicka på **OK**.
        
    - 5 replikering inställningarna konfigurerar replikeringsinställningar
    
        Kontrollera att den replikeringsprincip som valts i listrutan är **myReplicationPolicy** och klicka sedan på **OK**.
        
3. När guiden är klar klickar du på **Aktivera replikering**.
        

Du kan följa förloppet för den **Aktivera skydd** jobb **övervakning och rapporter** > **jobb** > **Site Recovery-jobb** . När jobbet **Slutför skydd** har körts är datorn redo för redundans.        
        
När du aktiverar replikering för en virtuell dator kan det ta 15 minuter eller längre ändringarna träder i kraft och visas på portalen.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

När du kör ett redundanstest händer följande:

1. En kravkontroll körs ska du kontrollera att alla de villkor som krävs för växling vid fel är på plats.
2. Redundans bearbetar data, så att du kan skapa en Azure VM. Om väljer skapas den senaste återställningspunkten, en återställningspunkt från data.
3. En Azure VM skapas med hjälp av de data som behandlas i föregående steg.

I portalen kör redundanstestet på följande sätt:

1. På sidan för ditt valv, gå till **skyddade objekt** > **replikerade objekt**> klickar du på den virtuella datorn > **+ testa redundans**.

2. Välj en återställningspunkt som ska användas för redundans:
    - **Senaste bearbetas**: växlar den virtuella datorn till den senaste återställningspunkten som bearbetats av Site Recovery. Tidsstämpeln visas. Med det här alternativet läggs ingen tid bearbetning av data, så att den ger en låga RTO (mål).
    - **Senaste programkonsekventa**: det här alternativet flyttas över alla virtuella datorer till den senaste programkonsekventa återställningspunkten. Tidsstämpeln visas.
    - **Anpassad**: Välj någon annan återställningspunkt.
3. I **Redundanstest**, Välj det Azure ska vara anslutet nätverk till vilka virtuella Azure-datorer efter redundansväxlingen. Detta bör vara det nätverk som du skapade i den [förbereda Azure-resurser](#prepare-azure-resources) avsnitt.
4. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på den **Redundanstest** jobb på sidan för ditt valv i **övervakning och rapporter** > **jobb** >
   **plats Återställningsjobb**.
5. När redundansväxlingen är klar repliken virtuella Azure-datorn visas i Azure-portalen > **virtuella datorer**. Kontrollera att den virtuella datorn är rätt storlek, att den är ansluten till rätt nätverk och att den körs.
6. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure.
7. Ta bort Azure virtuella datorer som skapades under redundanstestningen, klicka på **Rensa redundanstestet** på återställningsplanen. I **anteckningar**, registrera och spara observationer från redundanstestningen.

I vissa scenarier kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter för att slutföra. 


## <a name="migrate-to-azure"></a>Migrera till Azure

Kör en riktig redundansväxling för EC2-instanser att migrera dem till virtuella Azure-datorer. 

1. I **skyddade objekt** > **replikerade objekt** klickar du på AWS-instanser > **redundans**.
2. I **redundans** väljer en **återställningspunkt** ska gå över till. Välj den senaste återställningspunkten.
3. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery att göra en avstängning av virtuella källdatorer innan växling vid fel. Redundans fortsätter även om avstängning misslyckas. Du kan följa förloppet för växling vid fel på den **jobb** sidan.
4. Kontrollera att den virtuella datorn visas i **replikerade objekt**. 
5. Högerklicka på varje virtuell dator > **slutföra migreringen**. Detta avslutar migreringsprocessen, stoppar replikering för AWS-VM och stoppar Site Recovery-faktureringen för den virtuella datorn.

    ![Slutföra migrering](./media/tutorial-migrate-aws-to-azure/complete-migration.png)

> [!WARNING]
> **Inte avbryta en växling pågår**: innan redundans startas VM replikeringen har stoppats. Om du avbryter en växling pågår, stoppar för växling vid fel, men den virtuella datorn inte kommer att replikeras igen.  


    

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du migrerar AWS EC2 instanser till virtuella Azure-datorer i det här avsnittet. Om du vill veta mer om virtuella Azure-datorer kan du fortsätta att självstudierna för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](../virtual-machines/windows/tutorial-manage-vm.md)
