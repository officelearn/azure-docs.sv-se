---
title: Skydda igen från Azure till en lokal plats | Microsoft Docs
description: Du kan starta en återställning efter fel om du vill flytta virtuella datorer lokala efter redundans för virtuella datorer till Azure. Lär dig att skydda innan en återställning efter fel.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2018
ms.author: rajanaki
ms.openlocfilehash: c336966f9a785707e76bc6a10c4a9283d797d064
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768587"
---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Skydda igen från Azure till en lokal plats



## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du skyddar virtuella Azure-datorer från Azure till en lokal plats. Följ anvisningarna i den här artikeln när du är redo att växla tillbaka din virtuella VMware-datorer eller Windows-/ Linux fysiska servrar när de har redundansväxlats från lokalt platsen till Azure (enligt beskrivningen i [replikera VMware-datorer och fysiska servrar till Azure med Azure Site Recovery](site-recovery-failover.md)).

> [!WARNING]
> Det går inte att återställning efter fel när du har antingen [slutfört en migrering](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), flytta en virtuell dator till en annan resursgrupp eller ta bort den virtuella Azure-datorn. Om du inaktiverar skydd för den virtuella datorn, kan du inte återställning efter fel. Om den virtuella datorn skapades i Azure (föddes i molnet) kan du skyddar den tillbaka till lokalt. Datorn ska har ursprungligen skyddade lokalt och växlas över till Azure innan du skyddar.


Du kan starta en återställning efter fel på de virtuella datorerna för att anpassa dem till den lokala platsen efter att återaktivera skyddet har slutförts och replikerar de skyddade virtuella datorerna.

> [!NOTE]
> Du kan bara skydda igen och återställning efter fel ESXi-värd. Det går inte att återställning av den virtuella datorn till Hyper-v-värdar eller VMware-arbetsstationer eller andra virtualiseringsplattform.

Skicka kommentarer eller frågor i slutet av den här artikeln eller på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Titta på följande videoklipp om hur du växla över från Azure till en lokal plats för en snabb överblick.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> Vid redundans till Azure lokal plats kanske inte är tillgängligt och därför konfigurationsservern kan vara antingen icke tillgängliga eller avstängning. Under skyddar och återställning efter fel ska lokalt konfigurationsservern körs och OK anslutet.


När du förbereder att skydda virtuella datorer kan ta eller Överväg att följande nödvändiga åtgärder:

* Om en vCenter-servern hanterar de virtuella datorerna som du vill växla tillbaka till bör du se till att du har den [nödvändiga behörigheter](site-recovery-vmware-to-azure-classic.md) för identifiering av virtuella datorer på vCenter-servrar.

  > [!WARNING]
  > Om det finns ögonblicksbilder på den lokala huvudmålservern eller den virtuella datorn, misslyckas återaktivera skydd. Du kan ta bort ögonblicksbilder på huvudmålservern innan du fortsätter att skydda. Ögonblicksbilder på den virtuella datorn slås automatiskt samman under ett Skapa nytt jobb.

* Innan du växlar tillbaka skapa två ytterligare komponenter:

  * **Processervern**: den [processervern](site-recovery-vmware-setup-azure-ps-resource-manager.md) tar emot data från den skyddade virtuella datorn i Azure och skickar data till den lokala platsen. Ett nätverk med låg latens krävs mellan processervern och den skyddade virtuella datorn. Du kan därför har en lokal processerver om du använder en Azure ExpressRoute-anslutning eller en Azure-baserade processerver och en VPN-anslutning.
  
  * **Huvudmålservern**: huvudmålservern tar emot data för återställning efter fel. Lokala management-server som du har skapat har en huvudmålserver som installeras som standard. Beroende på mängden misslyckades tillbaka trafik, kan du dock behöver skapa en separat huvudmålserver för återställning efter fel.
    * [En virtuell Linux-dator måste en Linux-huvudmålserver](site-recovery-how-to-install-linux-master-target.md).
    * En virtuell Windows-dator måste en Windows-huvudmålserver. Du kan använda de lokala processen server och master måldatorerna igen.
    * Huvudmålservern har andra krav som anges i [vanliga saker att kontrollera på Huvudmålet innan du skyddar](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).

