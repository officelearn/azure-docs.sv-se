---
title: Migrera dina virtuella Linux-datorer till Azure Premium Storage med Azure Site Recovery | Microsoft Docs
description: Migrera dina befintliga virtuella datorer till Azure Premium Storage med hjälp av Site Recovery. Premium Storage erbjuder stöd för diskar med höga prestanda och låg latens för I/O-intensiva arbetsbelastningar som körs på Azure Virtual Machines.
services: virtual-machines-linux,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 3a4304301b99369426aa985508d6cabd26ba2c9f
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326967"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrera till Premium Storage med hjälp av Azure Site Recovery

[Azure premium SSD](disks-types.md) ger höga prestanda och låg latens disksupport för virtuella datorer (VM) som kör I/O-intensiva arbetsbelastningar. Den här guiden hjälper dig att migrera dina VM-diskar från ett standardlagringskonto till ett premium storage-konto med hjälp av [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery är en Azure-tjänst som bidrar till din strategi för affärskontinuitet och haveriberedskap genom att samordna replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt datacenter. Vid avbrott på den primära platsen så växlar du över till den sekundära platsen att hålla program och arbetsbelastningar som är tillgängliga. Du växlar tillbaka till den primära platsen när den återgår till normal drift. 

Site Recovery tillhandahåller redundanstestning för att stödja tester av haveriberedskap utan att påverka produktionsmiljöer. Du kan köra redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvensen) för oväntade haverier. I scenariot för att migrera till Premium Storage kan du använda den [redundans i Site Recovery](../../site-recovery/site-recovery-failover.md) att migrera måldiskarna till ett premium storage-konto.

Vi rekommenderar att du migrerar till Premium Storage med hjälp av Site Recovery eftersom det här alternativet ger minimal avbrottstid. Det här alternativet undviker också manuell körning av kopierar diskar och skapar nya virtuella datorer. Site Recovery kommer systematiskt kopiera dina diskar och skapa nya virtuella datorer under en redundansväxling. 

Site Recovery stöder ett antal typer av redundansväxlingar med minimal eller ingen avbrottstid. För att planera din driftstopp och beräkna förlust av data, se de [typer av redundans i Site Recovery](../../site-recovery/site-recovery-failover.md). Om du [förbereda för att ansluta till virtuella Azure-datorer efter redundans](../../site-recovery/vmware-walkthrough-overview.md), du ska kunna ansluta Azure-datorn med RDP efter en redundansväxling.

