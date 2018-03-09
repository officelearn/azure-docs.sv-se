---
title: "Skyddar virtuella datorer från Azure till en lokal plats | Microsoft Docs"
description: "Du kan starta en återställning efter fel om du vill flytta virtuella datorer lokala efter redundans för virtuella datorer till Azure. Lär dig att skydda innan en återställning efter fel."
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanaki
ms.openlocfilehash: cd5e53b49a850acf851e8351b5e14e2993176435
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="reprotect-machines-from-azure-to-an-on-premises-site"></a>Skyddar datorer från Azure till en lokal plats

Efter [redundans](site-recovery-failover.md) av lokala virtuella VMware-datorer eller fysiska servrar till Azure, är det första steget i misslyckas tillbaka till den lokala platsen att skydda den virtuella Azure-datorer som skapats under växling vid fel. Den här artikeln beskriver hur du gör detta. 

Titta på följande videoklipp om hur du växla över från Azure till en lokal plats för en snabb överblick.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-start"></a>Innan du börjar

Om du använder en mall för att skapa virtuella datorer, se till att har varje virtuell dator sin egen UUID för diskarna. Om den lokala virtuella UUID hamnar i konflikt med som huvudmålservern eftersom båda har skapats från samma mall, misslyckas återaktivera skydd. Distribuera en annan huvudmålserver som inte har skapats från samma mall.
- Om du vill växla tillbaka till en alternativ vCenter kontrollerar du att din nya vCenter och huvudmålservern identifieras. Ett typiskt symtom är att datastores inte är tillgänglig eller visas i den **skyddar** dialogrutan.
- Om du vill replikera tillbaka till lokala behöver du en princip för återställning efter fel. Den här principen skapas automatiskt när du skapar en princip för riktning framåt. Tänk på följande:
    - Den här principen hämtar automatiskt som är kopplade till konfigurationsservern under skapandet.
    - Den här principen kan inte redigeras.
    - Ange värdena för principen (RPO tröskelvärde = 15 minuter, Kvarhållningstid för återställningspunkten = 24 timmar, frekvensen för App konsekvenskontroll ögonblicksbilder = 60 minuter)
- Konfigurationsservern lokalt ska köras under återaktivera skydd och återställning efter fel och anslutet.
- Om en vCenter-servern hanterar de virtuella datorerna som du ska återställas se till att du har den [nödvändiga behörigheter](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) för identifiering av virtuella datorer på vCenter-servrar.
- Ta bort ögonblicksbilder på huvudmålservern innan du skyddar. Om det finns ögonblicksbilder på den lokala huvudmålservern eller den virtuella datorn, misslyckas återaktivera skydd. Ögonblicksbilder på den virtuella datorn slås automatiskt samman under ett Skapa nytt jobb.
- Alla virtuella datorer i en replikeringsgrupp ska vara av samma typ av operativsystem (alla fönster eller alla Linux). En replikeringsgrupp med blandade operativsystem stöds för närvarande inte för att skydda igen och återställning till lokalt. Detta beror på att huvudmålservern ska vara av samma operativsystem som den virtuella datorn och alla virtuella datorer i en replikeringsgrupp ska ha samma huvudmålservern. 
- En konfigurationsservern är lokala när du växlar tillbaka. Under återställning efter fel, måste den virtuella datorn finns i server-konfigurationsdatabasen. Återställning är annars misslyckas. Kontrollera att du vidtar regelbundna schemalagda säkerhetskopieringar serverns konfiguration. Om det finns en katastrof, återställa servern med samma IP-adress så att återställningen fungerar.
- Återaktivera skydd och återställning kräver en plats-till-plats VPN-anslutning att replikera data. Ange nätverket så att misslyckades över virtuella datorer i Azure kan nå (ping) konfigurationen av lokal server. Du kan också distribuera en processerver i Azure-nätverk för den virtuella datorn kunde inte över. Den här processen servern ska också kunna kommunicera med konfigurationsservern lokalt.
- Kontrollera att du öppnar följande portar för redundans och återställning efter fel.

    ![Portar för redundans och återställning efter fel](./media/vmware-azure-reprotect/failover-failback.png)

## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processerver i Azure