> [!NOTE]
> Alla virtuella datorer i en replikeringsgrupp ska vara av samma typ av operativsystem (alla fönster eller alla Linux). En replikeringsgrupp med blandade operativsystem stöds för närvarande inte för att skydda igen och återställning till lokalt. Detta beror på att huvudmålservern ska vara av samma operativsystem som den virtuella datorn och alla virtuella datorer i en replikeringsgrupp ska ha samma huvudmålservern. 

    

* En konfigurationsservern är lokala när du växlar tillbaka. Under återställning efter fel, måste den virtuella datorn finns i server-konfigurationsdatabasen. Återställning är annars misslyckas. 

> [!IMPORTANT]
> Kontrollera att du vidtar regelbundna schemalagda säkerhetskopieringar serverns konfiguration. Om det finns en katastrof, återställa servern med samma IP-adress så att återställningen fungerar.

> [!WARNING]
> En replikeringsgrupp endast ha virtuella Windows-datorer eller virtuella Linux-datorer och inte en blandning av båda eftersom alla virtuella datorer i en grupp för replictaion använder samma huvudmålservern och Linux VM kräver en Linux huvudmålserver och som klokt för Windows VM.

* Ange den `disk.EnableUUID=true` i konfigurationsparametrar för huvudmålservern virtuell dator i VMware. Om den här raden inte finns, kan du lägga till den. Den här inställningen krävs för att ge en konsekvent UUID till den virtuella disken (VMDK) så att den monterar korrekt.

* *Du kan inte använda Storage vMotion på huvudmålservern*. Detta kan orsaka återställning misslyckas. Den virtuella datorn kan inte starta eftersom de inte är tillgängliga för den. Exkludera master målservrarna vMotion-listan för att förhindra det här problemet.

* Lägg till en ny enhet på huvudmålservern: en kvarhållningsenhetens. Lägg till en ny disk och formatera hårddisken.


### <a name="frequently-asked-questions"></a>Vanliga frågor och svar

#### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>Varför behöver jag en S2S VPN-anslutning eller en ExpressRoute-anslutning för att replikera data tillbaka till den lokala platsen?
Medan replikering från lokal till Azure kan ske via Internet eller en ExpressRoute-anslutning som har offentlig peering, återaktivera skydd och återställning efter fel som kräver en plats-till-plats (S2S) VPN-anslutning att replikera data. Ange nätverket så att misslyckades över virtuella datorer i Azure kan nå (ping) konfigurationen av lokal server. Du kan också distribuera en processerver i Azure-nätverk för den virtuella datorn kunde inte över. Den här processen servern ska också kunna kommunicera med konfigurationsservern lokalt.

#### <a name="when-should-i-install-a-process-server-in-azure"></a>När ska installera en processerver i Azure?


De virtuella datorerna på Azure som du vill skydda igen skicka replikeringsdata till en processerver. Konfigurera nätverket så att de virtuella datorerna i Azure kan nå processervern.

Du kan distribuera en processerver i Azure eller använda den befintliga processervern som du använde under växling vid fel. Viktig aspekt att tänka är fördröjning för att skicka data från de virtuella datorerna på Azure till processervern.