![Disaster recovery-diagram][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery-komponenter

Dessa Site Recovery-komponenter är relevanta för den här Migreringsscenario:

* **Konfigurationsservern** är en Azure virtuell dator som samordnar kommunikationen och hanterar processer för replikering och återställning av data. På den här virtuella datorn du kör en enda installationsfil för att installera konfigurationsservern och en ytterligare komponent kallas en processerver som en replikeringsgateway. Läs mer om [krav för konfigurationsserver](../../site-recovery/vmware-walkthrough-overview.md). Du ställer in konfigurationsservern bara en gång och du kan använda den för samtliga migreringar för samma region.

* **Processervern** är en replikerings-gateway som: 

  1. Tar emot replikeringsdata från virtuella datorer.
  2. Optimerar data med cachelagring, komprimering och kryptering.
  3. Skickar data till ett lagringskonto. 

  Den hanterar push-installation av mobilitetstjänsten till virtuella datorer och utför automatisk identifiering av virtuella datorer. Standard-process-servern är installerad på konfigurationsservern. Du kan distribuera ytterligare fristående processervrar om du vill skala distributionen. Läs mer om [Metodtips för distribution av processerver](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) och [distribuera ytterligare processervrar](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Du ställer in processervern bara en gång och du kan använda den för samtliga migreringar för samma region.

* **Mobilitetstjänsten** är en komponent som har distribuerats på varje standard virtuell dator som du vill replikera. Den samlar in skrivna data på den standard virtuella datorn och vidarebefordrar dem till processervern. Läs mer om [replikeras förutsättningar dator](../../site-recovery/vmware-walkthrough-overview.md).

Den här bilden visar hur komponenterna samverkar:

![Interaktionen för Site Recovery-komponenter][15]

> [!NOTE]
> Site Recovery stöder inte migreringen av lagringsutrymmen-diskarna.

Ytterligare komponenter för andra scenarier, se [scenariearkitekturen](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Azure essentials

Det här är Azure-kraven för det här migreringsscenariot:

* En Azure-prenumeration.
* En Azure premium storage-konto att lagra replikerade data.
* Azure-nätverk som virtuella datorer ska ansluta till när de skapas vid en redundansväxling. Azure-nätverket måste vara i samma region som det som Site Recovery körs.
* Ett Azure standard storage-konto för att lagra replikeringsloggar. Detta kan vara samma lagringskonto för VM-diskarna som ska migreras.

## <a name="prerequisites"></a>Förutsättningar

* Förstå komponenterna i relevanta migrering scenariot i föregående avsnitt.
* Planera din driftstopp genom att lära dig mer om [redundans i Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Installation och migrering av steg

Du kan använda Site Recovery för att migrera virtuella Azure IaaS-datorer mellan regioner eller i samma region. Följande instruktioner är skräddarsydda för den här Migreringsscenario från artikeln [replikera virtuella VMware-datorer eller fysiska servrar till Azure](../../site-recovery/vmware-walkthrough-overview.md). Följ länkarna för detaljerade anvisningar förutom anvisningarna i den här artikeln.

### <a name="step-1-create-a-recovery-services-vault"></a>Steg 1: skapar ett Recovery Services-valv

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Välj **skapa en resurs** > **Management** > **Backup** och **Site Recovery (OMS)**. Du kan även välja **Bläddra** > **Recovery Services-valv** > **Lägg till**. 
3. Ange en region som virtuella datorer kommer att replikeras till. Välj den region där din virtuella källdatorer och källa storage-konton är huruvida i samma region. 

### <a name="step-2-choose-your-protection-goals"></a>Steg 2: Välja skyddsmål 

1. På den virtuella datorn där du vill installera konfigurationsservern, öppna den [Azure-portalen](https://portal.azure.com).
2. Gå till **Recovery Services-valv** > **inställningar** > **Site Recovery** > **steg 1: Förbered infrastrukturen** > **Skyddsmål**.

   ![Bläddra till målet i rutan][2]

3. Under **skyddsmål**, i den första listrutan väljer du **till Azure**. Välj i den andra listan **inte virtualiserad / övrigt**, och välj sedan **OK**.

   ![Målet i rutan med fyllts i rutorna][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Steg 3: Konfigurera källmiljön (konfigurationsserver)

1. Ladda ned **enhetliga installationsprogrammet för Azure Site Recovery** och valvregistreringsnyckeln genom att gå till den **Förbered infrastruktur** > **Förbered källa**  >  **Lägg till Server** fönster. 
 
   Du måste valvregistreringsnyckeln för att köra det enhetliga installationsprogrammet. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Gå till fönstret Lägg till Server][4]

2. I den **Lägg till Server** fönstret Lägg till en konfigurationsserver.

   ![Lägg till Server fönstret med konfigurationsservern har valts][5]

3. Kör enhetliga installationsprogrammet för att installera konfigurationsservern och processervern på den virtuella datorn som du använder som konfigurationsserver. Du kan [igenom skärmbilderna](../../site-recovery/vmware-walkthrough-overview.md) att slutföra installationen. Du kan referera till följande skärmbilder för steg som angetts för den här Migreringsscenario.

   1. I **innan du börjar**väljer **installera konfigurationsservern och processervern**.

      ![Innan du börjar sidan][6]

   2. I **registrering**, bläddra och välj den certifikatnyckel som du hämtade från valvet.

      ![Registreringssidan][7]

   3. I **Miljöinformation** väljer du om du ska replikera virtuella VMwares-datorer. Det här scenariot för migrering, välja **nr**.

      ![Miljö-informationssida][8]

4. När installationen är klar, gör du följande i den **Microsoft Azure Site Recovery konfigurationsserver** fönster:
 
   1. Använd den **hantera konton** fliken för att skapa kontot som Site Recovery kan använda för automatisk identifiering. (Konfigurera kontot är inte relevant i scenariot om hur du skyddar fysiska datorer, men du behöver minst ett konto för att aktivera något av följande steg. I det här fallet, kan du kalla kontot och lösenordet som helst.) 
   2. Använd den **Vault registrering** flik för att ladda upp valvautentiseringsfilen.

      ![Fliken för registrering av valvet][9]

### <a name="step-4-set-up-the-target-environment"></a>Steg 4: Konfigurera målmiljön

Välj **Förbered infrastruktur** > **Target**, och ange den distributionsmodell som du vill använda för virtuella datorer efter redundansväxling. Du kan välja **klassiska** eller **Resource Manager**, beroende på ditt scenario.

![Mål-fönstret][10]

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk. 

> [!NOTE]
> Om du använder ett premium storage-konto för replikerade data måste du ställa in en ytterligare ett standardlagringskonto för att lagra replikeringsloggar.

### <a name="step-5-set-up-replication-settings"></a>Steg 5: Konfigurera replikeringsinställningar

Så här kontrollerar du att konfigurationsservern är har associerats med den replikeringsprincip som du skapar, [konfigurera replikeringsinställningar](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Steg 6: Planera kapacitet

1. Använd den [kapacitetsplaneringsverktyget](../../site-recovery/site-recovery-capacity-planner.md) för att beräkna bandbredd, lagring och andra krav för dina behov. 
2. När du är klar väljer du **Ja, jag har gjort det** i **har du slutfört kapacitetsplanering?**.

   ![Kryssrutan för att bekräfta att du slutfört kapacitetsplanering][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Steg 7: Installera mobilitetstjänsten och aktivera replikering

1. Du kan välja att [push-installation](../../site-recovery/vmware-walkthrough-overview.md) i dina virtuella källdatorer eller till [manuellt installera mobilitetstjänsten](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) på dina virtuella källdatorer. Du hittar behovet av push-överföra installation och sökvägen till manuell installationsprogrammet i den angivna länken. Om du gör en manuell installation, kan du behöva använda en intern IP-adress för att hitta konfigurationsservern.

   ![Konfigurationssidan för Server-information][12]

   Den redundansväxlade virtuella datorn har två temporära diskar: en från den primära virtuella datorn och den andra skapades under etableringen av den virtuella datorn i återställningsregionen. Installera mobilitetstjänsten innan du aktiverar replikering för att undanta den temporära disken före replikering. Mer information om hur du utesluter den temporära disken finns [undanta diskar från replikering](../../site-recovery/vmware-walkthrough-overview.md).

2. Aktivera replikering på följande sätt:
   1. Välj **replikera program** > **källa**. När du har aktiverat replikering för första gången, Välj **+ replikera** i valvet för att aktivera replikering för ytterligare datorer.
   2. I steg 1, ställa in **källa** som processervern.
   3. I steg 2, ange postredundant distributionsmodell, ett premium storage-konto för att migrera till ett standardlagringskonto för att spara loggarna och ett virtuellt nätverk för att växla till.
   4. I steg 3, lägger du till skyddade virtuella datorer efter IP-adress. (Du kanske behöver en intern IP-adress för att hitta dem.)
   5. I steg 4 konfigurera egenskaper genom att välja de konton som du skapade tidigare på processervern.
   6. I steg 5, Välj den replikeringsprincip som du skapade tidigare i ”steg 5: Konfigurera replikeringsinställningar ”.
   7. Välj **OK**.

   > [!NOTE]
   > När en Azure-dator har frigjorts och igång igen, finns det ingen garanti för att den får samma IP-adress. Replikering i det här scenariot kanske inte fungerar korrekt om IP-adressen för konfigurationsservern/serverprocessen eller skyddade virtuella Azure-datorer ändras.

   ![Aktivera replikering fönstret med källan som valts][13]

När du utformar din Azure Storage-miljö rekommenderar vi att du använder separata lagringskonton för varje virtuell dator i en tillgänglighetsuppsättning. Vi rekommenderar att du följer bästa praxis i storage-skiktet till [Använd flera lagringskonton för varje tillgänglighetsuppsättning](../linux/manage-availability.md). Distribuera VM-diskar till flera lagringskonton hjälper till att förbättra tillgängligheten för lagring och distribuerar den i/o mellan Azure storage-infrastruktur.

Om dina virtuella datorer finns i en tillgänglighetsuppsättning, istället för att replikera diskar på alla virtuella datorer till en storage-konto, rekommenderar vi migrerar flera gånger för flera virtuella datorer. På så sätt kan de virtuella datorerna i samma tillgänglighetsuppsättning delar inte ett enda lagringskonto. Använd den **Aktivera replikering** fönstret för att ställa in en mål-lagringskontot för varje virtuell dator i taget.
 
Du kan välja en postredundant distributionsmodell enligt dina behov. Om du väljer Azure Resource Manager som din postredundant distributionsmodell du kan redundansväxla en virtuell dator (Resource Manager) till en virtuell dator (Resource Manager) eller du kan redundansväxla en virtuell dator (klassisk) till en virtuell dator (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Steg 8: Köra ett redundanstest

Om du vill kontrollera om din replikeringen är klar, Välj din Site Recovery-instans och välj sedan **inställningar** > **replikerade objekt**. Du ser statusen och procentandelen din replikeringsprocessen. 

Efter den inledande replikeringen är klar, köra ett redundanstest för att verifiera din replikeringsstrategi. Detaljerade anvisningar för ett redundanstest i [kör ett redundanstest i Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Innan du kör någon redundans kan du kontrollera att dina virtuella datorer och replikeringsstrategi uppfyller kraven. Läs mer om hur du kör ett redundanstest [testa redundans till Azure i Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Du kan se statusen för din testning av redundans i **inställningar** > **jobb** > *YOUR_FAILOVER_PLAN_NAME*. Du kan se en specifikation av steg och lyckade/misslyckade resultat i fönstret. Om redundanstestningen misslyckas i något steg, väljer du steget för att kontrollera felmeddelandet. 

### <a name="step-9-run-a-failover"></a>Steg 9: Köra en redundansväxling

När testet har redundans slutförts, köra en redundans för att migrera dina diskar till Premium Storage och replikera de Virtuella datorinstanserna. Följ de detaljerade steg i [köra en redundans](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Se till att välja **stänga av virtuella datorer och synkronisera senaste data**. Det här alternativet anger att Site Recovery bör försöka att Stäng de skyddade virtuella datorerna och synkronisera data så att den senaste versionen av data kommer att redundansväxlas. Om du inte väljer det här alternativet eller försöket inte lyckas, kommer växling vid fel från den senaste tillgängliga återställningspunkten för den virtuella datorn. 

Site Recovery skapar en VM-instans vars typ är samma som eller liknande till en Premium Storage-kompatibel virtuell dator. Du kan kontrollera prestanda och priset för olika VM-instanser genom att gå till [prissättning för Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) eller [prissättning för Linux Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Eftermigreringen

1. **Konfigurera replikerade virtuella datorerna i tillgänglighetsuppsättningen förekommande**. Site Recovery stöder inte migrera virtuella datorer tillsammans med tillgänglighetsuppsättningen. Beroende på distributionen för den replikerade virtuella datorn, gör du något av följande:
   * För en virtuell dator som skapats via den klassiska distributionsmodellen: Lägg till den virtuella datorn i tillgänglighetsuppsättningen i Azure-portalen. Detaljerade anvisningar finns i [lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning](../linux/classic/configure-availability-classic.md).
   * För en virtuell dator som skapats via Resource Manager-distributionsmodellen: Spara din konfiguration av den virtuella datorn och ta sedan bort och återskapa de virtuella datorerna i tillgänglighetsuppsättningen. Du gör detta genom att använda skriptet på [ange Azure Resource Manager VM-Tillgänglighetsuppsättning](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Innan du kör det här skriptet, kontrollera dess begränsningar och planera din driftstopp.

2. **Ta bort gamla virtuella datorer och diskar**. Kontrollera att Premium-diskar är konsekventa med källdiskarna och att de nya virtuella datorerna utför samma funktion som virtuella datorer. Ta bort den virtuella datorn och ta bort diskar från källan lagringskonton i Azure-portalen. Om det finns ett problem i som disken är inte bort även om du har tagit bort den virtuella datorn, se [felsöka fel vid borttagning av storage resource](storage-resource-deletion-errors.md).

3. **Rensa Azure Site Recovery-infrastruktur**. Om Site Recovery är inte längre behövs kan rensa du sin infrastruktur. Ta bort replikerade objekt och konfigurationsservern återställningsprincipen och tar bort Azure Site Recovery-valvet.

## <a name="troubleshooting"></a>Felsökning

* [Övervaka och felsöka skydd för virtuella datorer och fysiska servrar](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Nästa steg

Specifika scenarier för migrering av virtuella datorer finns i följande resurser:

* [Migrera virtuella Azure-datorer mellan Lagringskonton](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Ladda upp en virtuell hårddisk i Linux](upload-vhd.md)
* [Migrera virtuella datorer från Amazon AWS till Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Se även följande resurser för att lära dig mer om Azure Storage och Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Välj en disktyp för virtuella IaaS-datorer](disks-types.md)

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
