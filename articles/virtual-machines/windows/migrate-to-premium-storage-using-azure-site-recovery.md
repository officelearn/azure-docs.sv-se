---
title: Migrera dina virtuella Windows-datorer till Azure Premium Storage med Azure Site Recovery
description: Migrera dina befintliga virtuella datorer till Azure Premium Storage med hjälp av Site Recovery. Premium Storage erbjuder diskstöd med låg latens med hög prestanda för I/O-intensiva arbetsbelastningar som körs på virtuella Azure-datorer.
services: virtual-machines-windows,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: bd5f9fc787a6299e8d7c14f4b99f6f4d59cf78af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74819077"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrera till Premium Storage med hjälp av Azure Site Recovery

[Azure premium SSD-enheter](disks-types.md) ger högpresterande diskstöd med låg latens för virtuella datorer (VMs) som kör I/O-intensiva arbetsbelastningar. Den här guiden hjälper dig att migrera dina VM-diskar från ett standardlagringskonto till ett premiumlagringskonto med hjälp av [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery är en Azure-tjänst som bidrar till din strategi för affärskontinuitet och haveriberedskap genom att dirigera replikering av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt datacenter. När avbrott inträffar på din primära plats växlar du över till den sekundära platsen för att hålla program och arbetsbelastningar tillgängliga. Du återgår till din primära plats när den återgår till normal drift. 

Site Recovery ger test redundans för att stödja haveriberedskapsövningar utan att påverka produktionsmiljöer. Du kan köra redundans med minimal dataförlust (beroende på replikeringsfrekvens) för oväntade katastrofer. I scenariot med att migrera till Premium Storage kan du använda [redundansen i Site Recovery](../../site-recovery/site-recovery-failover.md) för att migrera måldiskar till ett premiumlagringskonto.

Vi rekommenderar att du migrerar till Premium Storage med hjälp av Site Recovery eftersom det här alternativet ger minimal stilleståndstid. Med det här alternativet undviks också manuell körning av att kopiera diskar och skapa nya virtuella datorer. Site Recovery kommer systematiskt kopiera dina diskar och skapa nya virtuella datorer under redundans. 

Site Recovery stöder ett antal typer av redundans med minimal eller ingen driftstopp. Om du vill planera avbrott och uppskatta dataförlust läser du [typerna av redundans i Site Recovery](../../site-recovery/site-recovery-failover.md). Om du [förbereder att ansluta till virtuella Azure-datorer efter redundans](../../site-recovery/vmware-walkthrough-overview.md)bör du kunna ansluta till den virtuella Azure-datorn med hjälp av RDP efter redundans.

![Diagram över haveriberedskap][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery-komponenter

Dessa site recovery-komponenter är relevanta för det här migreringsscenariot:

* **Konfigurationsserver** är en virtuell Azure-dator som samordnar kommunikation och hanterar datareplikerings- och återställningsprocesser. På den här virtuella datorn kör du en enda installationsfil för att installera konfigurationsservern och ytterligare en komponent, som kallas en processserver, som en replikeringsgateway. Läs om [konfigurationsserverkrav](../../site-recovery/vmware-walkthrough-overview.md). Du konfigurerar konfigurationsservern bara en gång och du kan använda den för alla migreringar till samma region.

* **Processserver** är en replikeringsgateway som: 

  1. Tar emot replikeringsdata från virtuella käll-datorer.
  2. Optimerar data med cachelagring, komprimering och kryptering.
  3. Skickar data till ett lagringskonto. 

  Den hanterar också push-installation av mobilitetstjänsten för att köpa virtuella datorer och utför automatisk identifiering av virtuella käll-datorer. Standardprocessservern är installerad på konfigurationsservern. Du kan distribuera ytterligare fristående processservrar för att skala distributionen. Läs om [metodtips för processserverdistribution](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) och [distribution av ytterligare processservrar](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Du ställer bara in processservern en gång och du kan använda den för alla migreringar till samma region.

* **Mobilitetstjänsten** är en komponent som distribueras på alla vanliga virtuella datorer som du vill replikera. Den samlar in data skriver på den vanliga virtuella datorn och vidarebefordrar dem till processservern. Läs om [replikerade datorförutsättninger](../../site-recovery/vmware-walkthrough-overview.md).

Den här bilden visar hur dessa komponenter samverkar:

![Interaktion mellan komponenter för webbplatsåterställning][15]

> [!NOTE]
> Site Recovery stöder inte migrering av Lagringsutrymme diskar.

Ytterligare komponenter för andra scenarier finns i [Scenarioarkitektur](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Grundläggande om Azure

Dessa är Azure-kraven för det här migreringsscenariot:

* En Azure-prenumeration.
* Ett Azure premium-lagringskonto för att lagra replikerade data.
* Ett virtuellt Azure-nätverk som virtuella datorer ansluter till när de skapas vid redundans. Det virtuella Azure-nätverket måste finnas i samma region som den där site recovery körs.
* Ett Azure-standardlagringskonto för att lagra replikeringsloggar. Detta kan vara samma lagringskonto för vm-diskarna som migreras.

## <a name="prerequisites"></a>Krav

* Förstå relevanta migreringsscenariokomponenter i föregående avsnitt.
* Planera driftstoppet genom att lära dig mer om [redundans i Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Installations- och migreringssteg

Du kan använda Site Recovery för att migrera virtuella Azure IaaS-datorer mellan regioner eller inom samma region. Följande instruktioner är anpassade för det här migreringsscenariot från artikeln Replikera virtuella datorer med [VMware eller fysiska servrar till Azure](../../site-recovery/vmware-walkthrough-overview.md). Följ länkarna för detaljerade steg utöver instruktionerna i den här artikeln.

### <a name="step-1-create-a-recovery-services-vault"></a>Steg 1: Skapa ett recovery services-valv

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Välj **Skapa en** > **resurshanteringssäkerhetskopiering****Management** > och platsåterställning (OMS) . Du kan också välja **Bläddra i** > **valv****för återställningstjänster.** > 
   >[!NOTE]
   >Backup och Site Recovery var tidigare en del av [OMS svit](/azure/azure-monitor/terminology#april-2018---retirement-of-operations-management-suite-brand).
1. Ange en region som virtuella datorer ska replikeras till. För migrering i samma region väljer du den region där käll-virtuella datorer och källlagringskonton finns. 

### <a name="step-2-choose-your-protection-goals"></a>Steg 2: Välj dina skyddsmål 

1. Öppna Azure-portalen på den virtuella datorn där du vill installera [konfigurationsservern](https://portal.azure.com).
2. Gå till **Recovery Services-valvinställningar** > **Webbplatsåterställning** > **Settings** > **steg 1: Förbered** > **infrastrukturskyddsmål**.

   ![Bläddra till målfönstret Skydd][2]

3. Under **Skyddsmål**väljer du **Till Azure**i den första listrutan . I den andra listrutan väljer du **Inte virtualiserad / Annan**och väljer sedan **OK**.

   ![Fönstret Skyddsmål med ifyllda rutor][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Steg 3: Konfigurera källmiljön (konfigurationsserver)

1. Hämta Azure Site Recovery Unified Setup och valvregistreringsnyckeln genom att gå till fönstret **Förbered infrastruktur** > **Förbered källa** > **Lägg till server.** **Azure Site Recovery Unified Setup** 
 
   Du behöver valvregistreringsnyckeln för att köra den enhetliga installationen. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Bläddra till fönstret Lägg till server][4]

2. Lägg till en **konfigurationsserver** i fönstret Lägg till server.

   ![Fönstret Lägg till server med konfigurationsserver markerad][5]

3. På den virtuella datorn som du använder som konfigurationsserver kör du Unified Setup för att installera konfigurationsservern och processservern. Du kan [gå igenom skärmdumpar](../../site-recovery/vmware-walkthrough-overview.md) för att slutföra installationen. Du kan läsa följande skärmbilder för steg som angetts för det här migreringsscenariot.

   1. Välj **Installera konfigurationsservern och processservern**i **Innan du börjar**.

      ![Innan du börjar sida][6]

   2. Bläddra **Registration**bland registrering och välj den registreringsnyckel som du hämtade från valvet.

      ![Registreringssida][7]

   3. I **Miljöinformation** väljer du om du ska replikera virtuella VMwares-datorer. För det här migreringsscenariot väljer du **Nej**.

      ![Sidan Miljöinformation][8]

4. När installationen är klar gör du följande i fönstret **Konfigurationsserver** för Microsoft Azure Site Recovery Configuration Server:
 
   1. Använd fliken **Hantera konton** för att skapa det konto som Webbplatsåterställning kan använda för automatisk identifiering. (I scenariot om att skydda fysiska datorer är det inte relevant att konfigurera kontot, men du behöver minst ett konto för att aktivera något av följande steg. I det här fallet kan du namnge kontot och lösenordet som vilket som helst.) 
   2. Använd fliken **Arkivregistrering** för att ladda upp valvautentiseringsfilen.

      ![Fliken Registrering av arkiv][9]

### <a name="step-4-set-up-the-target-environment"></a>Steg 4: Ställ in målmiljön

Välj **Förbered infrastrukturmål** > **Target**och ange den distributionsmodell som du vill använda för virtuella datorer efter redundans. Du kan välja **Classic** eller **Resource Manager**, beroende på ditt scenario.

![Fönstret Mål][10]

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk. 

> [!NOTE]
> Om du använder ett premiumlagringskonto för replikerade data måste du konfigurera ytterligare ett standardlagringskonto för att lagra replikeringsloggar.

### <a name="step-5-set-up-replication-settings"></a>Steg 5: Konfigurera replikeringsinställningar

Om du vill kontrollera att konfigurationsservern har associerats med replikeringsprincipen som du skapar följer du [Konfigurera replikeringsinställningar](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Steg 6: Planera kapacitet

1. Använd [kapacitetsplaneraren](../../site-recovery/site-recovery-capacity-planner.md) för att noggrant uppskatta nätverksbandbredd, lagring och andra krav för att uppfylla replikeringsbehoven. 
2. När du är klar väljer du **Ja, jag har gjort det** i Har du slutfört **kapacitetsplanering?**.

   ![Ruta för att bekräfta att du slutfört kapacitetsplanering][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Steg 7: Installera mobilitetstjänsten och aktivera replikering

1. Du kan välja att [skicka installationen](../../site-recovery/vmware-walkthrough-overview.md) till käll-virtuella datorer eller att manuellt [installera mobilitetstjänsten](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) på dina virtuella käll-datorer. Du kan hitta kravet på att trycka installation och sökvägen till den manuella installationsprogrammet i den medföljande länken. Om du gör en manuell installation kan du behöva använda en intern IP-adress för att hitta konfigurationsservern.

   ![Informationssida för konfigurationsserver][12]

   Den misslyckade virtuella datorn har två temporära diskar: en från den primära virtuella datorn och den andra som skapades under etableringen av den virtuella datorn i återställningsregionen. Om du vill utesluta den tillfälliga disken före replikering installerar du mobilitetstjänsten innan du aktiverar replikering. Mer information om hur du utesluter den temporära disken finns i [Utesluta diskar från replikering](../../site-recovery/vmware-walkthrough-overview.md).

2. Aktivera replikering på följande sätt:
   1. Välj **Replikera programkälla** > **Source**. När du har aktiverat replikering för första gången väljer du **+Replikera** i valvet för att aktivera replikering för ytterligare datorer.
   2. I steg 1 ställer du in **Källa** som processserver.
   3. I steg 2 anger du distributionsmodellen efter redundans, ett premiumlagringskonto att migrera till, ett standardlagringskonto för att spara loggar och ett virtuellt nätverk som inte kan lyckas.
   4. I steg 3 lägger du till skyddade virtuella datorer med IP-adress. (Du kan behöva en intern IP-adress för att hitta dem.)
   5. I steg 4 konfigurerar du egenskaperna genom att välja de konton som du har konfigurerat tidigare på processservern.
   6. I steg 5 väljer du replikeringsprincipen som du skapade tidigare i "Steg 5: Konfigurera replikeringsinställningar".
   7. Välj **OK**.

   > [!NOTE]
   > När en Azure-virtuell dator är avtalad och startad igen finns det ingen garanti för att den får samma IP-adress. Om IP-adressen för konfigurationsservern/processservern eller de skyddade virtuella Azure-datorerna ändras, kanske replikeringen i det här scenariot inte fungerar korrekt.

   ![Aktivera replikeringsfönstret med Källan markerad][13]

När du utformar din Azure Storage-miljö rekommenderar vi att du använder separata lagringskonton för varje virtuell dator i en tillgänglighetsuppsättning. Vi rekommenderar att du följer den bästa metoden i lagringslagret för att [använda flera lagringskonton för varje tillgänglighetsuppsättning](../linux/manage-availability.md). Distribuera VM-diskar till flera lagringskonton hjälper till att förbättra lagringstillgängligheten och distribuerar I/O över Azure-lagringsinfrastrukturen.

Om dina virtuella datorer har en tillgänglighetsuppsättning rekommenderar vi i stället för att replikera diskar för alla virtuella datorer till ett lagringskonto, att du migrerar flera virtuella datorer flera gånger. På så sätt delar de virtuella datorerna i samma tillgänglighetsuppsättning inte ett enda lagringskonto. Använd fönstret **Aktivera replikering** för att ställa in ett mållagringskonto för varje virtuell dator, ett i taget.
 
Du kan välja en distributionsmodell efter redundans beroende på dina behov. Om du väljer Azure Resource Manager som distributionsmodell efter redundans kan du växla över en virtuell dator (Resource Manager) till en virtuell dator (Resource Manager) eller växla över en virtuell dator (klassisk) till en virtuell dator (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Steg 8: Kör en testväxling

Om du vill kontrollera om replikeringen är klar markerar du instansen För webbplatsåterställning och väljer sedan **Inställningar** > **för replikerade objekt**. Du kommer att se status och procentandel av replikeringsprocessen. 

När den första replikeringen är klar kör du en test redundans för att validera replikeringsstrategin. Detaljerade steg i en testväxling finns [i Kör en testväxling i Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Innan du kör en redundans kontrollerar du att dina virtuella datorer och replikeringsstrategi uppfyller kraven. Mer information om hur du kör en testväxling finns i [Testa redundans till Azure i Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Du kan se status för testundansen i **Inställningar** > **jobb** > *YOUR_FAILOVER_PLAN_NAME*. I fönstret kan du se en uppdelning av stegen och resultat för lyckade/misslyckade. Om testmisslystningen misslyckas i något steg väljer du steget för att kontrollera felmeddelandet. 

### <a name="step-9-run-a-failover"></a>Steg 9: Kör en redundans

När testundansen har slutförts kör du en redundans för att migrera diskarna till Premium Storage och replikera VM-instanserna. Följ de detaljerade stegen i [Kör en redundans](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Var noga med att välja **Stäng av virtuella datorer och synkronisera de senaste data**. Det här alternativet anger att Site Recovery ska försöka stänga av skyddade virtuella datorer och synkronisera data så att den senaste versionen av data kommer att misslyckas över. Om du inte väljer det här alternativet eller om försöket inte lyckas kommer redundansen att komma från den senaste tillgängliga återställningspunkten för den virtuella datorn. 

Site Recovery skapar en VM-instans vars typ är samma som eller liknar en virtuell dator med Premium-lagring. Du kan kontrollera prestanda och pris för olika VM-instanser genom att gå till [Windows Virtual Machines Prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) eller Linux Virtual Machines [Prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Steg efter migrering

1. **Konfigurera replikerade virtuella datorer till tillgänglighetsuppsättningen om tillämpligt**. Site Recovery stöder inte migrerande virtuella datorer tillsammans med tillgänglighetsuppsättningen. Beroende på distributionen av den replikerade virtuella datorn gör du något av följande:
   * För en virtuell dator som skapats via den klassiska distributionsmodellen: Lägg till den virtuella datorn i tillgänglighetsuppsättningen i Azure-portalen. Detaljerade steg finns [i Lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning](../linux/classic/configure-availability-classic.md).
   * För en virtuell dator som skapats via Resurshanterarens distributionsmodell: Spara konfigurationen av den virtuella datorn och ta sedan bort och återskapa de virtuella datorerna i tillgänglighetsuppsättningen. Det gör du genom att använda skriptet på [Ange azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Innan du kör skriptet kontrollerar du dess begränsningar och planerar driftstoppet.

2. **Ta bort gamla virtuella datorer och diskar**. Kontrollera att Premium-diskarna överensstämmer med källdiskar och att de nya virtuella datorerna utför samma funktion som käll-virtuella datorer. Ta bort den virtuella datorn och ta bort diskarna från dina källlagringskonton i Azure-portalen. Om det finns ett problem där disken inte tas bort även om du har tagit bort den virtuella datorn läser [du Felsöka fel på lagringsresurser.](storage-resource-deletion-errors.md)

3. **Rensa Azure Site Recovery-infrastrukturen**. Om Site Recovery inte längre behövs kan du rensa dess infrastruktur. Ta bort replikerade objekt, konfigurationsservern och återställningsprincipen och ta sedan bort Azure Site Recovery-valvet.

## <a name="troubleshooting"></a>Felsökning

* [Övervaka och felsöka skydd för virtuella datorer och fysiska servrar](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Forum för återställning av Microsoft Azure-webbplatser](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Nästa steg

Specifika scenarier för migrering av virtuella datorer finns i följande resurser:

* [Migrera virtuella Azure-datorer mellan lagringskonton](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Skapa och överför en Windows Server VHD till Azure](upload-generalized-managed.md)
* [Migrera virtuella datorer från Amazon AWS till Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Se även följande resurser för att lära dig mer om Azure Storage och Virtuella Azure-datorer:

* [Azure-lagring](https://azure.microsoft.com/documentation/services/storage/)
* [Virtuella Azure-datorer](https://azure.microsoft.com/documentation/services/virtual-machines/)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
