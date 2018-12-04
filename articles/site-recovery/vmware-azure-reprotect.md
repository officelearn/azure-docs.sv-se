---
title: Återaktivera skyddet av virtuella datorer från Azure till en lokal plats under haveriberedskap för virtuella VMware-datorer och fysiska servrar | Microsoft Docs
description: Lär dig hur du växlar tillbaka från Azure till den lokala platsen efter redundansväxlingen till Azure under haveriberedskap för virtuella VMware-datorer och fysiska servrar.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: e965848b0c3c009444762dafdf42acc080b6915e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834976"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Skydda igen och misslyckas tillbaka datorer till en lokal plats efter redundansväxlingen till Azure

Efter [redundans](site-recovery-failover.md) av lokala virtuella VMware-datorer eller fysiska servrar till Azure, är det första steget i misslyckas till den lokala webbplatsen att återaktivera skyddet av virtuella Azure-datorer som har skapats under redundansväxlingen. Den här artikeln beskriver hur du gör detta. 

Titta på följande videoklipp om hur du redundansväxlar från Azure till en lokal plats för en snabb överblick.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Innan du börjar

Om du använder en mall för att skapa dina virtuella datorer, se till att varje virtuell dator har sin egen UUID för diskar. Om den lokala virtuella datorns UUID är i konflikt med UUID för Huvudmålet eftersom båda skapades från samma mall, misslyckas återaktiveringen av skyddet. Distribuera en annan huvudmålserver som inte har skapats från samma mall. Notera följande information:
- Om du vill växla tillbaka till en annan vCenter, se till att identifieras din nya vCenter och huvudmålservern. Ett typiskt symtom är att datalagringen inte är tillgänglig eller visas inte i den **återaktivera skyddet av** dialogrutan.
- Om du vill replikera tillbaka till den lokala, behöver du en princip för återställning efter fel. Den här principen skapas automatiskt när du skapar en princip för framåt. Notera följande information:
    - Den här principen är auto som är associerade med konfigurationsservern under skapandet.
    - Den här principen är inte kan redigeras.
    - Ange värden för principen är (tröskelvärde för Replikeringspunktmål = 15 minuter, återställningspunkt för kvarhållning = 24 timmar, App konsekvens Ögonblicksbildsfrekvens = 60 minuter).
- Under återaktivering av skydd och återställning efter fel kan vara den lokala konfigurationsservern körs och är ansluten.
- Om en vCenter-server hanterar de virtuella datorerna som du kan inte återställa kan du se till att du har den [nödvändiga behörigheter](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) för identifiering av virtuella datorer på vCenter-servrar.
- Ta bort ögonblicksbilder på huvudmålservern innan du återaktivera skyddet. Om ögonblicksbilder finns på den lokala huvudmålservern eller på den virtuella datorn, misslyckas återaktiveringen av skyddet. Ögonblicksbilder på den virtuella datorn slås automatiskt samman under ett jobb för återaktivering av skydd.
- Alla virtuella datorer i en replikeringsgrupp måste vara av samma typ av operativsystem (alla Windows eller alla Linux). En replikeringsgrupp med blandade operativsystem för närvarande stöds inte för återaktivering av skydd och återställning efter fel till den lokala. Det beror på att Huvudmålet måste vara av samma operativsystem som den virtuella datorn. Alla virtuella datorer i en replikeringsgrupp måste ha samma Huvudmålet. 
- En konfigurationsserver är lokala när du växlar tillbaka. Den virtuella datorn måste finnas i configuration server-databas under återställningen. Annars är återställningen misslyckades. Kontrollera att du gör regelbundna schemalagda säkerhetskopieringar av konfigurationsservern. Om en katastrof kan du återställa servern med samma IP-adress så att återställningen fungerar.
- Återaktivering av skydd och återställning efter fel behöver du en plats-till-plats (S2S) VPN-anslutning att replikera data. Ange nätverket så att de redundansväxlade virtuella datorerna i Azure kan nå (ping) konfigurationen av den lokala servern. Du kanske också vill distribuera en processerver i Azure-nätverket för den redundansväxlade virtuella datorn. Den här processervern måste också kunna kommunicera med den lokala konfigurationsservern.
- Kontrollera att du öppna följande portar för redundans och återställning efter fel:

    ![Portar för redundans och återställning efter fel](./media/vmware-azure-reprotect/failover-failback.png)