Om du har en ExpressRoute-anslutning som konfigurerar kan du använda en lokal processerver för att skicka data eftersom fördröjning mellan den virtuella datorn och processervern är låg.

 ![Arkitekturdiagram för ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



Om du har bara en S2S-VPN, rekommenderar vi dock distribution av processerver i Azure.

 ![Arkitekturdiagram för VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Kom ihåg att replikering från Azure till lokala kan inträffa endast via S2S-VPN eller privat peering i ExpressRoute-nätverk. Se till att tillräckligt med bandbredd är tillgänglig över nätverkskanalen.

Information om hur du installerar en Azure-baserade process finns [hantera en processerver som körs i Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> Vi rekommenderar att du använder en för Azure-baserade processerver vid återställning. Replikeringsprestanda är högre om processervern ligger närmare den replikerande virtuella datorn (misslyckades över dator i Azure). Under din bevis på koncept (POC) eller demonstration, kan du dock använda lokala processervern tillsammans med ExpressRoute med privat peering för att slutföra POC snabbare.

> [!NOTE]
> Replikering från lokal till Azure kan inträffa endast över internet eller ExpressRoute med offentlig peering. Replikering från Azure till lokala kan inträffa endast via S2S VPN eller ExpressRoute med privat peering


#### <a name="what-ports-should-i-open-on-different-components-so-that-reprotection-can-work"></a>Vilka portar bör jag öppna på olika komponenter så att återaktivera skyddet fungerar?

![Portar för redundans och återställning efter fel](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

#### <a name="which-master-target-server-should-i-use-for-reprotection"></a>Vilka huvudmålservern ska använda för att återaktivera skydd?
En lokal huvudmålsserver krävs för att ta emot data från processervern och sedan skriva till den lokala virtuella VMDK. Om du skyddar virtuella Windows-datorer, måste en Windows-huvudmålserver. Du kan återanvända processen lokalt target server och master<!-- !todo component -->. Du måste ställa in en ytterligare lokala Linux-huvudmålserverns för Linux virtuella datorer.


Information om hur du installerar en huvudmålserver finns:

* [Så här installerar du Windows huvudmålservern](site-recovery-vmware-to-azure.md)
* [Hur du installerar Linux huvudmålservern](site-recovery-how-to-install-linux-master-target.md)


#### <a name="what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback"></a>Vilka typer av datalagret stöds på lokala ESXi-värd under återställning efter fel?

Azure Site Recovery stöder för närvarande, misslyckas tillbaka endast till en virtuell dator file system (VMFS) eller ett virtuellt SAN-datalagret. Ett NFS-datalager stöds inte. På grund av den här begränsningen kan ange datastore markeringen på skyddar skärmen är tom vid NFS datastores, eller så visar du databasen men misslyckas under jobbet. Om du vill växla tillbaka du växlar tillbaka till den och skapa ett datalager VMFS lokalt. Den här återställning kommer en fullständig hämtning av VMDK.

### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Vanliga saker att kontrollera när du har slutfört installationen av huvudmålservern

* Om den virtuella datorn är finns lokalt på vCenter-servern, behöver åtkomst till den lokala virtuella VMDK huvudmålservern. Åtkomst krävs för att skriva replikerade data till den virtuella datorns diskar. Se till att den lokala virtuella datalagret har monterats på den huvudmålservern värd med läs-/ skrivbehörighet.

* Om den virtuella datorn inte finns lokalt på vCenter server måste Site Recovery-tjänsten skapa en ny virtuell dator under återaktivera skydd. Den här virtuella datorn skapas på ESX-värd som du skapar huvudmålservern. Välj ESX-värd noggrant, så att återställning av virtuell dator skapas på värden som du vill använda.

* *Du kan inte använda Storage vMotion för huvudmålservern*. Detta kan orsaka återställning misslyckas. Den virtuella datorn kan inte starta eftersom de inte är tillgängliga för den.

  > [!WARNING]
  > Om ett huvudmål genomgår en Storage vMotion aktivitet efter återaktivera skydd, migrera skyddade virtuella datorer diskar som är kopplade till huvudmålservern till målet för uppgiften vMotion. Om du försöker att återställas efter detta misslyckas vill koppla bort på disken eftersom diskarna inte hittas. Det blir svårt att hitta diskarna i storage-konton. Du måste du hittar dem manuellt och koppla dem till den virtuella datorn. Efter att kan den lokala virtuella datorn startas.

* Lägga till en kvarhållningsenhetens i din befintliga Windows-huvudmålservern. Lägg till en ny disk och formatera hårddisken. Kvarhållningsenhetens används för att stoppa punkterna i tid när den virtuella datorn replikeras tillbaka till den lokala platsen. Följande är några villkor för en enhet för kvarhållning. Utan dessa villkor visas enheten inte för huvudmålservern.

   * Volymen används inte i något annat syfte, till exempel ett mål för replikering.

   * Volymen är inte i låsläge.

   * Volymen är inte en cachevolym. Huvudmålservern-installationen får inte finnas på den volymen. Anpassad installationsvolymen för process-servern och master målet är inte berättigad till en kvarhållningsvolymen. Om processervern och huvudmålservern är installerade på en volym är är volymen en cachevolym på Huvudmålet.

   * Vilken typ av filsystem på volymen är inte FAT eller FAT32.

   * Volymens kapacitet är noll.

   * Standard-kvarhållningsvolymen för Windows är R-volym.

   * Standard-kvarhållningsvolymen för Linux är /mnt/retention.

  > [!IMPORTANT]
  > Du måste lägga till en ny enhet om du använder en befintlig process serverkonfiguration/server-dator eller en skala eller en process server eller huvudtjänsten server måldatorn. Den nya enheten ska kraven ovan. Om kvarhållningsenhetens inte finns, visas den inte i listrutan markeringen på portalen. När du lägger till en enhet till lokala huvudmålservern tar upp till 15 minuter för enheten visas i markeringen på portalen. Du kan också uppdatera konfigurationsservern om enheten inte visas efter 15 minuter.

* En Linux misslyckades över virtuell dator kräver en Linux-huvudmålserver. En Windows kunde inte över virtuell dator kräver en Windows-huvudmålserver.

* Installera VMware-verktygen på huvudmålservern. Datastores på ESXi-värd på huvudmålservern kan inte identifieras utan VMware-verktyg.

* Aktivera den `disk.EnableUUID=true` parametern på den virtuella datorn i huvudmålservern med hjälp av vCenter-egenskaper. <!-- !todo Needs link. -->

* Huvudmålservern bör ha minst en VMFS datastore ansluten. Om det finns ingen, den **Datastore** indata på sidan Skapa nytt är tomt, och du kan inte fortsätta.

* Huvudmålservern kan inte ha ögonblicksbilder på diskarna. Om det finns ögonblicksbilder, misslyckas återaktivera skydd och återställning efter fel.

* Huvudmålservern kan inte ha en Paravirtual SCSI-styrenhet. Kontrollanten kan bara finnas en domänkontrollant för en LSI Logic. Utan en domänkontrollant för en LSI Logic återaktivera skyddet fungerar inte.

* Huvudmålservern kan ha atmst 60 diskar som är anslutna till den i en viss instans. Om antalet virtuella datorer att återaktivera skyddet till den lokala huvudmålservern har summan totala antal diskar som är mer än 60 och sedan reprotects till huvudmålservern startar misslyckas. Se till att du har tillräckligt med master riktade disken platser eller distribuera ytterligare huvudmålservern servrar.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Steg för att skydda igen

> [!NOTE]
> Efter att en virtuell dator startar i Azure, tar det lite tid för att agenten ska registrera tillbaka till konfigurationsservern (upp till 15 minuter). Återaktivera skydd misslyckas under den här tiden och ett felmeddelande som anger att agenten inte är installerad. Vänta några minuter och försök sedan att återaktivera skyddet igen.



1. I **valvet** > **replikerade objekt**, högerklicka på den virtuella datorn har växlas över och välj sedan **skydda igen**. Du kan också klicka på datorn och välja **skydda igen** från knapparna.
2. I bladet Lägg märke till att riktningen för skydd, **Azure till lokala**, har redan valts.
3. I **Huvudmålserver** och **Processervern**, Välj den lokala huvudmålservern och processervern.
4. För **Datastore**, markera databasen som du vill återställa den diskar lokalt. Det här alternativet används när den lokala virtuella datorn tas bort och du behöver skapa nya diskar. Det här alternativet ignoreras om diskarna finns redan, men du måste ange ett värde.
5. Välj kvarhållningsenhetens.
6. Failback-principen väljs automatiskt.
7. Klicka på **OK** ska börja återaktivera skyddet. Ett jobb börjar att replikera den virtuella datorn från Azure till den lokala platsen. Du kan följa förloppet på fliken **Jobb**.

Om du vill återställa till en alternativ plats (när den lokala virtuella datorn tas bort), Välj kvarhållningsenhetens och datalager som är konfigurerade för huvudmålservern. När du växlar tillbaka till den lokala platsen VMware-datorer i skyddsplan återställning efter fel att använda samma datalager som huvudmålservern. En ny virtuell dator skapas sedan i vCenter.

Om du vill återställa den virtuella datorn i Azure för en befintlig lokal virtuell dator kan du montera den lokala virtuella datastores med läs-/ skrivbehörighet på huvudmålservern serverns ESXi-värd.
    ![Skydda dialogrutan igen](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Du kan även skydda igen på nivån i en återställningsplan. En replikeringsgrupp kan att återaktivera skyddet endast via en återställningsplan. När du skyddar med hjälp av en återställningsplan måste du ange värden för varje skyddad dator.

> [!NOTE]
> Använd samma huvudmålserver för att återaktivera skyddet av en replikeringsgrupp. Om du använder en annan huvudmålserver för att återaktivera skyddet av en replikeringsgrupp, får inte servern en gemensam tidpunkt.

> [!NOTE]
> Den lokala virtuella datorn stängs av under återaktivera skydd. Detta hjälper att säkerställa datakonsekvens vid replikeringen. Aktivera inte den virtuella datorn efter att återaktivera skyddet har slutförts.

När det återaktivera skyddet lyckas, kommer den virtuella datorn anger ett skyddat läge.

## <a name="common-issues"></a>Vanliga problem

* Om du använder en mall för att skapa virtuella datorer, se till att har varje virtuell dator sin egen UUID för diskarna. Om den lokala virtuella UUID hamnar i konflikt med som huvudmålservern eftersom båda har skapats från samma mall, misslyckas återaktivera skydd. Distribuera en annan huvudmålserver som inte har skapats från samma mall.

* Om du utför en skrivskyddade användaren vCenter identifiering och skydda virtuella datorer, skydd lyckas och redundans fungerar. Under återaktivera skydd misslyckas redundans eftersom datastores inte kan identifieras. Ett symtom är att datastores inte visas under återaktivera skydd. Lös problemet, kan du uppdatera vCenter-autentiseringsuppgifter med ett lämpligt konto som har behörighet och försök igen. Mer information finns i [replikera VMware-datorer och fysiska servrar till Azure med Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

* När du återställas på en virtuell Linux-dator och köra den lokalt, ser du att paketet Network Manager har avinstallerats från datorn. Den här avinstallationen beror Nätverkshanteraren paketet tas bort när den virtuella datorn återställs i Azure.

* När en virtuell Linux-dator är konfigurerad med en statisk IP-adress och växlas över till Azure, förvärvas IP-adress från DHCP. När du växla över till den lokala fortsätter den virtuella datorn att använda DHCP för att få IP-adress. Manuellt logga in på datorn och ange IP-adressen till en statisk adress om det behövs. En virtuell Windows-dator kan hämta den statiska IP-Adressen igen.

* Om du använder ESXi 5.5 gratisversion eller vSphere 6 Hypervisor gratisversion redundans lyckas, men lyckas inte återställning efter fel. Uppgradera till utvärderingslicens för båda programmen om du vill aktivera återställning efter fel.

* Om du inte kan nå konfigurationsservern från processervern, kan du använda Telnet för att kontrollera anslutning till konfigurationsservern på port 443. Du kan även försöka att pinga konfigurationsservern från processervern. En processerver bör också ha ett pulsslag när den är ansluten till konfigurationsservern.

* Om du vill växla tillbaka till en alternativ vCenter kontrollerar du att din nya vCenter och huvudmålservern identifieras. Ett typiskt symtom är att datastores inte är tillgänglig eller visas i den **skyddar** dialogrutan.

* En Windows Server 2008 R2 SP1-server som skyddas som en fysisk lokal server inte kan vara misslyckad tillbaka från Azure till en lokal plats.


## <a name="next-steps"></a>Nästa steg

Efter att den virtuella datorn har angett ett skyddat läge, kan du [starta en återställning efter fel](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back). 

Återställningen kommer att stänga av den virtuella datorn i Azure och starta den virtuella datorn lokalt. Förvänta dig vissa avbrott för programmet. Välj en tid för återställning efter fel när programmet kan tolerera driftstopp.