Du måste kanske en processerver i Azure innan du växlar tillbaka till den lokala platsen:
- Processervern] tar emot data från den skyddade virtuella datorn i Azure och skickar data till den lokala platsen.
- Ett nätverk med låg latens krävs mellan processervern och den skyddade virtuella datorn. I allmänhet måste du överväga svarstid när du bestämmer om du behöver en processerver i Azure:
    - Om du har en ExpressRoute-anslutning som konfigurerar kan du använda en lokal processerver för att skicka data eftersom fördröjning mellan den virtuella datorn och processervern är låg.
    - Om du har bara en S2S-VPN, rekommenderar vi dock distribution av processerver i Azure.
    - Vi rekommenderar att du använder en för Azure-baserade processerver vid återställning. Replikeringsprestanda är högre om processervern ligger närmare den replikerande virtuella datorn (misslyckades över dator i Azure). Du kan använda lokala processervern tillsammans med ExpressRoute för en--konceptbevis, med privat peering.

Distribuera på följande sätt:

1. Om du behöver distribuera en processerver i Azure följer [dessa anvisningar](vmware-azure-set-up-process-server-azure.md)
2. Virtuella Azure-datorer skickas replikeringsdata till processervern. Konfigurera nätverk så att den virtuella Azure-datorer kan nå processervern.
3. Kom ihåg att replikering från Azure till lokala kan inträffa endast via S2S-VPN eller över privat peering i ExpressRoute-nätverk. Se till att tillräckligt med bandbredd är tillgänglig över nätverkskanalen.

## <a name="deploy-a-separate-master-target-server"></a>Distribuera en separat huvudmålserver