- Läsa alla de [krav för portar och URL: en lista över tillåtna](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processerver i Azure

Du kanske måste en processerver i Azure innan du växlar tillbaka till din lokala plats:
- Processervern tar emot data från den skyddade virtuella datorn i Azure och skickar data till den lokala platsen.
- Ett nätverk med låg latens krävs mellan processervern och den skyddade virtuella datorn. I allmänhet måste du tänka på svarstid när du bestämmer om du behöver en processerver i Azure:
    - Om du har en Azure ExpressRoute-anslutning som ställer in kan du kan använda en lokal processerver för att skicka data eftersom fördröjningen mellan den virtuella datorn och processervern är låg.
    - Om du har endast en S2S-VPN, rekommenderar vi dock distribuerar processerver i Azure.
    - Vi rekommenderar att du använder en Azure-baserad processerver under återställning efter fel. Replikeringsprestandan är högre om processervern ligger närmast den replikerande virtuella datorn (den redundansväxlade datorn i Azure). För ett konceptbevis kan du använda lokala processervern och ExpressRoute med privat peering.

För att distribuera en processerver i Azure:

1. Om du vill distribuera en processerver i Azure kan du läsa [ställa in en processerver i Azure för återställning efter fel](vmware-azure-set-up-process-server-azure.md).
2. Virtuella Azure-datorer skickar replikeringsdata till processervern. Konfigurera nätverk så att virtuella Azure-datorer kan nå processervern.
3. Kom ihåg att replikering från Azure till lokalt kan inträffa endast via S2S-VPN eller via nätverket ExpressRoute privat peering. Se till att tillräcklig bandbredd är tillgänglig över nätverkskanalen.

## <a name="deploy-a-separate-master-target-server"></a>Distribuera en separat huvudmålserver

Huvudmålservern tar emot data vid återställning efter fel. Som standard körs huvudmålservern på konfigurationsservern lokalt. Beroende på vilken trafikvolym misslyckades tillbaka, kan du dock behöva skapa en separat huvudmålserver för återställning efter fel. Här är hur man skapar en:

* [Skapa en Linux-huvudmålserver](vmware-azure-install-linux-master-target.md) för återställning efter fel för virtuella Linux-datorer. Detta krävs.
* Du kan också skapa en separat huvudmålserver för återställning efter fel för Windows-VM. Att göra detta, köra enhetliga installationsprogrammet igen och välja för att skapa en huvudmålserver. [Läs mer](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers).

När du skapar en huvudmålserver kan du utföra följande uppgifter:

- Om den virtuella datorn är finns lokalt på vCenter-servern, måste huvudmålservern ha åtkomst till den lokala virtuella datorns virtuella diskar (VMDK)-fil. Åtkomst krävs för att skriva replikerade data till den virtuella datorns diskar. Se till att den lokala virtuella datorns datalagret har monterats på huvudmålserverns värd med läs-/ skrivbehörighet.
- Om den virtuella datorn inte finns lokalt på vCenter-servern, måste Site Recovery-tjänsten skapa en ny virtuell dator vid återaktiveringen av skyddet. Den här virtuella datorn skapas på ESX-värden som du skapade Huvudmålet. Välj ESX-värden noggrant, så att den virtuella datorn för återställning efter fel skapas på den värd som du vill.
- Du kan inte använda Storage vMotion för huvudmålservern. Använda Storage vMotion för huvudmålservern kan det leda till att återställningen misslyckas. Den virtuella datorn kan inte starta eftersom diskarna inte är tillgängliga för den. Om du vill förhindra att detta inträffar genom att exkludera huvudmålservrarna från vMotion-listan.
- Om ett huvudmål genomgår en Storage vMotion-uppgift när återaktiveringen av skyddet, migrera skyddade virtuella diskar som är kopplade till huvudmålservern till målet för uppgiften vMotion. Om du försöker att återställas efter detta misslyckas vill koppla bort diskens eftersom diskarna inte hittas. Sedan blir det svårt att hitta diskarna i dina lagringskonton. Du måste du hitta dem manuellt och koppla dem till den virtuella datorn. Efter det kan lokala virtuella datorn startas.
- Lägg till en kvarhållningsenhet i din befintliga Windows-huvudmålservern. Lägg till en ny disk och formatera hårddisken. Kvarhållningsenhetens används för att stoppa poäng tidpunkt när den virtuella datorn replikeras tillbaka till den lokala platsen. Här följer några villkor för en kvarhållningsenhet. Om dessa villkor inte uppfylls, enheten är inte i listan för huvudmålservern:
    - Volymen används inte i något annat syfte, till exempel målet för replikering.
    - Volymen finns inte i låsläge.
    - Volymen är inte en cachevolym. Installationen av huvudmålservern kan inte finnas på volymen. Anpassad installationsvolym för process server och master target är inte berättigad till en kvarhållningsvolym. När processervern och huvudmålservern installeras på en volym, är volymen en cachevolym för Huvudmålet.
    - Typ av filsystem på volymen inte FAT eller FAT32.
    - Volymens kapacitet är noll.
    - Standardkvarhållningsvolymen för Windows är R volym.
    - Standardkvarhållningsvolymen för Linux är/mnt/retention.
- Du måste lägga till en ny enhet om du använder en befintlig process server/configuration server-dator eller en skala eller process server/master target-serverdator. Den nya enheten måste uppfylla kraven på föregående. Om kvarhållningsenheten inte finns, kan den inte visas i listrutan val i portalen. När du lägger till en enhet till den lokala huvudmålservern tar upp till 15 minuter för enheten som visas i urvalet på portalen. Du kan också uppdatera konfigurationsservern om enheten inte visas efter 15 minuter.
- Installera VMware-verktyg eller verktyg för öppen virtuella datorer på huvudmålservern. Datalagringen på huvudmålserverns ESXi-värden utan att verktygen kan inte identifieras.
- Ange den `disk.EnableUUID=true` i konfigurationsparametrar för huvudmålservern virtuell dator i VMware. Om den här raden inte finns kan du lägga till den. Den här inställningen krävs för att tillhandahålla en konsekvent UUID för VMDK så att den monterar korrekt.
- ESX-värden där huvudmålservern skapas måste ha file system (VMFS) av minst en virtuell dator-datalager som kopplade till den. Om inga VMFS datalager kopplas de **datalager** indata på sidan återaktivering av skydd är tom och du kan inte fortsätta.
- Huvudmålservern kan inte ha ögonblicksbilder på diskarna. Om det finns ögonblicksbilder, misslyckas återaktivering av skydd och återställning efter fel.
- Huvudmålservern kan inte ha en Paravirtual SCSI-styrenhet. Kontrollanten kan bara vara en LSI Logic-styrenhet. Utan en LSI Logic-styrenhet misslyckas återaktiveringen av skyddet.
- För alla förekomster kan Huvudmålet ha högst 60 diskar som är anslutna till den. Om antalet virtuella datorer att återaktivera skyddet för den lokala huvudmålservern har fler än det totala antalet 60 diskar reprotects till Huvudmålet börja misslyckas. Kontrollera att du har tillräckligt med master rikta disk platser, eller distribuera ytterligare huvudmålservrar.
    

## <a name="enable-reprotection"></a>Aktivera återaktivering av skydd

När en virtuell dator startas i Azure, tar det lite tid för att agenten ska registrera tillbaka till konfigurationsservern (upp till 15 minuter). Du kan inte skydda igen under denna tid och ett felmeddelande som anger att agenten inte är installerad. Om detta inträffar kan du vänta några minuter och försök sedan återaktiveringen av skyddet igen:


1. Välj **Vault** > **replikerade objekt**. Högerklicka på den virtuella datorn som redundansväxlas och välj sedan **skydda igen**. Eller, från kommandoknappar, Välj datorn, och välj sedan **skydda igen**.
2. Kontrollera att den **Azure till lokala** skyddets riktning har valts.
3. I **Huvudmålservern** och **Processervern**, Välj den lokala huvudmålservern och processervern.  
4. För **datalager**, Välj det datalager som du vill återställa lokalerna diskar. Det här alternativet används när den lokala virtuella datorn tas bort och du behöver skapa nya diskar. Det här alternativet ignoreras om diskarna redan finns. Du måste ange ett värde.
5. Välj kvarhållningsenhetens.
6. Failback-principen väljs automatiskt.
7. Välj **OK** ska börja återaktivera skyddet. Ett jobb börjar att replikera den virtuella datorn från Azure till den lokala platsen. Du kan följa förloppet på fliken **Jobb**. När återaktiveringen av skyddet lyckas försätts den virtuella datorn i ett skyddat läge.

Notera följande information:
- Om du vill återställa till en alternativ plats (när den lokala virtuella datorn tas bort), Välj kvarhållningsenhet och datalager som är konfigurerade för huvudmålservern. När du växlar tillbaka till den lokala platsen, använda VMware-datorer i skyddsplanen för återställning efter fel samma datalager som huvudmålservern. En ny virtuell dator skapas i vCenter.
- Om du vill återställa den virtuella datorn på Azure till en befintlig lokal virtuell dator kan du montera den lokala virtuella datorns datalager med läs-/ skrivåtkomst på på huvudmålserverns ESXi-värden.

    ![Återaktivera skyddet av dialogrutan](./media/vmware-azure-reprotect/reprotectinputs.png)

- Du kan också återaktivera skyddet på nivån i en återställningsplan. En replikeringsgrupp kan att återaktivera skyddet endast via en återställningsplan. När du skyddar på nytt med hjälp av en återställningsplan, måste du ange värdena för varje skyddad dator.
- Använd samma huvudmålserver för att återaktivera skyddet av en replikeringsgrupp. Om du använder en annan huvudmålserver för att återaktivera skyddet av en replikeringsgrupp, får inte servern en gemensam tidpunkt i tid.
- Den lokala virtuella datorn stängs av under återaktiveringen av skyddet. Detta hjälper att säkerställa datakonsekvens vid replikeringen. Stäng inte på den virtuella datorn när återaktiveringen av skyddet har slutförts.



## <a name="common-issues"></a>Vanliga problem

- Site Recovery stöder för närvarande kan du växlar tillbaka till ett datalager VMFS eller virtuellt SAN-nätverk. En NFS-datalager stöds inte. På grund av den här begränsningen datalager val av indata på skärmen återaktivering av skydd är tomt för NFS-datalager, eller den visar virtuellt SAN-databasen men misslyckas under jobbet. Om du vill växla tillbaka du skapa ett VMFS datalager lokalt och växla tillbaka till den. Den här återställning efter fel gör en fullständig nedladdning av VMDK.
- Om du vill göra en skrivskyddad användare vCenter-identifiering och skydda virtuella datorer, protection lyckas och redundans fungerar. Vid återaktiveringen av skyddet misslyckas växling vid fel eftersom datalagringen inte kan identifieras. Ett symtom är att datalagringen inte visas under återaktiveringen av skyddet. För att lösa detta problem kan du uppdatera vCenter-autentiseringsuppgifter med ett lämpligt konto som har behörighet och försök sedan igen. 
- När du inte återställa en virtuell Linux-dator och köra den lokalt, ser du att paketet Network Manager har avinstallerats från datorn. Den här avinstallationen sker eftersom Nätverkshanteraren paketet tas bort när den virtuella datorn återställs i Azure.
- När en Linux-dator är konfigurerad med en statisk IP-adress och växlas över till Azure, hämta IP-adress från DHCP. När du växlar över till den lokala fortsätter den virtuella datorn att använda DHCP för att hämta IP-adressen. Manuellt logga in på datorn och sedan ange IP-adressen till en statisk adress om det behövs. En Windows-dator kan hämta statiska IP-adressen igen.
- Om du använder den kostnadsfria versionen av ESXi 5.5 eller den kostnadsfria versionen av Hypervisor vSphere 6, redundansväxlingen genomförs, men återställningen lyckas inte. Uppgradera till utvärderingslicens för båda programmen om du vill aktivera återställning efter fel.
- Om du inte kan nå konfigurationsservern från processervern, kan du använda Telnet för att kontrollera anslutningen till konfigurationsservern på port 443. Du kan också försöka pinga konfigurationsservern från processervern. En processerver bör även ha ett pulsslag när den är ansluten till konfigurationsservern.
- En Windows Server 2008 R2 SP1-server som skyddas som en fysisk lokala server kan inte växlas tillbaka från Azure till en lokal plats.
- Du kan inte tillbaka under följande omständigheter:
    - Du migrerade datorer till Azure. [Läs mer](migrate-overview.md#what-do-we-mean-by-migration).
    - Du kan flytta en virtuell dator till en annan resursgrupp.
    - Du har tagit bort den virtuella Azure-datorn.
    - Du har inaktiverat skyddet av den virtuella datorn.
    - Du har skapat den virtuella datorn manuellt i Azure. Datorn bör har ursprungligen skyddade lokala och växlas över till Azure innan återaktivering av skydd.
    - Du kan inte bara till en ESXi-värden. Det går inte att återställa virtuella VMware-datorer eller fysiska servrar till Hyper-V-värdar, fysiska datorer eller VMware-arbetsstationer.


## <a name="next-steps"></a>Nästa steg

Efter att den virtuella datorn har angett ett skyddat läge, kan du [initierar en återställning efter fel](vmware-azure-failback.md). Återställningen stänger av den virtuella datorn i Azure och startar den lokala virtuella datorn. Förvänta dig del avbrott för programmet. Välj en tid för återställning efter fel när programmet kan tolerera driftstopp.


