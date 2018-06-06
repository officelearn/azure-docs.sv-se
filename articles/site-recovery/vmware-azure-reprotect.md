---
title: Skyddar virtuella datorer från Azure till en lokal plats | Microsoft Docs
description: Du kan starta en återställning efter fel om du vill flytta virtuella datorer lokala efter redundans för virtuella datorer till Azure. Lär dig att skydda innan en återställning efter fel.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanaki
ms.openlocfilehash: 4ee6eefa431b06e0cb694635e188c87a8a4175c9
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737273"
---
# <a name="reprotect-machines-from-azure-to-an-on-premises-site"></a>Skyddar datorer från Azure till en lokal plats

Efter [redundans](site-recovery-failover.md) av lokala virtuella VMware-datorer eller fysiska servrar till Azure, är det första steget i misslyckas tillbaka till den lokala platsen att skydda den virtuella Azure-datorer som har skapats under växling vid fel. Den här artikeln beskriver hur du gör detta. 

Titta på följande videoklipp om hur du växla över från Azure till en lokal plats för en snabb överblick.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Innan du börjar

Om du använder en mall för att skapa virtuella datorer, se till att har varje virtuell dator sin egen UUID för diskarna. Om den lokala virtuella UUID hamnar i konflikt med UUID på Huvudmålet eftersom båda har skapats från samma mall, misslyckas återaktivera skydd. Distribuera en annan huvudmålserver som inte har skapats från samma mall. Notera följande information:
- Om du vill växla tillbaka till en alternativ vCenter kontrollerar du att din nya vCenter och huvudmålservern identifieras. Ett typiskt symtom är att datastores inte är tillgänglig eller visas inte i den **skyddar** dialogrutan.
- Om du vill replikera tillbaka till lokala behöver du en princip för återställning efter fel. Den här principen skapas automatiskt när du skapar en princip för riktning framåt. Notera följande information:
    - Den här principen är auto som är kopplade till konfigurationsservern under skapandet.
    - Den här principen inte redigeras.
    - Ange värdena för principen (RPO tröskelvärde = 15 minuter, Kvarhållningstid för återställningspunkten = 24 timmar, frekvensen för App konsekvenskontroll ögonblicksbilder = 60 minuter).
- Under återaktivera skydd och återställning efter fel vara lokala konfigurationsservern körs och är ansluten.
- Om en vCenter-servern hanterar de virtuella datorerna som du ska återställas, se till att du har den [nödvändiga behörigheter](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) för identifiering av virtuella datorer på vCenter-servrar.
- Ta bort ögonblicksbilder på huvudmålservern innan du skyddar. Om ögonblicksbilder finns på huvudmålservern lokalt eller på den virtuella datorn inte återaktivera skydd. Ögonblicksbilder på den virtuella datorn slås automatiskt samman under ett Skapa nytt jobb.
- Alla virtuella datorer i en replikeringsgrupp måste vara av samma typ av operativsystem (alla fönster eller alla Linux). En replikeringsgrupp med blandade operativsystem för närvarande stöds inte för skyddar och återställning till lokalt. Det beror på att huvudmålservern måste vara av samma operativsystem som den virtuella datorn. Alla virtuella datorer i en replikeringsgrupp måste ha samma huvudmålservern. 
- En konfigurationsservern är lokala när du växlar tillbaka. Under återställning efter fel, måste den virtuella datorn finns i server-konfigurationsdatabasen. Återställning är annars misslyckas. Kontrollera att du gör regelbundna schemalagda säkerhetskopieringar serverns konfiguration. Om det finns en katastrof, återställa servern med samma IP-adress så att återställningen fungerar.
- Återaktivera skydd och återställning kräver en plats-till-plats (S2S) VPN-anslutning att replikera data. Ange nätverket så att misslyckades över virtuella datorer i Azure kan nå (ping) konfigurationen av lokal server. Du kanske också vill distribuera en processerver i Azure-nätverk för den virtuella datorn kunde inte över. Den här processen-servern måste också kunna kommunicera med konfigurationsservern lokalt.
- Kontrollera att du öppnar följande portar för redundans och återställning:

    ![Portar för redundans och återställning efter fel](./media/vmware-azure-reprotect/failover-failback.png)

