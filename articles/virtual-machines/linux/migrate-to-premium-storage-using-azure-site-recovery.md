---
title: Migrera din virtuella Linux-datorer till Azure Premium-lagring med Azure Site Recovery | Microsoft Docs
description: "Migrera dina befintliga virtuella datorer till Azure Premium-lagring med hjälp av Site Recovery. Premium-lagring ger stöd för I/O-intensiva arbetsbelastningar som körs på Azure Virtual Machines diskar med hög prestanda, låg latens."
services: virtual-machines-linux
cloud: Azure
documentationcenter: na
author: luywang
manager: jeconnoc
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.openlocfilehash: 5d6eb958169b7bf04e206c861250ffd98670652b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrera till Premium-lagring med hjälp av Azure Site Recovery

[Azure Premium-lagring](premium-storage.md) ger stöd för virtuella datorer (VM) som körs I/O-intensiva arbetsbelastningar diskar med hög prestanda, låg latens. Den här guiden hjälper dig att migrera dina Virtuella diskar från ett standardlagringskonto till ett premiumlagringskonto med hjälp av [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery är en Azure-tjänst som stödjer din strategi för affärskontinuitet och haveriberedskap genom att samordna replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt datacenter. Vid driftstopp på den primära platsen växlar du över till den sekundära platsen så att program och arbetsbelastningar som är tillgängliga. Du växlar tillbaka till den primära platsen när den återgår till normal drift. 

Site Recovery tillhandahåller redundanstestning så att den stöder haveriberedskap utan att påverka produktionsmiljöer. Du kan köra redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvens) för oväntade haverier. I scenariot för att migrera till Premium-lagring kan du använda den [redundans i Site Recovery](../../site-recovery/site-recovery-failover.md) att migrera måldiskarna till ett premiumlagringskonto.

Vi rekommenderar att du migrerar till Premium-lagring med hjälp av Site Recovery eftersom det här alternativet ger minimal avbrottstid. Det här alternativet förhindrar också manuell körningen av kopierar diskar och skapa nya virtuella datorer. Site Recovery kommer systematiskt kopiera diskarna och skapa nya virtuella datorer under växling vid fel. 

Site Recovery stöder ett antal typer av redundansväxlingar med minimal eller ingen avbrottstid. För att planera din driftstopp och beräkna dataförlust, finns det [typer av redundans i Site Recovery](../../site-recovery/site-recovery-failover.md). Om du [förbereda för att ansluta till virtuella Azure-datorer efter redundans](../../site-recovery/vmware-walkthrough-overview.md), du ska kunna ansluta till den virtuella Azure-datorn med RDP efter växling vid fel.

