---
title: Skydda virtuella VMware-datorer/fysiska servrar till en lokal plats med Azure Site Recovery
description: Efter redundansväxlingen till Azure under haveri beredskap för virtuella VMware-datorer och fysiska servrar, lär du dig hur du växlar tillbaka från Azure till den lokala platsen.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: mayg
ms.openlocfilehash: f3d5f38d940b99c6a74d784f174c91d4127353dc
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961351"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Återaktivera skydd och återställning av datorer till en lokal plats efter redundansväxling till Azure

Efter [redundansväxlingen](site-recovery-failover.md) av lokala virtuella VMware-datorer eller fysiska servrar till Azure, är det första steget i att växla tillbaka till din lokala plats att skydda de virtuella Azure-datorer som skapades under redundansväxlingen. Den här artikeln beskriver hur du gör detta. 

Om du vill ha en snabb översikt tittar du på följande video om hur du växlar över från Azure till en lokal plats.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Innan du börjar

Om du använde en mall för att skapa dina virtuella datorer måste du kontrol lera att varje virtuell dator har ett eget UUID för diskarna. Om den lokala virtuella datorns UUID står i konflikt med huvud målets UUID, eftersom båda har skapats från samma mall, Miss lyckas skyddet. Distribuera ett annat huvud mål som inte har skapats från samma mall. Notera följande information:
- Om du försöker växla tillbaka till en alternativ vCenter kontrollerar du att din nya vCenter och huvud mål servern har identifierats. Ett typiskt symptom är att data lagret inte är tillgängligt eller inte visas i dialog rutan för att **skydda** .
- Om du vill replikera tillbaka till en lokal plats behöver du en princip för återställning efter fel. Den här principen skapas automatiskt när du skapar en princip för framåt-riktning. Notera följande information:
    - Den här principen associeras automatiskt med konfigurations servern när den skapas.
    - Den här principen är inte redigerbar.
    - Ange värden för principen är (återställnings punkt tröskelvärde = 15 minuter, kvarhållning av återställnings punkt = 24 timmar, frekvens för programkonsekvens för ögonblicks bild = 60 minuter).
- Vid återskydd och återställning efter fel måste den lokala konfigurations servern vara igång och ansluten.
- Om en vCenter-Server hanterar de virtuella datorer som du kommer att återställa till, kontrol lera att du har de [behörigheter som krävs](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) för identifiering av virtuella datorer på vCenter-servrar.
- Ta bort ögonblicks bilder på huvud mål servern innan du återaktiverar. Om ögonblicks bilder finns på det lokala huvud målet eller på den virtuella datorn Miss lyckas skyddet. Ögonblicks bilderna på den virtuella datorn slås samman automatiskt under ett återskydds jobb.
- Alla virtuella datorer i en replikeringsgrupp måste vara av samma operativ system typ (antingen alla Windows eller alla Linux). En replikeringsgrupp med blandade operativ system stöds för närvarande inte för skydd och återställning efter fel till lokalt. Detta beror på att huvud målet måste vara av samma operativ system som den virtuella datorn. Alla virtuella datorer i en replikeringsgrupp måste ha samma huvud mål. 
- Huvud målet måste ha samma eller högre OS-version än operativ system versionerna för de replikerade objekten.
- En konfigurations server krävs lokalt när du växlar tillbaka. Under återställning efter fel måste den virtuella datorn finnas i konfigurations serverns databas. Annars Miss lyckas failback. Se till att du gör regelbundna schemalagda säkerhets kopieringar av konfigurations servern. Om det är en katastrof återställer du servern med samma IP-adress så att återställning efter fel fungerar. 
- Skydd och återställning efter fel kräver en plats-till-plats (S2S) VPN-eller ExpressRoute-peering för att replikera data. Ange nätverket så att de misslyckade virtuella datorerna i Azure kan komma åt (pinga) den lokala konfigurations servern. Du måste distribuera en processerver i Azure-nätverket för de misslyckade eller virtuella datorerna. Den här processervern måste också kunna kommunicera med den lokala konfigurations servern och huvud mål servern.
- Om IP-adresserna för replikerade objekt behålls vid redundansväxling, bör S2S-eller ExpressRoute-anslutningen upprättas mellan virtuella Azure-datorer och konfigurations serverns failback-nätverkskort. Observera att IP-postkvarhållning kräver att konfigurations servern har två nätverkskort som är anslutna till käll datorer och ett för Azure-återställning av fel. Detta är för att undvika överlappande av adress intervallen för under nätet och redundansväxla virtuella datorer.
- Se till att du öppnar följande portar för redundans och återställning efter fel:

    ![Portar för redundans och återställning efter fel](./media/vmware-azure-reprotect/failover-failback.png)