Huvudmålservern tar emot data vid återställning efter fel. Som standard körs huvudmålservern på konfigurationsservern lokalt. Beroende på mängden misslyckades tillbaka trafik, kan du dock behöver skapa en separat huvudmålserver för återställning efter fel. Här är hur man skapar en:

    * [Skapa en Linux-huvudmålserver](vmware-azure-install-linux-master-target.md) för återställning av virtuella Linux-datorer. Detta krävs.
    * Du kan också skapa en separat huvudmålserver för återställning av virtuell Windows-dator. Om du vill göra det kör Unified installationen igen och välja för att skapa en huvudmålserver. [Läs mer](physical-azure-set-up-source.md#run-azure-site-recovery-unified-setup).

När du har skapat en huvudmålserver kan du göra följande:

- Om den virtuella datorn är finns lokalt på vCenter-servern, behöver åtkomst till den lokala virtuella VMDK huvudmålservern. Åtkomst krävs för att skriva replikerade data till den virtuella datorns diskar. Se till att den lokala virtuella datalagret har monterats på den huvudmålservern värd med läs-/ skrivbehörighet.
- Om den virtuella datorn inte finns lokalt på vCenter server måste Site Recovery-tjänsten skapa en ny virtuell dator under återaktivera skydd. Den här virtuella datorn skapas på ESX-värd som du skapar huvudmålservern. Välj ESX-värd noggrant, så att återställning av virtuell dator skapas på värden som du vill använda.
- Du kan använda Storage vMotion för huvudmålservern server *. Detta kan orsaka återställning misslyckas. Den virtuella datorn kan inte starta eftersom de inte är tillgängliga för den. Exkludera master målservrarna vMotion-listan för att förhindra det här problemet.
- Om ett huvudmål genomgår en Storage vMotion aktivitet efter återaktivera skydd, migrera skyddade virtuella datorer diskar som är kopplade till huvudmålservern till målet för uppgiften vMotion. Om du försöker att återställas efter detta misslyckas vill koppla bort på disken eftersom diskarna inte hittas. Det blir svårt att hitta diskarna i storage-konton. Du måste du hittar dem manuellt och koppla dem till den virtuella datorn. Efter att kan den lokala virtuella datorn startas.
- Lägga till en kvarhållningsenhetens i din befintliga Windows-huvudmålservern. Lägg till en ny disk och formatera hårddisken. Kvarhållningsenhetens används för att stoppa punkterna i tid när den virtuella datorn replikeras tillbaka till den lokala platsen. Följande är några villkor för en enhet för kvarhållning. Utan dessa villkor visas enheten inte för huvudmålservern.
    - Volymen används inte i något annat syfte, till exempel ett mål för replikering.
    - Volymen är inte i låsläge.
    - Volymen är inte en cachevolym. Huvudmålservern-installationen får inte finnas på den volymen. Anpassad installationsvolymen för process-servern och master målet är inte berättigad till en kvarhållningsvolymen. Om processervern och huvudmålservern är installerade på en volym är är volymen en cachevolym på Huvudmålet.
    - Vilken typ av filsystem på volymen är inte FAT eller FAT32.
    - Volymens kapacitet är noll.
    - Standard-kvarhållningsvolymen för Windows är R-volym.
    - Standard-kvarhållningsvolymen för Linux är /mnt/retention.
- Du måste lägga till en ny enhet om du använder en befintlig process serverkonfiguration/server-dator eller en skala eller en process server eller huvudtjänsten server måldatorn. Den nya enheten ska kraven ovan. Om kvarhållningsenhetens inte finns, visas den inte i listrutan markeringen på portalen. När du lägger till en enhet till lokala huvudmålservern tar upp till 15 minuter för enheten visas i markeringen på portalen. Du kan också uppdatera konfigurationsservern om enheten inte visas efter 15 minuter.
- Installera VMware-verktygen på huvudmålservern. Datastores på ESXi-värd på huvudmålservern kan inte identifieras utan VMware-verktyg.
- Ange den `disk.EnableUUID=true` i konfigurationsparametrar för huvudmålservern virtuell dator i VMware. Om den här raden inte finns, kan du lägga till den. Den här inställningen krävs för att ge en konsekvent UUID till den virtuella disken (VMDK) så att den monterar korrekt.
- Huvudmålservern bör ha minst en VMFS datastore ansluten. Om det finns ingen, den **Datastore** indata på sidan Skapa nytt är tomt, och du kan inte fortsätta.
- Huvudmålservern kan inte ha ögonblicksbilder på diskarna. Om det finns ögonblicksbilder, misslyckas återaktivera skydd och återställning efter fel.
- Huvudmålservern kan inte ha en Paravirtual SCSI-styrenhet. Kontrollanten kan bara finnas en domänkontrollant för en LSI Logic. Utan en domänkontrollant för en LSI Logic återaktivera skyddet fungerar inte.
- Huvudmålservern kan ha atmst 60 diskar som är anslutna till den i en viss instans. Om antalet virtuella datorer att återaktivera skyddet till den lokala huvudmålservern har summan totala antal diskar som är mer än 60 och sedan reprotects till huvudmålservern startar misslyckas. Se till att du har tillräckligt med master riktade disken platser eller distribuera ytterligare huvudmålservern servrar.
    

## <a name="enable-reprotection"></a>Aktivera återaktivera skydd

Efter att en virtuell dator startar i Azure, tar det lite tid för att agenten ska registrera tillbaka till konfigurationsservern (upp till 15 minuter). Under denna tid kan du längre inte att skydda och ett felmeddelande som anger att agenten inte är installerad. Om det händer, Vänta några minuter och försök sedan att återaktivera skyddet igen.


1. I **valvet** > **replikerade objekt**, högerklicka på den virtuella datorn har växlas över och välj sedan **skydda igen**. Du kan också klicka på datorn och välja **skydda igen** från knapparna.
2. Kontrollera riktningen för skydd, **Azure till lokala**, har redan valts.
3. I **Huvudmålserver** och **Processervern**, Välj den lokala huvudmålservern och processervern.
4. För **Datastore**, markera databasen som du vill återställa den diskar lokalt. Det här alternativet används när den lokala virtuella datorn tas bort och du behöver skapa nya diskar. Det här alternativet ignoreras om diskarna finns redan, men du måste ange ett värde.
5. Välj kvarhållningsenhetens.
6. Failback-principen väljs automatiskt.
7. Klicka på **OK** ska börja återaktivera skyddet. Ett jobb börjar att replikera den virtuella datorn från Azure till den lokala platsen. Du kan följa förloppet på fliken **Jobb**. När det återaktivera skyddet lyckas, kommer den virtuella datorn anger ett skyddat läge.

Tänk på följande:
- Om du vill återställa till en alternativ plats (när den lokala virtuella datorn tas bort), Välj kvarhållningsenhetens och datalager som är konfigurerade för huvudmålservern. När du växlar tillbaka till den lokala platsen VMware-datorer i skyddsplan återställning efter fel att använda samma datalager som huvudmålservern. En ny virtuell dator skapas sedan i vCenter.
- Om du vill återställa den virtuella datorn i Azure för en befintlig lokal virtuell dator kan du montera den lokala virtuella datastores med läs-/ skrivbehörighet på huvudmålservern serverns ESXi-värd.
    ![Skydda dialogrutan igen](./media/vmware-azure-reprotect/reprotectinputs.png)

- Du kan även skydda igen på nivån i en återställningsplan. En replikeringsgrupp kan att återaktivera skyddet endast via en återställningsplan. När du skyddar med hjälp av en återställningsplan måste du ange värden för varje skyddad dator.
- Använd samma huvudmålserver för att återaktivera skyddet av en replikeringsgrupp. Om du använder en annan huvudmålserver för att återaktivera skyddet av en replikeringsgrupp, får inte servern en gemensam tidpunkt.
- Den lokala virtuella datorn stängs av under återaktivera skydd. Detta hjälper att säkerställa datakonsekvens vid replikeringen. Aktivera inte den virtuella datorn efter att återaktivera skyddet har slutförts.



## <a name="common-issues"></a>Vanliga problem


- Site Recovery stöder för närvarande, misslyckas tillbaka endast till en virtuell dator file system (VMFS) eller ett virtuellt SAN-datalagret. Ett NFS-datalager stöds inte. På grund av den här begränsningen kan ange datastore markeringen på skyddar skärmen är tom vid NFS datastores, eller så visar du databasen men misslyckas under jobbet. Om du vill växla tillbaka du växlar tillbaka till den och skapa ett datalager VMFS lokalt. Den här återställning kommer en fullständig hämtning av VMDK.
- Om du utför en skrivskyddade användaren vCenter identifiering och skydda virtuella datorer, skydd lyckas och redundans fungerar. Under återaktivera skydd misslyckas redundans eftersom datastores inte kan identifieras. Ett symtom är att datastores inte visas under återaktivera skydd. Lös problemet, kan du uppdatera vCenter-autentiseringsuppgifter med ett lämpligt konto som har behörighet och försök igen. 
- När du återställas på en virtuell Linux-dator och köra den lokalt, ser du att paketet Network Manager har avinstallerats från datorn. Den här avinstallationen beror Nätverkshanteraren paketet tas bort när den virtuella datorn återställs i Azure.
- När en virtuell Linux-dator är konfigurerad med en statisk IP-adress och växlas över till Azure, förvärvas IP-adress från DHCP. När du växla över till den lokala fortsätter den virtuella datorn att använda DHCP för att få IP-adress. Manuellt logga in på datorn och ange IP-adressen till en statisk adress om det behövs. En virtuell Windows-dator kan hämta den statiska IP-Adressen igen.
- Om du använder ESXi 5.5 gratisversion eller vSphere 6 Hypervisor gratisversion redundans lyckas, men lyckas inte återställning efter fel. Uppgradera till utvärderingslicens för båda programmen om du vill aktivera återställning efter fel.
- Om du inte kan nå konfigurationsservern från processervern, kan du använda Telnet för att kontrollera anslutning till konfigurationsservern på port 443. Du kan även försöka att pinga konfigurationsservern från processervern. En processerver bör också ha ett pulsslag när den är ansluten till konfigurationsservern.
- En Windows Server 2008 R2 SP1-server som skyddas som en fysisk lokal server inte kan vara misslyckad tillbaka från Azure till en lokal plats.
- Du kan inte underkänna tillbaka under följande omständigheter:
    - Du har migrerat datorer till Azure. [Läs mer](migrate-overview.md#what-do-we-mean-by-migration).
    - Du kan flytta en virtuell dator till en annan resursgrupp.
    - Du har tagit bort den virtuella Azure-datorn.
    - Du har inaktiverat skydd för den virtuella datorn.
    - Du har skapat den virtuella datorn manuellt i Azure. Datorn ska har ursprungligen skyddade lokalt och växlas över till Azure innan du skyddar.
    - Du kan bara växla till en ESXi-värd. Det går inte att återställning av virtuella VMware-datorer eller fysiska servrar med Hyper-V-värdar, fysiska datorer eller VMware-arbetsstationer.


## <a name="next-steps"></a>Nästa steg

Efter att den virtuella datorn har angett ett skyddat läge, kan du [starta en återställning efter fel](vmware-azure-failback.md). Återställningen kommer att stänga av den virtuella datorn i Azure och starta den virtuella datorn lokalt. Förvänta dig vissa avbrott för programmet. Välj en tid för återställning efter fel när programmet kan tolerera driftstopp.