![Disaster recovery-diagram][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery-komponenter

Dessa Site Recovery-komponenter är relevanta för den här Migreringsscenario:

* **Konfigurationsservern** är en Azure VM som samordnar kommunikationen och hanterar processer för replikering och återställning av data. På den virtuella datorn, du kör en enda installationsfil för att installera konfigurationsservern och en ytterligare komponent kallas en processerver som en gateway för replikering. Läs mer om [server konfigurationskrav](../../site-recovery/vmware-walkthrough-overview.md). Du ställer in konfigurationsservern bara en gång och du kan använda den för samtliga migreringar för samma region.

* **Processervern** är en replikerings-gateway som: 

  1. Tar emot replikeringsdata från källan virtuella datorer.
  2. Optimerar data med cachelagring, komprimering och kryptering.
  3. Skickar data till ett lagringskonto. 

  Den hanterar push-installation av mobilitetstjänsten till källdatorn virtuella datorer och utför automatisk identifiering av virtuella källdatorer. Standard-process-servern är installerad på konfigurationsservern. Du kan distribuera ytterligare fristående servrar för att skala din distribution. Läs mer om [Metodtips för distribution av processerver](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) och [distribution av ytterligare servrar](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Du ställer in processervern bara en gång och du kan använda den för samtliga migreringar för samma region.

* **Mobilitetstjänsten** är en komponent som distribueras på varje standard virtuell dator som du vill replikera. Den samlar in dataskrivningar på standard virtuella datorn och vidarebefordrar dem till processervern. Läs mer om [replikeras förutsättningar dator](../../site-recovery/vmware-walkthrough-overview.md).

Den här bilden illustrerar hur dessa komponenter:

![Samverkan mellan Site Recovery-komponenter][15]

> [!NOTE]
> Site Recovery har inte stöd för migrering av lagringsutrymmen-diskarna.

Ytterligare komponenter för andra scenarier finns [scenariots arkitektur](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Azure essentials

Dessa är Azure kraven för den här Migreringsscenario:

* En Azure-prenumeration.
* Ett Azure premium-lagringskonto att lagra replikerade data.
* Azure-nätverk som virtuella datorer ska ansluta till när de skapas under växling vid fel. Virtuella Azure-nätverket måste vara i samma region som Site Recovery körs.
* Ett Azure standardlagring-konto för att lagra replikeringsloggar. Det kan vara samma lagringskonto för de Virtuella diskar som ska migreras.

## <a name="prerequisites"></a>Förutsättningar

* Förstå komponenterna i relevanta migrering scenariot i föregående avsnitt.
* Planera din driftstopp genom att lära dig mer om [redundans i Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Installation och migrering av steg

Du kan använda Site Recovery för att migrera Azure IaaS-VM mellan regioner eller i samma region. Följande instruktioner är skräddarsydda för den här Migreringsscenario från artikeln [replikera virtuella VMware-datorer eller fysiska servrar till Azure](../../site-recovery/vmware-walkthrough-overview.md). Följ länkarna för detaljerade anvisningar förutom anvisningarna i den här artikeln.

### <a name="step-1-create-a-recovery-services-vault"></a>Steg 1: Skapa ett Recovery Services-valv

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Välj **skapar du en resurs** > **Management** > **säkerhetskopiering** och **Site Recovery (OMS)**. Du kan också välja **Bläddra** > **Återställningstjänstvalvet** > **Lägg till**. 
3. Ange en region som virtuella datorer kommer att replikeras till. Välj den region där din virtuella källdatorer och källa storage-konton är för migrering i samma region. 

### <a name="step-2-choose-your-protection-goals"></a>Steg 2: Välja skyddsmål 

1. På den virtuella datorn där du vill installera konfigurationsservern, öppna den [Azure-portalen](https://portal.azure.com).
2. Gå till **Recovery Services-valv** > **inställningar** > **Site Recovery** > **steg 1: Förbered Infrastruktur** > **skyddsmål**.

   ![Bläddra till rutan skydd målet][2]

3. Under **skyddsmål**, Välj i listan listruta **till Azure**. Välj i den andra listrutan **inte virtualiserade / andra**, och välj sedan **OK**.

   ![Skydd målet rutan med ifyllda rutor][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Steg 3: Konfigurera källmiljön (konfigurationsserver)

1. Hämta **Unified installationsprogram för Azure Site Recovery** och valvregistreringsnyckeln genom att gå till den **Förbered infrastruktur** > **Förbered källa**  >  **Lägg till Server** fönster. 
 
   Du behöver valvregistreringsnyckeln för att köra enhetlig installationen. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Bläddra till fönstret Lägg till Server][4]

2. I den **Lägg till Server** fönstret Lägg till en konfigurationsserver.

   ![Lägg till Server rutan med konfigurationsservern markerat][5]

3. Kör Unified installationsprogrammet för att installera konfigurationsservern och processervern på den virtuella datorn som du använder som konfigurationsservern. Du kan [igenom skärmbilderna](../../site-recovery/vmware-walkthrough-overview.md) att slutföra installationen. Du kan referera till följande skärmbilder för steg som angetts för den här Migreringsscenario.

   1. I **innan du börjar**väljer **installera konfigurationsservern och processervern**.

      ![Innan du börjar sida][6]

   2. I **registrering**, bläddra och välj den certifikatnyckel som du hämtade från valvet.

      ![Registreringssidan][7]

   3. I **Miljöinformation** väljer du om du ska replikera virtuella VMwares-datorer. Den här Migreringsscenario väljer **nr**.

      ![Informationssidan för miljö][8]

4. När installationen är klar, gör du följande i den **Microsoft Azure Site Recovery Configuration Server** fönster:
 
   1. Använd den **hantera konton** att skapa kontot som Site Recovery kan använda för automatisk identifiering. (Konfigurera kontot är inte relevant i scenario om hur du skyddar fysiska datorer men du behöver minst ett konto för att aktivera någon av följande steg. I det här fallet, kan du kalla kontot och lösenordet som helst.) 
   2. Använd den **valvet registrering** att ladda upp valvautentiseringsfilen.

      ![Fliken för registrering av valvet][9]

### <a name="step-4-set-up-the-target-environment"></a>Steg 4: Konfigurera målmiljön

Välj **Förbered infrastruktur** > **mål**, och ange den distributionsmodell som du vill använda för virtuella datorer efter redundans. Du kan välja **klassiska** eller **Resource Manager**, beroende på ditt scenario.

![Mål-fönstret][10]

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk. 

> [!NOTE]
> Om du använder ett premium-lagringskonto för replikerade data, måste du konfigurera en ytterligare ett standardlagringskonto för att lagra replikeringsloggar.

### <a name="step-5-set-up-replication-settings"></a>Steg 5: Konfigurera replikeringsinställningar

Så här kontrollerar du att din konfigurationsservern är har associerat med replikeringsprincip som du skapar, [konfigurera replikeringsinställningar](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Steg 6: Planera kapacitet

1. Använd den [kapacitetsplaneringsverktyg](../../site-recovery/site-recovery-capacity-planner.md) för att beräkna bandbredd, lagring och andra krav för att uppfylla dina replikering behov. 
2. När du är klar väljer du **Ja, jag har gjort det** i **har du planerat kapaciteten?**.

   ![Kryssrutan för att bekräfta att du slutfört kapacitetsplanering][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Steg 7: Installera mobilitetstjänsten och aktivera replikering

1. Du kan välja att [push-installation](../../site-recovery/vmware-walkthrough-overview.md) till dina virtuella källdatorer eller till [manuellt installera mobilitetstjänsten](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) på dina virtuella källdatorer. Du kan hitta krav för push-överföring installation och sökvägen till manuell installationsprogrammet i den angivna länken. Om du utför en manuell installation kan du behöva använda en intern IP-adress för att hitta konfigurationsservern.

   ![Konfigurationssidan för serverinformation][12]

   Kunde inte över VM har två tillfälliga diskar: en från den primära virtuella datorn och den andra skapades under etableringen av den virtuella datorn i området för återställning. Uteslut tillfälliga disken före replikering genom att installera mobilitetstjänsten innan du aktiverar replikering. Mer information om hur du utesluter den tillfälliga disken finns [Exkludera diskar från replikeringen](../../site-recovery/vmware-walkthrough-overview.md).

2. Aktivera replikering på följande sätt:
   1. Välj **replikera program** > **källa**. När du har aktiverat replikering för första gången, Välj **+ replikera** i valvet för att aktivera replikering för ytterligare datorer.
   2. I steg 1, ställa in **källa** som processervern.
   3. Ange distributionsmodell efter en redundansväxling, ett premiumlagringskonto att migrera till ett standardlagringskonto för att spara loggar och ett virtuellt nätverk att växla till i steg 2.
   4. Lägga till skyddade virtuella datorer efter IP-adress i steg 3. (Du kanske måste en intern IP-adress du hittar dem.)
   5. Konfigurera egenskaper för i steg 4, genom att välja de konton som du skapat tidigare på processervern.
   6. I steg 5 väljer du den replikeringsprincip som du skapade tidigare i ”steg 5: Konfigurera replikeringsinställningar”.
   7. Välj **OK**.

   > [!NOTE]
   > När en Azure VM är frigjord och startas igen, finns det ingen garanti för att den får samma IP-adress. Replikering i det här scenariot kanske inte fungerar korrekt om IP-adressen för server-processen konfigurationsservern eller skyddade virtuella Azure-datorer ändras.

   ![Aktivera replikering rutan med källan som valts][13]

När du utformar din Azure Storage-miljö, rekommenderar vi att du använder separata storage-konton för varje virtuell dator i en tillgänglighetsuppsättning. Vi rekommenderar att du följer bästa praxis i lagringsskikt till [använder flera lagringskonton för varje tillgänglighetsuppsättning](../linux/manage-availability.md). Distribuera Virtuella diskar med flera lagringskonton hjälper oss för att förbättra lagerutrymme och distribuerar läsning/skrivning i Azure storage-infrastruktur.

Om din virtuella dator finns i en tillgänglighetsuppsättning, istället för att replikera diskar för alla virtuella datorer i ett lagringskonto, rekommenderar vi Migrera flera virtuella datorer på flera gånger. På så sätt kan de virtuella datorerna i samma tillgänglighetsuppsättning inte delar ett enda storage-konto. Använd den **Aktivera replikering** fönstret för att ställa in en destinationslagringskontot för varje virtuell dator i taget.
 
Du kan välja en postredundans distributionsmodell enligt dina behov. Om du väljer Azure Resource Manager som din postredundans distributionsmodell, kan du växla över en virtuell dator (Resource Manager) till en virtuell dator (Resource Manager) eller kan du växla över en virtuell dator (klassisk) till en virtuell dator (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>Steg 8: Köra ett redundanstest

Om du vill kontrollera om din replikeringen är klar väljer du Site Recovery-instansen och välj sedan **inställningar** > **replikerade objekt**. Du ser statusen och procentandelen din replikeringen. 

Efter den första replikeringen är klar, köra ett redundanstest för att verifiera din replikeringsstrategi för. Detaljerade anvisningar för att testa redundans finns [köra ett redundanstest i Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Innan du kör en växling vid fel, kontrollera att dina virtuella datorer och replikeringsstrategi uppfyller kraven. Läs mer om hur du kör ett redundanstest [Redundanstestningen till Azure Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Du kan se status för din testning av redundans i **inställningar** > **jobb** > *YOUR_FAILOVER_PLAN_NAME*. I fönstret visas en sammanställning av steg och lyckade/misslyckade resultat. Om du testar redundansen misslyckas när som helst, väljer du steget för att kontrollera felmeddelandet. 

### <a name="step-9-run-a-failover"></a>Steg 9: Kör en växling vid fel

När testet har växling vid fel slutförts, kör en redundansväxling för att migrera diskarna till Premium-lagring och replikera VM-instanser. Detaljerade anvisningarna i [kör en redundansväxling](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Se till att välja **Stäng virtuella datorer och synkronisera senaste data**. Det här alternativet anger att Site Recovery ska försöka stänga av de skyddade virtuella datorerna och synkronisera data så att den senaste versionen av data kommer att redundansväxlas. Om du inte väljer det här alternativet eller försöket inte lyckas, att växling vid fel från den senaste tillgängliga återställningspunkten för den virtuella datorn. 

Site Recovery skapar en VM-instans vars typ är samma som eller liknande till en Premium Storage-kompatibla virtuell dator. Du kan kontrollera prestanda och priset för olika VM-instanser genom att gå till [prissättning för Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) eller [priser för Linux virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Eftermigreringen

1. **Konfigurera replikerade virtuella datorerna till tillgänglighetsuppsättning om tillämpligt**. Site Recovery stöder inte migrera virtuella datorer tillsammans med tillgänglighetsuppsättningen. Beroende på distribution av den replikerade virtuella datorn, gör du något av följande:
   * För en virtuell dator som skapats via den klassiska distributionsmodellen: lägga till den virtuella datorn till tillgänglighetsuppsättning i Azure-portalen. Detaljerade anvisningar finns i [lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning](../linux/classic/configure-availability-classic.md).
   * För en virtuell dator som skapats via Resource Manager-distributionsmodellen: spara konfigurationen av den virtuella datorn och ta sedan bort och återskapa de virtuella datorerna i tillgänglighetsuppsättningen. Gör du genom att använda skript på [ange Azure Resource Manager VM Tillgänglighetsuppsättning](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Innan du kör det här skriptet, kontrollera vissa begränsningar och planera din driftstopp.

2. **Ta bort gamla virtuella datorer och diskar**. Kontrollera att diskarna Premium är konsekventa med källan diskar och att de nya virtuella datorerna utför samma funktion som virtuella källdatorer. Ta bort den virtuella datorn och ta bort diskar från källan storage-konton i Azure-portalen. Om det finns ett problem i som disken är inte bort även om du har tagit bort den virtuella datorn, se [Felsöka när du tar bort virtuella hårddiskar](../../storage/common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

3. **Rensa Azure Site Recovery-infrastruktur**. Om Site Recovery inte längre behövs, kan du rensa sin infrastruktur. Ta bort replikerade objekt, konfigurationsservern och återställningsprincipen och tar bort Azure Site Recovery-valvet.

## <a name="troubleshooting"></a>Felsökning

* [Övervaka och felsöka skydd för virtuella datorer och fysiska servrar](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Nästa steg

Specifika scenarier för att migrera virtuella datorer finns i följande resurser:

* [Migrera Azure virtuella datorer mellan Storage-konton](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Överför en virtuell hårddisk för Linux](upload-vhd.md)
* [Migrering av virtuella datorer från Amazon AWS till Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Se även följande resurser för att du lär dig mer om Azure Storage- och virtuella datorer i Azure:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Premium Storage: Lagring med höga prestanda för virtuella Azure-datorarbetsbelastningar](premium-storage.md)

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