- Läs alla [krav för portar och URL-vit listning](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processerver i Azure

Du behöver en processerver i Azure innan du växlar tillbaka till din lokala plats:

- Processervern tar emot data från den eller de skyddade virtuella datorerna i Azure och skickar sedan data till den lokala platsen.
- Ett nätverk med låg latens krävs mellan processervern och den skyddade virtuella datorn. Därför rekommenderar vi att du distribuerar en processerver i Azure. Replikeringens prestanda är högre om processervern är närmare den replikerade virtuella datorn (den felande datorn i Azure). 
- För ett koncept bevis kan du använda den lokala processervern och ExpressRoute med privat peering.

Så här distribuerar du en processerver i Azure:

1. Om du behöver distribuera en processerver i Azure läser du [Konfigurera en processerver i Azure för återställning efter fel](vmware-azure-set-up-process-server-azure.md).
2. De virtuella Azure-datorerna skickar replikeringsdata till processervern. Konfigurera nätverk så att de virtuella Azure-datorerna kan komma åt processervern.
3. Kom ihåg att replikeringen från Azure till lokala enheter bara kan ske över S2S VPN eller via privat peering av ditt ExpressRoute-nätverk. Se till att tillräckligt med bandbredd är tillgängligt över nätverks kanalen.

## <a name="deploy-a-separate-master-target-server"></a>Distribuera en separat huvud mål Server

Huvudmålservern tar emot data vid återställning efter fel. Som standard körs huvudmålservern på konfigurationsservern lokalt. Beroende på volymen för misslyckad återställning kan du dock behöva skapa en separat huvud mål server för återställning efter fel. Så här skapar du en:

* [Skapa en Linux-huvud mål server](vmware-azure-install-linux-master-target.md) för återställning efter fel för virtuella Linux-datorer. Detta är obligatoriskt. Observera att huvud mål servern på LVM inte stöds.
* Du kan också skapa en separat huvud mål server för återställning av Windows-VM. Det gör du genom att köra den enhetliga installationen igen och välja att skapa en huvud mål server. [Läs mer](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers). 

När du har skapat en huvud mål server utför du följande aktiviteter:

- Om den virtuella datorn finns lokalt på vCenter-servern behöver huvud mål servern åtkomst till den lokala virtuella datorns VMDK-fil (Virtual Machine disk). Åtkomst krävs för att skriva replikerade data till den virtuella datorns diskar. Se till att den lokala virtuella datorns data lager är monterat på huvud mål serverns värd med Läs-/Skriv behörighet.
- Om den virtuella datorn inte finns lokalt på vCenter-servern måste Site Recoverys tjänsten skapa en ny virtuell dator under återskyddet. Den här virtuella datorn skapas på den ESX-värd som du skapar huvud målet på. Välj ESX-värden noggrant, så att den virtuella datorn för återställning efter fel skapas på den värd som du vill ha.
- Du kan inte använda Storage vMotion för huvud mål servern. Om du använder Storage vMotion för huvud mål servern kan återställningen Miss skadas. Det går inte att starta den virtuella datorn eftersom diskarna inte är tillgängliga. Undvik detta genom att undanta huvud mål servrarna från vMotion-listan.
- Om ett huvud mål bevarar en lagrings vMotion-aktivitet efter återaktivering, migreras de skyddade virtuella dator diskarna som är kopplade till huvud mål servern till målet för vMotion-aktiviteten. Om du försöker växla tillbaka efter detta Miss lyckas det att du inte kan hitta diskarna. Sedan blir det svårt att hitta diskarna i dina lagrings konton. Du måste hitta dem manuellt och koppla dem till den virtuella datorn. Sedan kan den lokala virtuella datorn startas.
- Lägg till en lagrings enhet till din befintliga huvud mål server för Windows. Lägg till en ny disk och formatera enheten. Lagrings enheten används för att stoppa tidpunkterna då den virtuella datorn replikerar tillbaka till den lokala platsen. Följande är några kriterier för en kvarhållning enhet. Om dessa villkor inte uppfylls visas inte enheten för huvud mål servern:
    - Volymen används inte för något annat ändamål, t. ex. ett mål för replikering.
    - Volymen är inte i låst läge.
    - Volymen är inte en cache-volym. Det går inte att installera huvud mål servern på volymen. Den anpassade installations volymen för processervern och huvud mål servern är inte giltig för en kvarhållningsperiod. När processervern och huvud målet är installerade på en volym, är volymen en cache-volym på huvud mål servern.
    - Volymens fil system typ är inte FAT eller FAT32.
    - Volym kapaciteten är inte noll.
    - Standard Retentions volymen för Windows är R-volymen.
    - Standard lagrings volymen för Linux är/mnt/retention.
- Du måste lägga till en ny enhet om du använder en befintlig processerver/konfigurations Server dator eller en skalnings-eller processervern/huvud mål Server dator. Den nya enheten måste uppfylla föregående krav. Om lagrings enheten inte finns visas den inte i list rutan för val i portalen. När du har lagt till en enhet i det lokala huvud målet tar det upp till 15 minuter innan enheten visas i valet på portalen. Du kan också uppdatera konfigurations servern om enheten inte visas efter 15 minuter.
- Installera VMware-verktyg eller öppna-VM-verktyg på huvud mål servern. Utan verktygen går det inte att identifiera data lagringen på huvud mål serverns ESXi-värd.
- Ange `disk.EnableUUID=true` inställningen i konfigurations parametrarna för den virtuella huvud mål datorn i VMware. Om den här raden inte finns lägger du till den. Den här inställningen krävs för att tillhandahålla ett konsekvent UUID till VMDK så att den monteras på rätt sätt.
- Den ESX-värd som huvud målet skapas på måste ha minst ett VMFS-datalager som är kopplat till det. Om inga VMFS-datalager är anslutna är data lagret på sidan **restore** tomt och du kan inte fortsätta.
- Huvud mål servern kan inte ha ögonblicks bilder på diskarna. Om det finns ögonblicks bilder går det inte att återställa och återställa efter fel.
- Huvud målet kan inte ha en paravirtuell SCSI-styrenhet. Kontrollanten kan bara vara en LSI Logic-styrenhet. Utan en LSI Logic-kontroll, Miss lyckas återskydd.
- För en instans kan huvud målet ha högst 60 diskar anslutna till sig. Om antalet virtuella datorer som återskyddas till det lokala huvud målet har fler än det totala antalet 60 diskar, kan det leda till att det inte går att återaktivera huvud målet. Se till att du har tillräckligt med huvud mål disk platser eller distribuera ytterligare huvud mål servrar.
    

## <a name="enable-reprotection"></a>Aktivera skydd

När en virtuell dator startas i Azure tar det lite tid för agenten att registrera tillbaka till konfigurations servern (upp till 15 minuter). Under den här tiden kan du inte återaktivera skyddet och ett fel meddelande anger att agenten inte är installerad. Om detta händer väntar du några minuter och försöker sedan igen:


1. Välj **valv** > **replikerade objekt**. Högerklicka på den virtuella datorn som har redundansväxlats och välj sedan **skydda igen**. Eller välj datorn från kommando knapparna och välj sedan **skydda igen**.
2. Kontrol lera att skydds riktningen för **Azure till lokal** riktning är vald.
3. I **huvud mål server** och **processerver**väljer du den lokala huvud mål servern och processervern.  
4. För **data lager**väljer du det data lager som du vill återställa diskarna till lokalt. Det här alternativet används när den lokala virtuella datorn tas bort och du måste skapa nya diskar. Det här alternativet ignoreras om diskarna redan finns. Du måste fortfarande ange ett värde.
5. Välj lagrings enhet.
6. Failback-principen väljs automatiskt.
7. Välj **OK** för att starta skyddet. Ett jobb börjar att replikera den virtuella datorn från Azure till den lokala platsen. Du kan följa förloppet på fliken **jobb** . När skyddet lyckas övergår den virtuella datorn till ett skyddat tillstånd.

Notera följande information:
- Om du vill återställa till en alternativ plats (när den lokala virtuella datorn tas bort) väljer du den lagrings enhet och det data lager som har kon figurer ATS för huvud mål servern. När du växlar tillbaka till den lokala platsen använder de virtuella VMware-datorerna i skydds planen för återställning efter fel samma data lager som huvud mål servern. En ny virtuell dator skapas sedan i vCenter.
- Om du vill återställa den virtuella datorn på Azure till en befintlig lokal virtuell dator, monterar du den lokala virtuella datorns data lager med Läs-/Skriv behörighet på huvud mål serverns ESXi-värd.

    ![Dialog rutan för att skydda igen](./media/vmware-azure-reprotect/reprotectinputs.png)

- Du kan också återaktivera skyddet på nivån för en återställnings plan. En replikeringsgrupp kan bara skyddas via en återställnings plan. När du återaktiverar med hjälp av en återställnings plan måste du ange värden för varje skyddad dator.
- Använd samma huvudmålserver för att återaktivera skyddet av en replikeringsgrupp. Om du använder en annan huvud mål server för att återaktivera skyddet av en replikeringsgrupp, kan servern inte tillhandahålla en gemensam tidpunkt.
- Den lokala virtuella datorn är inaktive rad under återskyddet. Detta hjälper att säkerställa datakonsekvens vid replikeringen. Aktivera inte den virtuella datorn när skyddet har slutförts.



## <a name="common-issues"></a>Vanliga problem

- Om du utför en skrivskyddad vCenter-identifiering och skyddar virtuella datorer, slutförs skyddet och redundansväxlingen fungerar. Vid återskydd Miss lyckas redundansväxlingen eftersom data lagret inte kan identifieras. Ett symtom är att data lager inte visas under återskydd. För att lösa det här problemet kan du uppdatera vCenter-autentiseringsuppgifterna med ett lämpligt konto som har behörigheter och sedan försöka utföra jobbet igen. 
- När du växlar tillbaka till en virtuell Linux-dator och kör den lokalt kan du se att Network Manager-paketet har avinstallerats från datorn. Avinstallationen sker eftersom Network Manager-paketet tas bort när den virtuella datorn återställs i Azure.
- När en virtuell Linux-dator konfigureras med en statisk IP-adress och växlas över till Azure, hämtas IP-adressen från DHCP. När du växlar över till den lokala datorn fortsätter den virtuella datorn att använda DHCP för att hämta IP-adressen. Logga in manuellt på datorn och ange sedan IP-adressen tillbaka till en statisk adress vid behov. En virtuell Windows-dator kan hämta sin statiska IP-adress igen.
- Om du använder antingen ESXi 5,5 Free Edition eller den kostnads fria versionen av vSphere 6, lyckas redundansväxlingen, men failback fungerar inte. Om du vill aktivera återställning efter fel, uppgradera till antingen programmets utvärderings licens.
- Om du inte kan komma åt konfigurations servern från processervern använder du Telnet för att kontrol lera anslutningen till konfigurations servern på port 443. Du kan också prova att pinga konfigurations servern från processervern. En processerver bör också ha ett pulsslag när den är ansluten till konfigurations servern.
- En Windows Server 2008 R2 SP1-Server som skyddas som en fysisk lokal server kan inte återställas från Azure till en lokal plats.
- Du kan inte växla tillbaka i följande situationer:
    - Du migrerade datorer till Azure. [Läs mer](migrate-overview.md#what-do-we-mean-by-migration).
    - Du har flyttat en virtuell dator till en annan resurs grupp.
    - Du har tagit bort den virtuella Azure-datorn.
    - Du har inaktiverat skyddet av den virtuella datorn.
    - Du skapade den virtuella datorn manuellt i Azure. Datorn bör först skyddas lokalt och ha redundansväxlats till Azure innan återskydden.
    - Det går bara att återställa en ESXi-värd. Du kan inte återställa virtuella VMware-datorer eller fysiska servrar till Hyper-V-värdar, fysiska datorer eller VMware-arbetsstationer.


## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har försatts i ett skyddat tillstånd kan du [starta en återställning efter fel](vmware-azure-failback.md). Failback-tjänsten stänger av den virtuella datorn i Azure och startar den lokala virtuella datorn. En del stillestånds tid för programmet förväntas. Välj en tid för återställning efter fel när programmet kan tolerera stillestånds tid.