- Läsa alla de [krav för portar och URL: en vitlistning](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processerver i Azure

Du måste kanske en processerver i Azure innan du växlar tillbaka till den lokala platsen:
- Processervern tar emot data från den skyddade virtuella datorn i Azure och skickar data till den lokala platsen.
- Ett nätverk med låg latens krävs mellan processervern och den skyddade virtuella datorn. I allmänhet måste du överväga svarstid när du bestämmer om du behöver en processerver i Azure:
    - Om du har en Azure ExpressRoute-anslutning som konfigurerar kan du använda en lokal server för processen för att skicka data eftersom fördröjning mellan den virtuella datorn och processervern är låg.
    - Om du har bara en S2S-VPN, rekommenderar vi dock distribution av processerver i Azure.
    - Vi rekommenderar att du använder en för Azure-baserade processerver vid återställning. Replikeringsprestanda är högre om processervern ligger närmare den replikerande virtuella datorn (misslyckades över dator i Azure). För ett konceptbevis kan du använda lokala processervern och ExpressRoute med privat peering.

Att distribuera en processerver i Azure:

1. Om du behöver distribuera en processerver i Azure finns [ställa in en processerver i Azure för återställning efter fel](vmware-azure-set-up-process-server-azure.md).
2. Virtuella Azure-datorer skicka replikeringsdata till processervern. Konfigurera nätverk så att den virtuella Azure-datorer kan nå processervern.
3. Kom ihåg att replikering från Azure till lokala kan inträffa endast via S2S VPN- eller privat peering i ExpressRoute-nätverk. Se till att tillräckligt med bandbredd är tillgänglig över nätverkskanalen.

## <a name="deploy-a-separate-master-target-server"></a>Distribuera en separat huvudmålserver

Huvudmålservern tar emot data vid återställning efter fel. Som standard körs huvudmålservern på konfigurationsservern lokalt. Beroende på mängden misslyckades tillbaka trafik, kan du dock behöver skapa en separat huvudmålserver för återställning efter fel. Här är hur man skapar en:

* [Skapa en Linux-huvudmålserver](vmware-azure-install-linux-master-target.md) för återställning av virtuella Linux-datorer. Detta krävs.
* Du kan också skapa en separat huvudmålserver för återställning av virtuell Windows-dator. Om du vill göra det kör Unified installationen igen och välja för att skapa en huvudmålserver. [Läs mer](physical-azure-set-up-source.md#run-azure-site-recovery-unified-setup).

När du har skapat en huvudmålserver gör du följande uppgifter:

- Om den virtuella datorn finns lokalt på vCenter server behöver huvudmålservern åtkomst till den lokala virtuella virtuell Disk (VMDK)-fil. Åtkomst krävs för att skriva replikerade data till den virtuella datorns diskar. Se till att den lokala virtuella datalagret har monterats på den huvudmålservern värd med läs-/ skrivbehörighet.
- Om den virtuella datorn inte finns lokalt på vCenter-servern, måste Site Recovery-tjänsten skapa en ny virtuell dator under återaktivera skydd. Den här virtuella datorn skapas på ESX-värd som du skapar huvudmålservern. Välj ESX-värd noggrant, så att återställning av virtuell dator skapas på värden som du vill använda.
- Du kan använda Storage vMotion för huvudmålservern. Återställningen misslyckas kan leda till att använda Storage vMotion för huvudmålservern. Den virtuella datorn kan inte starta eftersom diskarna inte är tillgängliga för den. Exkludera master målservrarna vMotion-listan om du vill förhindra att detta inträffar.
- Om ett huvudmål genomgår en Storage vMotion aktivitet efter återaktivera skydd, migrera skyddade virtuella diskar som är kopplade till huvudmålservern till målet för uppgiften vMotion. Om du försöker att återställas efter detta misslyckas vill koppla bort på disken eftersom diskarna inte hittas. Det blir svårt att hitta diskarna i storage-konton. Du måste du hittar dem manuellt och koppla dem till den virtuella datorn. Efter att kan den lokala virtuella datorn startas.
- Lägga till en kvarhållningsenhetens i din befintliga Windows-huvudmålservern. Lägg till en ny disk och formatera hårddisken. Kvarhållningsenhetens används för att stoppa punkterna i tid när den virtuella datorn replikeras tillbaka till den lokala platsen. Följande är några villkor för en enhet för kvarhållning. Om dessa villkor inte uppfylls inte enheten i listan för huvudmålservern:
    - Volymen används inte i något annat syfte, till exempel ett mål för replikering.
    - Det finns inte i låsläge volymen.
    - Volymen är inte en cachevolym. Installationen av huvudmålservern kan inte finnas på den volymen. Anpassad installationsvolymen för process-servern och master målet är inte berättigad till en kvarhållningsvolymen. Om processervern och huvudmålservern är installerade på en volym är är volymen en cachevolym på Huvudmålet.
    - Vilken typ av filsystem på volymen är inte FAT eller FAT32.
    - Volymens kapacitet är noll.
    - Standard-kvarhållningsvolymen för Windows är R-volym.
    - Standard-kvarhållningsvolymen för Linux är /mnt/retention.
- Du måste lägga till en ny enhet om du använder en befintlig process serverkonfiguration/server-dator eller en skal- eller server eller huvudtjänsten target server-dator. Den nya enheten måste uppfylla kraven ovan. Om kvarhållningsenhetens inte finns, visas den inte i listrutan markeringen på portalen. När du lägger till en enhet till lokala huvudmålservern tar upp till 15 minuter för enheten visas i markeringen på portalen. Du kan också uppdatera konfigurationsservern om enheten inte visas efter 15 minuter.
- Installera VMware-verktyg eller öppna-vm-verktyg på huvudmålservern. Utan verktyg, kan datastores på ESXi-värd på huvudmålservern inte identifieras.
- Ange den `disk.EnableUUID=true` i konfigurationsparametrar för huvudmålservern virtuell dator i VMware. Om den här raden inte finns kan du lägga till den. Den här inställningen krävs för att ge en konsekvent UUID till VMDK så att den monterar korrekt.
- ESX-värd som huvudmålservern skapas måste ha filen system (VMFS) av minst en virtuell dator-datalagret som kopplats till den. Om inga VMFS datastores kopplas den **Datastore** indata på sidan Skapa nytt är tom och du kan inte fortsätta.
- Huvudmålservern kan inte ha ögonblicksbilder på diskarna. Om det finns ögonblicksbilder, misslyckas återaktivera skydd och återställning efter fel.
- Huvudmålservern kan inte ha en Paravirtual SCSI-styrenhet. Kontrollanten kan bara finnas en domänkontrollant för en LSI Logic. Utan en domänkontrollant för en LSI Logic återaktivera skyddet fungerar inte.
- Huvudmålservern kan ha högst 60 diskar som är anslutna till den för alla instanser. Om antalet virtuella datorer att återaktivera skyddet till den lokala huvudmålservern har fler än det totala antalet 60 diskar reprotects till huvudmålservern börja att misslyckas. Se till att du har tillräckligt med master riktade disken kortplatser, eller distribuera ytterligare huvudmålservern servrar.
    

## <a name="enable-reprotection"></a>Aktivera återaktivera skydd

Efter att en virtuell dator startar i Azure, tar det lite tid för att agenten ska registrera tillbaka till konfigurationsservern (upp till 15 minuter). Under denna tid kan du längre inte att skydda och ett felmeddelande som anger att agenten inte är installerad. Om det händer, Vänta några minuter och försök sedan att återaktivera skyddet igen:


1. Välj **valvet** > **replikerade objekt**. Högerklicka på den virtuella datorn växlas vid fel och välj sedan **skydda igen**. Eller, från kommandoknappar, Välj datorn, och välj sedan **skydda igen**.
2. Kontrollera att den **Azure till lokala** riktning av skydd är markerat.
3. I **Huvudmålserver** och **Processervern**, Välj den lokala huvudmålservern och processervern.  
4. För **Datastore**, markera databasen som du vill återställa den diskar lokalt. Det här alternativet används när den lokala virtuella datorn tas bort och du behöver skapa nya diskar. Det här alternativet ignoreras om diskarna redan finns. Du måste ange ett värde.
5. Välj kvarhållningsenhetens.
6. Failback-principen väljs automatiskt.
7. Välj **OK** ska börja återaktivera skydd. Ett jobb börjar att replikera den virtuella datorn från Azure till den lokala platsen. Du kan följa förloppet på fliken **Jobb**. När det återaktivera skyddet lyckas försätts den virtuella datorn i ett skyddat läge.

Notera följande information:
- Om du vill återställa till en alternativ plats (när den lokala virtuella datorn tas bort), Välj kvarhållningsenhetens och datalager som är konfigurerade för huvudmålservern. När du växlar tillbaka till den lokala platsen VMware-datorer i skyddsplan återställning efter fel att använda samma datalager som huvudmålservern. En ny virtuell dator skapas sedan i vCenter.
- Om du vill återställa den virtuella datorn i Azure för en befintlig lokal virtuell dator kan du montera den lokala virtuella datastores med läs-/ skrivbehörighet på huvudmålservern serverns ESXi-värd.

    ![Skapa nytt dialogrutan](./media/vmware-azure-reprotect/reprotectinputs.png)

- Du kan även skydda igen på nivån i en återställningsplan. En replikeringsgrupp kan att återaktivera skyddet endast via en återställningsplan. När du skyddar med hjälp av en återställningsplan måste du ange värdena för varje skyddad dator.
- Använd samma huvudmålserver för att återaktivera skyddet av en replikeringsgrupp. Om du använder en annan huvudmålserver för att skydda en replikeringsgrupp kan inte servern tillhandahålla en gemensam punkt i tiden.
- Den lokala virtuella datorn stängs av under återaktivera skydd. Detta hjälper att säkerställa datakonsekvens vid replikeringen. Aktivera inte den virtuella datorn efter att återaktivera skyddet har slutförts.



## <a name="common-issues"></a>Vanliga problem

- Site Recovery stöder för närvarande, misslyckas tillbaka till ett VMFS eller virtuellt SAN-datalager. En NFS-datalager stöds inte. På grund av den här begränsningen kan datalagret val av indata på skärmen skyddar är tom för NFS datastores eller den visar virtuellt SAN-databasen men misslyckas under jobbet. Om du vill växla tillbaka du växlar tillbaka till den och skapa ett datalager VMFS lokalt. Den här återställning efter fel gör en fullständig hämtning av VMDK.
- Om du utför en skrivskyddade användaren vCenter identifiering och skydda virtuella datorer, skydd lyckas och redundans fungerar. Under återaktivera skydd misslyckas redundans eftersom datastores inte kan identifieras. Ett symtom är att datastores inte visas under återaktivera skydd. Lös problemet du uppdatera vCenter-autentiseringsuppgifter med ett lämpligt konto som har behörighet och försök sedan igen. 
- När du återställas på en virtuell Linux-dator och köra den lokalt, ser du att paketet Network Manager har avinstallerats från datorn. Den här avinstallationen beror Nätverkshanteraren paketet tas bort när den virtuella datorn återställs i Azure.
- När en virtuell Linux-dator är konfigurerad med en statisk IP-adress och växlas över till Azure, förvärvas IP-adress från DHCP. När du växla över till den lokala fortsätter den virtuella datorn att använda DHCP för att få IP-adress. Logga in manuellt på datorn och ange sedan IP-adressen till en statisk adress om det behövs. En virtuell Windows-dator kan hämta statiska IP-adressen igen.
- Om du använder ESXi 5.5 gratisversion eller vSphere 6 Hypervisor gratisversion redundans lyckas, men lyckas inte återställning efter fel. Uppgradera till utvärderingslicens för båda programmen om du vill aktivera återställning efter fel.
- Om du inte kan nå konfigurationsservern från processervern, kan du använda Telnet för att kontrollera anslutning till konfigurationsservern på port 443. Du kan även försöka att pinga konfigurationsservern från processervern. En processerver bör också ha ett pulsslag när den är ansluten till konfigurationsservern.
- En Windows Server 2008 R2 SP1-server som skyddas som en fysisk lokal server inte kan vara misslyckad tillbaka från Azure till en lokal plats.
- Du kan inte underkänna tillbaka under följande omständigheter:
    - Du har migrerat datorer till Azure. [Läs mer](migrate-overview.md#what-do-we-mean-by-migration).
    - Du kan flytta en virtuell dator till en annan resursgrupp.
    - Du har tagit bort den virtuella Azure-datorn.
    - Du har inaktiverat skydd för den virtuella datorn.
    - Du har skapat den virtuella datorn manuellt i Azure. Datorn ska har ursprungligen skyddade lokalt och växlas över till Azure innan du återaktivera skydd.
    - Du kan inte bara till en ESXi-värd. Det går inte att återställning av virtuella VMware-datorer eller fysiska servrar med Hyper-V-värdar, fysiska datorer eller VMware-arbetsstationer.


## <a name="next-steps"></a>Nästa steg

Efter att den virtuella datorn har angett ett skyddat läge, kan du [starta en återställning efter fel](vmware-azure-failback.md). Återställningen stängs den virtuella datorn i Azure och startar den lokala virtuella datorn. Förvänta dig vissa avbrott för programmet. Välj en tid för återställning efter fel när programmet kan tolerera driftstopp.


