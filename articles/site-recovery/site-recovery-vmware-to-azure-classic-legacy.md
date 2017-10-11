---
title: "Replikera virtuella VMware-datorer och fysiska servrar till Azure (klassiska äldre) | Microsoft Docs"
description: "Beskriver hur du replikera lokala virtuella datorer och Windows-/ Linux fysiska servrar till Azure med Azure Site Recovery i en äldre distribution i den klassiska portalen."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: f980fb52-8ec2-4dd9-85e2-8e52e449f1ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
ms.openlocfilehash: 325be23cffc9c728a8af6f92a0f3dce6d31da4ae
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>Replikera virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery med den klassiska portalen (bakåtkompatibelt)
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmware-to-azure.md)
> * [Klassisk portal](site-recovery-vmware-to-azure-classic.md)
> * [Klassiska portalen (bakåtkompatibelt)](site-recovery-vmware-to-azure-classic-legacy.md)
>
>

Välkommen till Azure Site Recovery! Den här artikeln beskriver en äldre distribution för att replikera lokala virtuella VMware-datorer eller Windows-/ Linux fysiska servrar till Azure med Azure Site Recovery på den klassiska portalen.

## <a name="overview"></a>Översikt
Organisationer behöver en BCDR-strategi som beskriver hur appar, arbetsbelastningar och data fungerar och är tillgängliga under planerade och oplanerade driftavbrott och som ser till att systemets normala drifttillstånd återställs så fort som möjligt. Avsikten med en BCDR-strategi är att skydda affärsdata och se till att de kan återställas samt att säkerställa att arbetsbelastningar förblir tillgängliga i händelse av allvarliga fel.

Site Recovery är en Azure-tjänst som stödjer din BCDR-strategi genom att dirigera replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt datacenter. Vid driftstopp på den primära platsen växlar du över till den sekundära platsen så att program och arbetsbelastningar fortsätter att vara tillgängliga. Du växlar tillbaka till den primära platsen när den har återgått till normal drift. Läs mer i [Vad är Azure Site Recovery?](site-recovery-overview.md)

> [!WARNING]
> Den här artikeln innehåller **äldre instruktioner**. Använd inte det för nya distributioner. I stället [följer du dessa instruktioner](site-recovery-vmware-to-azure.md) du distribuerar Site Recovery i Azure-portalen eller [Följ dessa instruktioner](site-recovery-vmware-to-azure-classic.md) att konfigurera förbättrad distribution i den klassiska portalen. Om du redan har distribuerat med den metod som beskrivs i den här artikeln, rekommenderar vi att du migrerar till den förbättrade distributionen i den klassiska portalen.
>
>

## <a name="migrate-to-the-enhanced-deployment"></a>Migrera till den förbättrade distributionen
Det här avsnittet gäller endast om du redan har distribuerat Site Recovery med hjälp av anvisningarna i den här artikeln.

Att migrera den befintliga distributionen måste du:

1. Distribuera nya Site Recovery-komponenter på den lokala platsen.
2. Konfigurera behörigheterna för automatisk identifiering av virtuella VMware-datorer på den nya konfigurationsservern.
3. Identifiera VMware-servrar med den nya konfigurationsservern.
4. Skapa en ny skyddsgrupp med nya konfigurationsservern.

Innan du börjar:

* Vi rekommenderar att du konfigurerar en underhållsperiod för migrering.
* Den **migrera datorer** alternativet är bara tillgängligt om du har befintliga skyddsgrupper som skapades under en äldre distribution.
* När du har slutfört stegen i migreringen kan det ta 15 minuter eller längre tid att uppdatera autentiseringsuppgifterna, och för att identifiera och uppdatera virtuella datorer så att du kan lägga till dem i en skyddsgrupp. Du kan uppdatera manuellt i stället för att vänta.

Migrera enligt följande:

1. Läs mer om [förbättrad distribution i den klassiska portalen](site-recovery-vmware-to-azure-classic.md). Granska de förbättrade [arkitektur](site-recovery-vmware-to-azure-classic.md), och [krav](site-recovery-vmware-to-azure-classic.md).
2. Avinstallera mobilitetstjänsten från datorer som du replikerar. En ny version av tjänsten kommer att installeras på datorer när du lägger till dem till den nya skyddsgruppen.
3. Hämta en [valvregistreringsnyckel](site-recovery-vmware-to-azure-classic.md) och [kör installationsguiden för enhetlig](site-recovery-vmware-to-azure-classic.md) att installera konfigurationsservern, processervern och huvudmålservern server-komponenter. Läs mer om [kapacitetsplanering](site-recovery-vmware-to-azure-classic.md).
4. [Ställ in autentiseringsuppgifter](site-recovery-vmware-to-azure-classic.md) som Site Recovery kan använda för att få åtkomst till VMware-server för att automatiskt identifiera virtuella VMware-datorer. Lär dig mer om [nödvändiga behörigheter](site-recovery-vmware-to-azure-classic.md).
5. Lägg till [vCenter-servrar eller vSphere värdar](site-recovery-vmware-to-azure-classic.md). Det kan ta 15 minuter för servrar som ska visas i Site Recovery-portalen.
6. Skapa en [ny skyddsgrupp](site-recovery-vmware-to-azure-classic.md). Det kan ta upp till 15 minuter för portalen för att uppdatera så att de virtuella datorerna ska identifieras och visas. Om du inte vill vänta kan du markera hanteringsserverns namn (inte på den) > **uppdatera**.
7. Klicka på under den nya skyddsgruppen **migrera datorer**.

    ![Lägg till konto](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)
8. I **Välj datorer** väljer du vill migrera från skyddsgruppen och de datorer som du vill migrera.

    ![Lägg till konto](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)
9. I **konfigurera Målinställningar** ange om du vill använda samma inställningar för alla datorer och välja processervern och Azure storage-konto. Om du inte har någon separat processerver är den IP-adressen för servern konfigurationsservern.

    ![Lägg till konto](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] [Migrering av lagringskonton](../resource-group-move-resources.md) mellan resursgrupper i samma prenumeration eller mellan prenumerationer stöds inte för lagringskonton som används för att distribuera Site Recovery.

1. I **ange konton**, väljer du det konto som du skapade för processervern att få åtkomst till datorn för att vidarebefordra den nya versionen av mobilitetstjänsten.

   ![Lägg till konto](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)
2. Site Recovery kommer att migrera dina replikerade data till Azure storage-konto som du angav. Alternativt kan du återanvända storage-konto som du använde i äldre distributionen.
3. När jobbet har slutförts synkroniseras automatiskt virtuella datorer. När synkroniseringen har slutförts kan du ta bort de virtuella datorerna från äldre skyddsgruppen.
4. Alla datorer som har migrerat kan du ta bort äldre skyddsgruppen.
5. Kom ihåg att ange egenskaper för redundans för datorer och Azure-nätverksinställningar när synkroniseringen är klar.
6. Om du har befintliga återställningsplaner kan du migrera dem till den förbättrade distributionen med den **migrera Recovery planera** alternativet. Du bör bara göra detta när alla skyddade datorer har migrerats.

   ![Lägg till konto](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

> [!NOTE]
> När du är klar med migreringen fortsätta med den [förbättrad artikel](site-recovery-vmware-to-azure-classic.md). Resten av äldre artikeln kommer inte längre att relevanta och du behöver inte följa stegen som beskrivs i it ** fler.
>
>

## <a name="what-do-i-need"></a>Vad behöver jag?
Det här diagrammet visar distributionen komponenterna.

![Nytt valv](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Du behöver det här:

| **Komponent** | **Distribution** | **Detaljer** |
| --- | --- | --- |
| **Konfigurationsserver** |En Azure standard A3 virtuell dator i samma prenumeration som Site Recovery. |Konfigurationsservern samordnar kommunikationen mellan skyddade datorer, processervern och huvudmålservern servrar i Azure. Den konfigurerar replikering och koordinater återställning i Azure vid en redundansväxling. |
| **Huvudmålservern** |En virtuell Azure-dator – antingen en Windows server baserat på en Windows Server 2012 R2-galleriet avbildningen (för att skydda Windows-datorer) eller som en Linux-server baserat på en bild med OpenLogic CentOS 6.6 galleriet (för att skydda Linux-datorer).<br/><br/> Tre storlekar är tillgängliga – Standard A4, Standard D14 och Standard DS4.<br/><br/> Servern är ansluten till samma Azure-nätverk som konfigurationsservern.<br/><br/> Du ställer in i Site Recovery-portalen |Den tar emot och lagrar replikerade data från dina skyddade datorer med anslutna virtuella hårddiskar skapas på blob-lagring i ditt Azure storage-konto.<br/><br/> Välj Standard DS4 specifikt för att konfigurera skydd för arbetsbelastningar som kräver konsekvent hög prestanda och låg latens med Premium-Lagringskontot. |
| **Processervern** |En lokal virtuell eller fysisk server som kör Windows Server 2012 R2<br/><br/> Vi rekommenderar att den placeras på samma nätverk och LAN-segment som de datorer som du vill skydda, men det kan köras i ett annat nätverk som skyddade datorer har L3 nätverket insyn i den.<br/><br/> Du ställa in och registrera den på konfigurationsservern i Site Recovery-portalen. |Skyddade datorer skicka replikeringsdata till processerver lokalt. Den har en diskbaserad cacheminne cachen replikeringsdata som tas emot. Ett antal åtgärder utförs på dessa data.<br/><br/> Detta optimerar data genom cachelagring, komprimering och kryptering innan du skickar in på huvudmålservern.<br/><br/> Den hanterar push-installation av Mobilitetstjänsten.<br/><br/> Den utför automatisk identifiering av virtuella VMware-datorer. |
| **Lokala datorer** |Lokala virtuella VMware-datorer eller fysiska servrar som kör Windows eller Linux. |Du kan konfigurera replikeringsinställningar som gäller en eller flera datorer. Du kan växla över en enskild dator eller vanligare, flera datorer som du samla in en återställningsplan. |
| **Mobilitetstjänsten** |Installerad på varje virtuell dator eller fysisk server som du vill skydda<br/><br/> Kan installeras manuellt eller flyttas och installerats automatiskt av processervern när du aktiverar replikering för en dator. |Mobilitetstjänsten skickar data till processervern under den första replikeringen (synkronisera). När datorn är i ett skyddat läge (efter att synkronisering har slutförts) mobilitetstjänsten fångar skrivning till disk i minnet och skickar dem till processervern. Applicationconsistency för Windows-servrar uppnås med VSS. |
| **Azure Site Recovery-valvet** |Du skapar ett Site Recovery-valv med en Azure-prenumeration och registrera servrar i valvet. |Valvet samordnar och styr replikering, redundans och återställning mellan din lokala plats och Azure. |
| **Replikeringsmekanism** |**Via Internet**– kommunicerar och replikerar data från skyddade lokala servrar till Azure med hjälp av säkra SSL/TLS-kanalen via internet. Detta är standardalternativet.<br/><br/> **VPN/ExpressRoute**– kommunicerar och replikerar data mellan lokala servrar och Azure via en VPN-anslutning. Du behöver konfigurera en plats-till-plats-VPN eller en ExpressRoute-anslutning mellan lokal plats och Azure-nätverk.<br/><br/> Väljer du hur du vill replikera under distributionen av Site Recovery. Du kan inte ändra mekanismen när den har konfigurerats utan att påverka replikering av befintliga datorer. |Varken alternativet måste du öppna alla inkommande nätverksportar på skyddade datorer. Alla nätverkskommunikation initieras från den lokala platsen. |

## <a name="capacity-planning"></a>Kapacitetsplanering
Huvudområden som du behöver tänka:

* **Källmiljö**– i VMware-infrastruktur, inställningar för datakälla datorn och krav.
* **Komponentservrarna**– den processervern och konfigurationsservern huvudmålservern

### <a name="considerations-for-the-source-environment"></a>Överväganden för källmiljön
* **Maximala diskstorleken**– aktuella maxstorleken för den disk som kan kopplas till en virtuell dator är 1 TB. Därför är den maximala storleken för en källdisken som kan replikeras också begränsad till 1 TB.
* **Maximal storlek per källa**– den maximala storleken för en enskild källdator är 31 TB (med 31 diskar) och en D14-instans som har etablerats för huvudmålservern.
* **Antal källor per huvudmålservern**– flera källdatorer som kan skyddas med en enda huvudmålserver. Men kan inte en enda källdatorn skyddas över flera huvudmålservern servrar eftersom som diskar replikera en virtuell Hårddisk som speglar storleken på disken har skapats i Azure blob storage och bifogas som en datadisk till huvudmålservern.  
* **Maximal dagliga förändringstakten per källa**– det finns tre faktorer som måste beaktas när du överväger rekommenderade förändringstakten per källa. Två IOPS krävs mål baserat överväganden på måldisken för varje åtgärd på källan. Det beror på att en gammal läsning och skrivning av nya data sker på måldisken.
  * **Varje dag ändrar som stöds av processervern**– en källdator kan sträcka sig över flera servrar. En enda process-server kan stöda upp till 1 TB dagliga förändringstakten. Därför är 1 TB maximala dagliga data ändra som stöds för en källdator.
  * **Maximalt dataflöde som stöds av måldisken**– maximalt omsättning per källdisken får inte vara mer än 144 GB/dag (med 8 kB skrivåtgärder storlek). Se tabellen i avsnittet huvudmålservern för genomströmning och IOPs för målet för olika skriva storlekar. Siffran måste delas av två eftersom varje källa IOP genererar 2 IOPS på måldisken. Läs mer om [Azure skalbarhets- och prestandamål](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) när du konfigurerar mål för premium-lagringskonton.
  * **Maximalt dataflöde som stöds av lagringskontot**– en källa kan sträcka sig över flera lagringskonton. Att ett lagringskonto tar högst 20 000 begäranden per sekund och att varje källa IOP genererar 2 IOPS på huvudmålservern får rekommenderar vi att du behåller antalet IOPS över källan till 10 000. Läs mer om [Azure skalbarhets- och prestandamål](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) när du konfigurerar källa för premium-lagringskonton.

### <a name="considerations-for-component-servers"></a>Överväganden för komponentservrarna
Tabell 1 sammanfattar storlekar för virtuella datorer för konfiguration och huvudmålservern servrar.

| **Komponent** | **Distribuerade Azure-instanser** | **Kärnor** | **Minne** | **Maximalt antal diskar** | **Diskens storlek** |
| --- | --- | --- | --- | --- | --- |
| Konfigurationsservern |Standard A3 |4 |7 GB |8 |1 023 GB |
| Huvudmålservern |Standard A4 |8 |14 GB |16 |1 023 GB |
| Standard D14 |16 |112 GB |32 |1 023 GB | |
| Standard DS4 |8 |28 GB |16 |1 023 GB | |

**Tabell 1**

#### <a name="process-server-considerations"></a>Bearbeta server-överväganden
Processen server storlek beror vanligtvis på dagliga förändringstakten över alla skyddade arbetsbelastningar.

* Du behöver tillräckligt beräkning att utföra åtgärder som infogade komprimering och kryptering.
* Processervern använder diskbaserade cachen. Kontrollera att rekommenderade cache-utrymme och diskgenomflödet är tillgänglig för att underlätta dataändringarna lagras vid flaskhalsar i nätverket eller avbrott.
* Se till att tillräckligt med bandbredd så att processervern kan överföra data till huvudmålservern för att tillhandahålla kontinuerligt dataskydd.

Tabell 2 innehåller en översikt över processen server riktlinjer.

| **Dataändringshastighet** | **CPU** | **Minne** | **Cachestorleken för disk** | **Cache-diskgenomflödet** | **Bandbredd ingång-/ utgång** |
| --- | --- | --- | --- | --- | --- |
| < 300 GB |4 vCPUs (2 sockets * 2 kärnor @ 2,5 GHz) |4 GB |600 GB |7 till 10 MB per sekund |30 Mbit/s/21 Mbit/s |
| 300 600 GB |8 vCPUs (2 sockets * 4 kärnor @ 2,5 GHz) |6 GB |600 GB |11-15 MB per sekund |60 Mbit/s/42 Mbit/s |
| 600 GB till 1 TB |12 vCPUs (2 sockets * @ 2,5 GHz-6 kärnor) |8 GB |600 GB |16 till 20 MB per sekund |100 Mbit/s/70 Mbit/s |
| > 1 TB |Distribuera en annan processerver | | | | |

**Tabell 2**

Där:

* Ingång är download bandbredd (intranät mellan käll-och).
* Utgående är Överför bandbredd (internet mellan processervern och huvudmålservern). Utgående siffror förutsätta genomsnittlig 30% processen server komprimering.
* Disk en separat OS-disk på minst 128 GB rekommenderas för alla servrar för cachen.
* För cache diskgenomflödet användes följande lagringen för prestandamätningar: 8 SAS-enheter på 10 K RPM med RAID 10-konfiguration.

#### <a name="configuration-server-considerations"></a>Överväganden vid konfiguration av servern
Varje konfigurationsservern har stöd för upp till 100 källdatorer med 3 och 4 volymer. Om distributionen är större rekommenderar vi att du distribuerar en annan konfigurationsservern. Se tabell 1 för virtuell dator standardegenskaperna för konfigurationsservern.

#### <a name="master-target-server-and-storage-account-considerations"></a>Huvudmålservern server- och överväganden för användarkonton
Lagring för varje huvudmålservern innehåller en OS-disk, en kvarhållningsvolymen och datadiskar. Kvarhållningsenhetens underhåller diskändringarna journal för varaktighet för kvarhållningsperiod som definierats i Site Recovery-portalen.  Finns i tabell 1 för virtuella datorns egenskaper för huvudmålservern. Tabell 3 visar hur diskar för A4 används.

| **Instans** | **OS-disk** | **Kvarhållning** | **Datadiskar** |
| --- | --- | --- | --- |
| **Kvarhållning** |**Datadiskar** | | |
| Standard A4 |1 disk (1 * 1 023 GB) |1 disk (1 * 1 023 GB) |15 diskar (15 * 1 023 GB) |
| Standard D14 |1 disk (1 * 1 023 GB) |1 disk (1 * 1 023 GB) |31 diskar (15 * 1 023 GB) |
| Standard DS4 |1 disk (1 * 1 023 GB) |1 disk (1 * 1 023 GB) |15 diskar (15 * 1 023 GB) |

**Tabell 3**

Kapacitetsplanering för huvudmålservern beror på:

* Azure lagringsprestanda och begränsningar
  * Det maximala antalet hög utnyttjade diskar för en Standard-nivån VM är ungefär 40 (20 000/500 IOPS per disk) i ett enda lagringskonto. Läs mer om [skalbarhetsmål för standard lagringskonton](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) och [premiumlagringskonton](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
* Dagliga förändringstakten
* Kvarhållning volym lagring.

Tänk på följande:

* En källa kan sträcka sig över flera lagringskonton. Detta gäller för datadisk som går till storage-konton när du konfigurerar skydd. OS- och kvarhållning diskarna gå vanligtvis till automatiskt distribuerade lagringskontot.
* Kvarhållning lagringsvolym som krävs beror på den dagliga förändringstakten och antalet dagar för kvarhållning. Kvarhållning lagring behövs per huvudmålservern = totala omsättningen från källdatorn per dag * antal kvarhållning dagar.
* Varje huvudmålservern har endast en kvarhållningsvolymen. Kvarhållningsvolymen delas mellan diskar som är kopplade till huvudmålservern. Exempel:
  * Detta innebär att 240 IOPS per disk (2 åtgärder på måldisken per källa IO) om det finns en källdator med 5 diskar och varje disk genererar 120 IOPS (om 8K storlek) på källan. 240 IOPS ligger inom Azure per disk IOPS högst 500.
  * På kvarhållningsvolymen, detta blir 120 * 5 = 600 IOPS och det kan bli en bottle utlopp. I det här scenariot är en bra strategi att lägga till fler diskar i kvarhållningsvolymen och omfattar den över, som en stripe RAID-konfiguration. Detta förbättrar prestanda eftersom antalet IOPS som är fördelade på flera enheter. Antalet enheter som ska läggas till kvarhållningsvolymen ska vara följande:
    * Totalt antal IOPS från källmiljön / 500
    * Totala omsättningen per dag från källmiljön (okomprimerade) / 287 GB. 287 GB är maximalt dataflöde som stöds av en måldisken per dag. Mätvärdet varierar beroende på storleken skrivning med en faktor på 8 kB eftersom i det här fallet är 8 kB dig antas skrivåtgärder storlek. Till exempel om skrivning är 4K blir genomströmning 287/2. Och om skrivning storlek är 16 kB genomströmning kommer att vara 287 * 2.
* Numret för storage-konton som krävs = totala källa IOPs/10000.

## <a name="before-you-start"></a>Innan du börjar
| **Komponent** | **Krav** | **Detaljer** |
| --- | --- | --- |
| **Azure-konto** |Du behöver ett [Microsoft Azure](https://azure.microsoft.com/)-konto. Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/). | |
| **Azure Storage** |Du behöver ett Azure storage-konto för att lagra replikerade data<br/><br/> Antingen kontot ska vara en [Standard Geo-redundant Lagringskonto](../storage/common/storage-redundancy.md#geo-redundant-storage) eller [Premiumlagringskonto](../storage/common/storage-premium-storage.md).<br/><br/> Kontot måste finnas i samma region som Azure Site Recovery-tjänsten och vara associerat med samma prenumeration. Vi stöder inte flytt av lagringskonton som skapats med hjälp av den [nya Azure-portalen](../storage/common/storage-create-storage-account.md) över resursgrupper.<br/><br/> Mer information läser [introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md) | |
| **Virtuella Azure-nätverket** |Du behöver ett virtuellt Azure-nätverk där konfigurationsservern och huvudmålservern ska distribueras. Det bör vara i samma prenumeration och region som Azure Site Recovery-valvet. Om du vill replikera data över en ExpressRoute- eller VPN-anslutning måste virtuella Azure-nätverket vara ansluten till ditt lokala nätverk via en ExpressRoute-anslutning eller en plats-till-plats-VPN. | |
| **Azure-resurser** |Kontrollera att du har tillräckligt med Azure-resurser att distribuera alla komponenter. Läs mer i [Azure-prenumerationsbegränsningar](../azure-subscription-service-limits.md). | |
| **Azure Virtual Machines** |Virtuella datorer som du vill skydda måste uppfylla [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).<br/><br/> **Disken antal**– högst 31 diskar kan användas på en skyddad server<br/><br/> **Disken storlekar**– enskilda diskkapacitet får inte vara mer än 1 023 GB<br/><br/> **Klustring**– klustrade servrar stöds inte<br/><br/> **Start**– Extensible Firmware Interface UEFI (Unified) / Start Extensible Firmware via stöds inte<br/><br/> **Volymer**– Bitlocker-krypterade volymer inte stöds<br/><br/> **Servernamn**– namn måste innehålla mellan 1 och 63 tecken (bokstäver, siffror och bindestreck). Namnet måste börja med en bokstav eller siffra och sluta med en bokstav eller siffra. När en dator skyddas kan du ändra namnet på Azure. | |
| **Konfigurationsserver** |Standard A3 virtuell dator baserat på en bild för Azure Site Recovery Windows Server 2012 R2-galleriet kommer att skapas i din prenumeration för konfigurationsservern. Det skapas som den första instansen i en ny molntjänst. Om du väljer offentliga Internet som anslutningstypen för konfigurationsservern skapas Molntjänsten med reserverade offentliga IP-adress.<br/><br/> Installationssökvägen måste vara i engelska tecken. | |
| **Huvudmålservern** |Azure-dator, standard A4 D14 eller DS4.<br/><br/> Installationssökvägen måste vara i engelska tecken. Till exempel ska sökvägen vara **/usr/local/ASR** för en huvudmålserver som kör Linux. | |
| **Processervern** |Du kan distribuera processervern på fysisk eller virtuell dator som kör Windows Server 2012 R2 med de senaste uppdateringarna. Installera på C: /.<br/><br/> Vi rekommenderar att du placerar servern på samma nätverk och undernät som de datorer som du vill skydda.<br/><br/> Installera VMware vSphere CLI 5.5.0 på processervern. VMware vSphere CLI komponenten krävs på processervern för att identifiera virtuella datorer som hanteras av en vCenter-server eller virtuella datorer på ESXi-värd.<br/><br/> Installationssökvägen måste vara i engelska tecken.<br/><br/> ReFS-filsystemet stöds inte. | |
| **VMware** |En VMware vCenter-servern hanterar VMware vSphere-hypervisor-program. Den bör köras vCenter version 5.1 eller 5.5 med de senaste uppdateringarna.<br/><br/> En eller flera vSphere-hypervisor-program som innehåller virtuella VMware-datorer som du vill skydda. Hypervisor-programmet ska köras ESX/ESXi version 5.1 eller 5.5 med de senaste uppdateringarna.<br/><br/> Virtuella VMware-datorer ska ha VMware-verktyg installerat och körs. | |
| **Windows-datorer** |Skyddade fysiska servrar eller VMware-datorer som kör Windows har ett antal krav.<br/><br/> En 64-bitars operativsystem som stöds: **Windows Server 2012 R2**, **Windows Server 2012**, eller **Windows Server 2008 R2 med på minst SP1**.<br/><br/> Värdnamn, monteringspunkter, enhetsnamn, Windows systemsökväg (t.ex: C:\Windows) ska endast på engelska.<br/><br/> Operativsystemet ska installeras på enhet C:\.<br/><br/> Endast standarddiskar stöds. Dynamiska diskar stöds inte.<br/><br/> Brandväggsregler på skyddade datorer ska tillåta dem att nå konfiguration och master-målservrar i Azure.p ><p>Du måste ange ett administratörskonto (måste vara lokal administratör på Windows-dator) till push-installera Mobilitetstjänsten på Windows-servrar. Om det angivna kontot är ett domänkonto måste du inaktivera kontroll av fjärråtkomst till användare på den lokala datorn. Om du vill göra detta till registerposten LocalAccountTokenFilterPolicy DWORD med värdet 1 under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Att lägga till en registerpost från en öppen cmd för CLI eller powershell och ange  **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`** . [Lär dig mer](https://msdn.microsoft.com/library/aa826699.aspx) om åtkomstkontroll.<br/><br/> Efter växling vid fel, om du vill ansluta till virtuella datorer i Azure med fjärrskrivbord kontrollerar du att Windows har Fjärrskrivbord aktiverats för den lokala datorn. Om du inte ansluter via VPN, ska brandväggsregler tillåta anslutningar till fjärrskrivbord via internet. | |
| **Linux-datorer** |En stöds 64-bitars operativsystem: **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5 Red Hat kompatibel kernel eller Unbreakable Enterprise Kernel version 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> Brandväggsregler på skyddade datorer ska tillåta dem att nå konfigurations- och huvudmålservern servrar i Azure.<br/><br/> / etc/hosts-filer på skyddade datorer bör innehålla poster som matchar namnet på lokala värddatorn till IP-adresser som är associerade med alla nätverkskort <br/><br/> Om du vill ansluta till en Azure-dator som kör Linux efter en redundansväxling med hjälp av en Secure Shell-klient (ssh) se till att Secure Shell-tjänsten på den skyddade datorn är inställd att starta automatiskt vid systemstart och att brandväggsreglerna tillåter en ssh anslutning i den.<br/><br/> Värdnamn, monteringspunkter, enhetsnamn, och Linux-system sökvägar och filnamn (t ex/etc /; / usr) ska endast på engelska.<br/><br/> Skydd kan aktiveras för lokala datorer med följande lagring:-<br>Filsystem: EXT3, ETX4, ReiserFS, XFS<br>Multipath programvara enhet Mapper (MPIO)<br>Volymhanterare: LVM2<br>Fysiska servrar med HP CCISS domänkontrollant lagring stöds inte. | |
| **Från tredje part** |Vissa distributionskomponenter i det här fallet är beroende av tredje parts programvara ska fungera korrekt. En fullständig lista finns [tredje parts programvara meddelanden och information](#third-party) | |

### <a name="network-connectivity"></a>Nätverksanslutning
Du har två alternativ när du konfigurerar nätverksanslutningen mellan lokal plats och virtuella Azure-nätverket som infrastrukturkomponenter (konfigurationsservern, huvudmålservern servrar) har distribuerats. Du måste bestämma vilket alternativ för anslutningen ska använda innan du kan distribuera din konfigurationsservern. Du måste välja inställningen vid tidpunkten för distribution. Det går inte att ändra dem senare.

**Internet:** kommunikation och replikering av data mellan de lokala servrarna (processervern, skyddade datorer) och Azure komponenten infrastrukturservrar (konfigurationsservern, huvudmålserver) sker via en säker SSL/TLS anslutningen från lokal till offentliga slutpunkter på målservrarna konfiguration och master. (Det enda undantaget är anslutningen mellan processervern och huvudmålservern på TCP-port 9080 som är okrypterad. Endast kontrollinformation som rör protokollet replication för replikering utväxlas på den här anslutningen.)

![Distribution av diagram internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**: kommunikation och replikering av data mellan de lokala servrarna (processervern, skyddade datorer) och Azure komponenten infrastrukturservrar (konfigurationsservern, huvudmålserver) sker via en VPN-anslutning mellan ditt lokala nätverk och virtuella Azure-nätverket som konfigurationsservern och huvudmålservern-servrar distribueras. Se till att det lokala nätverket är ansluten till virtuella Azure-nätverket med en ExpressRoute-anslutning eller en plats-till-plats VPN-anslutning.

![Distribution av diagram VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)

## <a name="step-1-create-a-vault"></a>Steg 1: Skapa ett valv
1. Logga in på den [hanteringsportalen](https://portal.azure.com).
2. Expandera **datatjänster** > **återställningstjänster** och på **Site Recovery-valv**.
3. Klicka på **Skapa nytt** > **Snabbregistrering**.
4. I **Namn** anger du ett eget namn som identifierar valvet.
5. I **Region** väljer du ett geografiskt område för valvet. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Klicka på **Skapa valv**.

    ![Nytt valv](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Kontrollera att valvet har skapats genom att titta i statusfältet. Valvet visas som **Active** på primära **återställningstjänster** sidan.

## <a name="step-2-deploy-a-configuration-server"></a>Steg 2: Distribuera en konfigurationsserver
### <a name="configure-server-settings"></a>Konfigurera serverinställningar
1. På sidan **Recovery Services** klickar du på valvet för att öppna sidan Snabbstart. Du kan också öppna Snabbstart när du vill med hjälp av ikonen.

    ![Snabbstartsikon](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)
2. Välj i den nedrullningsbara listan **mellan en lokal plats med VMware/fysiska servrar och Azure**.
3. I **förbereda Target(Azure) resurser** klickar du på **distribuera konfigurationsservern**.

    ![Distribuera konfigurationsservern](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)
4. I **nya Server-konfigurationsinformation** ange:

   * Ett namn för konfigurationsservern och autentiseringsuppgifter för att ansluta till den.
   * Listrutan i nätverket anslutningstypen väljer **offentliga Internet** eller **VPN**. Observera att du inte kan ändra den här inställningen när den används.
   * Välj den Azure-nätverk som servern ska vara belägen. Om du använder VPN-Kontrollera att Azure nätverket är anslutet till det lokala nätverket som förväntat.
   * Ange intern IP-adress och nätmask som ska tilldelas till servern. Observera att de fyra första IP-adresserna i alla undernät är reserverade för intern användning i Azure. Använd en annan tillgänglig IP-adress.

     ![Distribuera konfigurationsservern](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)
5. När du klickar på **OK** en standard A3 virtuell dator baserat på en bild för Azure Site Recovery Windows Server 2012 R2-galleriet kommer att skapas i din prenumeration för konfigurationsservern. Det skapas som den första instansen i en ny molntjänst. Om du har valt för att ansluta via internet har Molntjänsten skapats med en reserverade offentliga IP-adress. Du kan övervaka förloppet på den **jobb** fliken.

    ![Övervaka förloppet](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)
6. Om du ansluter via internet när konfigurationsservern distribuerade Obs offentlig IP-adress tilldelas till den på den **virtuella datorer** sida i Azure-portalen. På den **slutpunkter** fliken Obs offentliga HTTPS porten mappade till privat port 443. Du behöver den här informationen senare när du registrerar huvudmålservern och servrar med konfigurationsservern. Konfigurationsservern distribueras med dessa slutpunkter:

   * HTTPS: En offentlig port används för att samordna kommunikationen mellan komponentservrarna och Azure via internet. Privat port 443 används för att samordna kommunikationen mellan komponentservrarna och Azure via VPN.
   * Anpassad: En offentlig port används för återställning efter fel för kommunikation via internet. Privat port 9443 används för återställning efter fel för kommunikation via VPN.
   * PowerShell: Privat port 5986
   * Fjärrskrivbord: privat port 3389

   ![VM-slutpunkter](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

   > [!WARNING]
   > Inte ta bort eller ändra offentligt eller privat portnumret för alla slutpunkter som skapades under distribution av konfigurationsserver.
   >
   >

Konfigurationsservern distribueras i en automatiskt skapad Azure-molntjänst med en reserverad IP-adress. Reserverad adress behövs för att kontrollera att konfigurationen serverns cloud service IP-adress är densamma mellan omstarter av de virtuella datorerna (inklusive konfigurationsservern) i Molntjänsten. Reserverade offentliga IP-adressen måste vara icke reserverade manuellt om konfigurationsservern är inaktiverat eller det kvar reserverade. Det finns en standard på högst 20 reserverade offentliga IP-adresser per prenumeration. [Lär dig mer](../virtual-network/virtual-networks-reserved-private-ip.md) om reserverade IP-adresser.

### <a name="register-the-configuration-server-in-the-vault"></a>Registrera konfigurationsservern i valvet
1. I den **Snabbstart** klickar **förbereda Målresurser** > **ladda ned en registreringsnyckel**. Nyckelfilen genereras automatiskt. Det är giltig i fem dagar efter det genereras. Kopiera den till konfigurationsservern.
2. I **virtuella datorer** Välj konfigurationsservern från listan över virtuella datorer. Öppna den **instrumentpanelen** och klicka på **Anslut**. **Öppna** hämtade RDP-filen för att logga in på konfigurationsservern med hjälp av fjärrskrivbord. Om du använder VPN kan du använda interna IP-adress (adressen du angav när du har distribuerat konfigurationsservern) för anslutning till fjärrskrivbord från den lokala platsen. Installationsguiden för Azure Site Recovery Configuration Server körs automatiskt när du loggar in för första gången.

    ![Registrering](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)
3. I **från tredje part Programvaruinstallation** klickar du på **jag accepterar** att hämta och installera MySQL.

    ![MySQL-installation](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)
4. I **MySQL serverinformation** skapa autentiseringsuppgifter för att logga in på MySQL-serverinstansen.

    ![MySQL-autentiseringsuppgifter](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)
5. I **Internetinställningar** ange hur konfigurationsservern ansluter till internet. Tänk på följande:

   * Om du vill använda en anpassad proxy konfigurerar du den innan du installerar providern.
   * När du klickar på **nästa** körs ett test för att kontrollera proxyanslutningen.
   * Om du använder en anpassad proxy eller din standardproxy kräver autentisering måste du ange proxyinformationen, inklusive adress, port och autentiseringsuppgifter.
   * Följande URL: er ska vara tillgänglig via proxy:
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *.store.core.windows.net
   * Om du har IP-adressbaserade brandväggsregler kontrollerar du att reglerna är inställda på att tillåta kommunikation från konfigurationsservern till IP-adresser som beskrivs i [IP-intervall för Azure-Datacenter](https://msdn.microsoft.com/library/azure/dn175718.aspx) och HTTPS (443)-protokollet. Du skulle behöva lista för tillåten IP-adressintervall i Azure-region som du planerar att använda och som västra USA.

     ![Proxyregistrering](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)
6. I **lokalisering providerinställningar fel meddelande** ange i vilket språk som du vill till felmeddelanden.

    ![Fel vid registrering för meddelandet](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)
7. I **Azure Site Recovery-registrering** Bläddra och välj nyckelfil som du kopierade till servern.

    ![Nyckelfilen registrering](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)
8. Välj alternativen på den sista sidan i guiden:

   * Välj **starta konto Management dialogrutan** att ange ska hantera konton dialogrutan Öppna när du har slutfört guiden.
   * Välj **skapa en skrivbordsikon för Cspsconfigtool** lägga till en genväg på skrivbordet på konfigurationsservern så att du kan öppna den **hantera konton** dialogrutan när som helst utan att behöva köra guiden igen.

     ![Slutföra registreringen](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)
9. Klicka på **Slutför** att slutföra guiden. En lösenfras genereras. Kopiera den till en säker plats. Du behöver det för att autentisera och registrera den processen och huvudmålservern servrar med konfigurationsservern. Den används också för att försäkra integritet kanal i configuration server-kommunikation. Du kan återskapa lösenfrasen men sedan måste du registrera huvudmålservern och bearbeta servrar med hjälp av den nya lösenfrasen.

    ![Lösenfrasen](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Efter registreringen konfigurationsservern visas på den **Konfigurationsservrar** i valvet.

### <a name="set-up-and-manage-accounts"></a>Konfigurera och hantera konton
Under distributionen av begär Site Recovery identifiering för följande åtgärder:

* En VMware-konto så thatSite återställning kan automatiskt identifiering av virtuella datorer på vCenter-servrar eller vSphere-värdar.
* När du lägger till datorer för skydd, så att Site Recovery kan installera mobilitetstjänsten på dem..

När du har registrerat konfigurationsservern kan du öppna den **hantera konton** för att lägga till och hantera konton som ska användas för dessa åtgärder. Det finns ett par olika sätt att göra detta:

* Öppna en genväg som du valde att skapa för dialogrutan på sista sidan i installationsprogrammet för konfigurationsservern (cspsconfigtool).
* Öppna dialogrutan på Slutför för konfiguration av servern.

1. I **hantera konton** klickar du på **Lägg till konto**. Du kan också ändra och ta bort befintliga konton.

    ![Hantera konton](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)
2. I **kontoinformation** ange ett kontonamn som ska användas i Azure och autentiseringsuppgifter (domän/användarnamn).

    ![Hantera konton](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>Ansluta till konfigurationsservern
Det finns två sätt att ansluta till konfigurationsservern:

* Via en VPN-plats-till-plats eller en ExpressRoute-anslutning
* Via internet

Tänk på följande:

* En internet-anslutning använder slutpunkter för den virtuella datorn tillsammans med offentliga virtuella IP-adressen för servern.
* En VPN-anslutning använder interna IP-adressen för servern tillsammans med slutpunkten privata portar.
* Det är ett enstaka beslut om att ansluta (kontroll och replikering) från din lokala servrar att olika komponentservrarna (konfigurationsservern, huvudmålserver) körs i Azure via en VPN-anslutning eller internet. Du kan inte ändra den här inställningen efteråt. Om du gör behöver du distribuera scenariot och skyddar dina datorer.  

## <a name="step-3-deploy-the-master-target-server"></a>Steg 3: Distribuera huvudmålservern
1. Klicka på **förbereda Target(Azure) resurser** > **distribuera huvudmålservern**.
2. Ange information om huvudmålservern och autentiseringsuppgifter. Servern kommer att distribueras i samma Azure-nätverk som konfigurationsservern. När du klickar på för att slutföra en virtuell Azure-dator kommer att skapas med en avbildning för Windows- eller Linux-galleriet.

    ![Mål-serverinställningar](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Observera att de fyra första IP-adresserna i alla undernät är reserverade för intern användning i Azure. Ange en annan tillgänglig IP-adress.

> [!NOTE]
> Välj Standard DS4 när du konfigurerar skydd för arbetsbelastningar som kräver konsekvent höga i/o-prestanda och låg latens för att vara värd för i/o-intensiv arbetsbelastning med [Premiumlagringskonto](../storage/common/storage-premium-storage.md).
>
>

1. En Windows huvudservern mål VM skapas med de här slutpunkterna. Observera att offentliga slutpunkter skapas endast om din ansluter via internet.

   * Anpassad: Offentliga porten används av processervern för att skicka replikeringsdata över internet. Privat port 9443 används av processervern för att skicka replikeringsdata till huvudmålservern via VPN.
   * Anpassad1: Offentliga porten används av processervern för att skicka metadata via internet. Privat port 9080 används av processervern för att skicka metadata till huvudmålservern via VPN.
   * PowerShell: Privat port 5986
   * Fjärrskrivbord: privat port 3389
2. En Linux huvudmålserver VM skapas med de här slutpunkterna. Observera att offentliga slutpunkter skapas bara om du ansluter via internet.

   * Anpassad: Offentliga porten används av processervern för att skicka replikeringsdata över internet. Privat port 9443 används av processervern för att skicka replikeringsdata till huvudmålservern via VPN.
   * Anpassad1: Offentliga porten används av processervern för att skicka metadata via internet. Privat port 9080 används av processervern för att skicka metadata till huvudmålservern via VPN
   * SSH: Privat port 22

     > [!WARNING]
     > Inte ta bort eller ändra offentligt eller privat portnumret för någon av de slutpunkter som skapades under distributionen av huvudmålservern-servern.
     >
     >
3. I **virtuella datorer** vänta tills den virtuella datorn ska starta.

   * Om det är en Windows server-anteckning ned remote desktop information.
   * Observera interna IP-adressen för den virtuella datorn om den är en Linux-server och du ansluter via VPN. Observera den offentliga IP-adressen om du ansluter via internet.
4. Logga in på servern för att slutföra installationen och registrera den med konfigurationsservern.
5. Om du kör Windows:

   1. Initiera en fjärrskrivbordsanslutning till den virtuella datorn. Första gången du loggar in ett skript kan köras i ett PowerShell-fönster. Stäng inte den. När den är klar öppnas automatiskt värden Agent Config-verktyget för att registrera servern.
   2. I **värden Agent Config** ange interna IP-adress för konfigurationsservern och port 443. Du kan använda den interna adressen och privat port 443, även om du inte ansluter via VPN eftersom den virtuella datorn är ansluten till samma Azure-nätverk som konfigurationsservern. Lämna **Använd HTTPS** aktiverat. Ange lösenfrasen för konfigurationsservern som du antecknade tidigare. Klicka på **OK** att registrera servern. Du kan ignorera NAT-alternativ. De används inte.
   3. Om din uppskattade kvarhållning enheten krävs mer än 1 TB kan du konfigurera kvarhållningsvolymen (R:) med hjälp av en virtuell disk och [lagringsutrymmen](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)

   ![Huvudmålservern för Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)
6. Om du kör Linux:

   1. Kontrollera att du har installerat den senaste Linux Integration Services (LIS) installerat innan du installerar huvudmålservern. Du hittar den senaste versionen av LIS tillsammans med anvisningar om hur du installerar [här](https://www.microsoft.com/download/details.aspx?id=46842). Starta om datorn när LIS installerat.
   2. I **förbereda (Azure) Målresurser** klickar du på **ladda ned och installera ytterligare programvara (endast för Linux Huvudmålserver)**. Kopiera den nedladdade tar-filen till den virtuella datorn med en sftp-klient. Du kan också logga in på huvudmålservern för distribuerade linux och använda *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* att ladda ned den filen.
   3. Logga in på servern med hjälp av en Secure Shell-klient. Om du är ansluten till Azure-nätverk via VPN kan du använda interna IP-adress. Annars använda externa IP-adressen och den offentliga SSH-slutpunkten.
   4. Extrahera filerna från gzippade installationsprogrammet genom att köra: **tar – xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
      ![huvudmålservern för Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
   5. Kontrollera att du är i katalogen där du extraherade innehållet i tar-filen.
   6. Kopiera konfigurationsserverns lösenfras till en lokal fil med hjälp av kommandot **echo  *`<passphrase>`*  > passphrase.txt**
   7. Kör kommandot ”**sudo. / install -t både - en värd -R MasterTarget -d /usr/local/ASR -i  *`<Configuration server internal IP address>`*  -p 443 -s y - c https -P passphrase.txt**”.

      ![Registrera målservern](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)
7. Vänta några minuter (10 – 15) och kontrollera att huvudmålservern visas som har registrerats i på sidan **servrar** > **Konfigurationsservrar** **-serverinformation** fliken. Om du kör Linux och kördes registrera inte värden konfigurationsverktyg igen från /usr/local/ASR/Vx/bin/hostconfigcli. Du måste ange åtkomstbehörigheter genom att köra chmod som rot.

    ![Verifiera målserver](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

> [!NOTE]
> Det kan ta upp till 15 minuter när registreringen är klar för huvudmålservern ska visas i portalen. Om du vill uppdatera omedelbart klickar du på **uppdatera** på den **Konfigurationsservrar** sidan.
>
>

## <a name="step-4-deploy-the-on-premises-process-server"></a>Steg 4: Distribuera processerver lokalt
Innan du börjar rekommenderar vi att du konfigurerar en statisk IP-adress på processervern så att den har garanterat beständig i omstarter.

1. Klicka på Snabbstart > **installera Processervern lokala** > **ladda ned och installera processervern**.

    ![Installera processervern](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)
2. Kopiera den hämta zip-filen till servern som du ska installera processervern. Zip-filen innehåller två installationsfiler:

   * Microsoft-ASR_CX_TP_8.4.0.0_Windows*
   * Microsoft-ASR_CX_8.4.0.0_Windows*
3. Packa upp arkivet och kopiera installationsfilerna till en plats på servern.
4. Kör den **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** installationen filen och följ instruktionerna. Detta installerar tredjeparts-komponenter som behövs för distributionen.
5. Kör sedan **Microsoft-ASR_CX_8.4.0.0_Windows***.
6. På den **serverläge** markerar **Processervern**.
7. På den **miljö information** gör du så här:

    - Om du vill skydda VMware virtuella datorer Klicka **Ja**
    - Om du bara vill skydda fysiska servrar och därför inte behöver VMware vCLI installerat på processervern. Klicka på **nr** och fortsätta.

1. Observera följande när du installerar VMware vCLI:

   * **Stöds bara VMware vSphere CLI 5.5.0**. Processervern fungerar inte med andra versioner eller uppdateringar av vSphere CLI.
   * Hämta vSphere CLI 5.5.0 från [här.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
   * Om du har installerat vSphere CLI innan du startade installerar processervern och det inte att hitta den vänta upp till fem minuter innan du kör installationsprogrammet igen. Detta säkerställer att alla miljövariabler behövs för identifiering av vSphere CLI har initierats på rätt sätt.
2. I **NIC val för Processervern** Välj det nätverkskort som ska använda för processervern.

   ![Välj adapter](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)
3. I **Server konfigurationsinformation**:

   * Ange intern IP-adress för konfigurationsservern och 443 för porten för IP-adressen och porten, om du ansluter via VPN. Ange annars offentliga virtuella IP-adressen och mappade offentlig HTTP-slutpunkt.
   * Skriv i lösenfras för konfigurationsservern.
   * Rensa **Kontrollera Mobility tjänsten software signatur** om du vill inaktivera verifiering när du använder automatisk push för att installera tjänsten. Signaturverifiering måste internet-anslutning från processervern.
   * Klicka på **Nästa**.

   ![Registrera konfigurationsservern](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)
4. I **Välj installationsenheten** välja en cacheenhet. Processervern måste en cacheenhet med minst 600 GB ledigt utrymme. Klicka på **Installera**.

   ![Registrera konfigurationsservern](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)
5. Observera att du kan behöva starta om servern för att slutföra installationen. I **konfigurationsservern** > **serverinformation** Kontrollera att processervern visas och har registrerats i valvet.

> [!NOTE]
> Det kan ta upp till 15 minuter när registreringen är klar för processervern ska visas som anges under konfigurationsservern. Om du vill uppdatera omedelbart uppdatera konfigurationsservern genom att klicka på uppdateringsknappen längst ned på konfigurationssidan
>
>

![Validera processervern](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Om du inte inaktiverar du signaturverifiering av mobilitetstjänsten när du registrerade processervern kan du göra det senare på följande sätt:

1. Logga in på processervern som administratör och öppna filen C:\pushinstallsvc\pushinstaller.conf för redigering. Under avsnittet **[PushInstaller.transport]** Lägg till följande rad: **SignatureVerificationChecks = ”0”**. Spara och stäng filen.
2. Starta om tjänsten InMage PushInstall.

## <a name="step-5-update-site-recovery-components"></a>Steg 5: Uppdatera Site Recovery-komponenter
Site Recovery-komponenterna uppdateras då. När nya uppdateringar är tillgängliga bör du installera dem i följande ordning:

1. Konfigurationsservern
2. Processervern
3. Huvudmålservern
4. Återställning av verktyget (vContinuum)

### <a name="obtain-and-install-the-updates"></a>Hämta och installera uppdateringar
1. Du kan hämta uppdateringar för configuration-, process- och huvudmålservern servrar från Site Recovery **instrumentpanelen**. Extrahera filerna från gzippade installationsprogrammet för Linux-installationen och kör kommandot ”sudo. / install” att installera uppdateringen.
2. [Hämta](http://go.microsoft.com/fwlink/?LinkID=533813) den senaste uppdateringen för återställning efter fel tool(vContinuum).
3. Om du använder virtuella datorer eller fysiska servrar som redan har installerat mobilitetstjänsten, kan du hämta uppdateringar för tjänsten enligt följande:

   * **Alternativ 1**: ladda ned uppdateringar:
     * [Windows Server (endast 64-bitars)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
     * [CentOS 6.4,6.5,6.6 (endast 64-bitars)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
     * [Oracle Enterprise Linux 6.4,6.5 (endast 64-bitars)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
     * [SUSE Linux Enterprise Server SP3 (endast 64-bitars)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
     * När du har uppdaterat processervern blir den uppdaterade versionen av mobilitetstjänsten tillgängligt i mappen C:\pushinstallsvc\repository på processervern.
   * **Alternativ 2**: Om du har en dator med en äldre version av mobilitetstjänsten installeras, kan du automatiskt uppgradera mobilitetstjänsten på datorn från hanteringsportalen.

     1. Kontrollera att processervern uppdateras.
     2. Kontrollera att den skyddade datorn uppfyller de [krav](#install-the-mobility-service-automatically) för att automatiskt skicka mobilitetstjänsten, så att uppdateringen fungerar som förväntat.
     3. Välj skyddsgruppen, markera den skyddade datorn och klicka på **uppdatering mobilitetstjänsten**. Den här knappen är endast tillgänglig om det finns en nyare version av mobilitetstjänsten.

         ![Välj vCenter-servern](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

Ange administratörskontot som används för att uppdatera mobilitetstjänsten på den skyddade servern i Select-konton. Klicka på OK och vänta utlösta jobbet ska slutföras.

## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>Steg 6: Lägga till vCenter-servrar eller vSphere-värdar
1. Klicka på **servrar** > **Konfigurationsservrar** > konfigurationsservern >**lägga till vCenter-servern** att lägga till en vCenter server eller vSphere-värd.

    ![Välj vCenter-servern](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)
2. Ange information för servern eller värden och välj som används för att identifiera den.

   * Ange portnumret som vCenter-servern körs om vCenter-servern inte körs på standardporten 443.
   * Processervern måste finnas på samma nätverk som vCenter server/vSphere-värd och ska ha VMware vSphere CLI 5.5.0 installerad.

     ![inställningar för vCenter-server](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)
3. När identifieringen har slutförts visas vCenter-servern under konfigurationsinformation för servern.

    ![inställningar för vCenter-server](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)
4. Om du använder ett administratörskonto för att lägga till servern eller värden Kontrollera att kontot har behörighet för följande:

   * vCenter-konton ska ha Datacenter, datalagret, mapp, värden, nätverk, resurs, lagring vyer, virtuell dator och vSphere distribuerade växeln behörigheterna.
   * vSphere-värd konton bör ha Datacenter, datalagret, mapp, värden, nätverk, resurs, virtuell dator och vSphere distribuerade växeln behörigheterna

## <a name="step-7-create-a-protection-group"></a>Steg 7: Skapa en skyddsgrupp
1. Öppna **skyddade objekt** > **Skyddsgrupp** > **skapa skyddsgrupp**.

    ![Skapa en skyddsgrupp](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)
2. På den **ange inställningarna för Skyddsgruppen** sidan Ange ett namn för gruppen och välj den configuration-server som du vill skapa gruppen.

    ![Inställningarna för skyddsgruppen](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)
3. På den **ange replikeringsinställningarna** konfigurera replikeringsinställningarna som ska användas för alla datorer i gruppen.

    ![Skydd grupp replikering](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)
4. Inställningar för:

   * **Multi VM konsekvenskontroll**: Om du aktiverar detta när delade programkonsekventa återställningspunkter skapas på alla datorer i skyddsgruppen. Den här inställningen är mycket användbar när alla datorer i skyddsgruppen kör samma arbetsbelastning. Alla datorer återställs till samma datapunkt. Endast tillgängligt för Windows-servrar.
   * **Tröskelvärde för Återställningspunktmål**: aviseringar genereras när den kontinuerlig dataskyddsreplikering överskrider det konfigurerade tröskelvärdet.
   * **Kvarhållningstid för återställningspunkten**: Anger kvarhållningsperiod. Skyddade datorer kan återställas till valfri punkt inom det här fönstret.
   * **Programkonsekventa ögonblicksbilder frekvens**: Anger hur ofta återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas.

Du kan övervaka skyddsgruppen som de skapas på den **skyddade objekt** sidan.

## <a name="step-8-set-up-machines-you-want-to-protect"></a>Steg 8: Konfigurera datorer som du vill skydda
Du behöver installera mobilitetstjänsten på virtuella datorer och fysiska servrar som du vill skydda. Du kan göra detta på två sätt:

* Automatisk push- och installation av tjänsten på varje dator från processervern.
* Installera tjänsten manuellt.

### <a name="install-the-mobility-service-automatically"></a>Installera mobilitetstjänsten automatiskt
När du lägger till datorer i en skyddsgrupp pushas mobilitetstjänsten automatiskt och installeras på varje dator med processervern.

**Automatiskt installera push mobilitetstjänsten på Windows-servrar:**

1. Installera de senaste uppdateringarna för processervern enligt beskrivningen i [steg 5: installera senaste uppdateringarna](#step-5-install-latest-updates), och se till att processervern är tillgänglig.
2. Se till att det finns en nätverksanslutning mellan källdatorn och processervern och att källdatorn kan nås från processervern.  
3. Konfigurera Windows-brandväggen att tillåta **File and Printer Sharing** och **Windows Management Instrumentation**. Under Windows-brandväggens inställningar väljer du alternativet ”Tillåt en app eller funktion via brandväggen” och Välj program som visas i bilden nedan. Du kan konfigurera brandväggsprincipen för datorer som tillhör en domän med ett grupprincipobjekt.

    ![Brandväggsinställningar](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)
4. Det konto som används för att utföra push-installation måste vara i gruppen Administratörer på den dator du vill skydda. Dessa autentiseringsuppgifter används bara för push-installation av mobilitetstjänsten och du måste ange dem när du lägger till en dator i en skyddsgrupp.
5. Om kontot som angetts är inte ett domänkonto måste du inaktivera kontroll av fjärråtkomst till användare på den lokala datorn. Om du vill göra detta till registerposten LocalAccountTokenFilterPolicy DWORD med värdet 1 under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Att lägga till en registerpost från en öppen cmd för CLI eller powershell och ange  **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`** .

**Automatiskt installera push mobilitetstjänsten på Linux-servrar:**

1. Installera de senaste uppdateringarna för processervern enligt beskrivningen i [steg 5: installera senaste uppdateringarna](#step-5-install-latest-updates), och se till att processervern är tillgänglig.
2. Se till att det finns en nätverksanslutning mellan källdatorn och processervern och att källdatorn kan nås från processervern.  
3. Kontrollera att kontot är en rotanvändare på källservern för Linux.
4. Kontrollera att filen/etc/hosts på källservern för Linux innehåller poster som matchar namnet på lokala värddatorn till IP-adresser som är associerade med alla nätverkskort.
5. Installera den senaste openssh, openssh-server, openssl paket på datorn du vill skydda.
6. Kontrollera att SSH är aktiverat och körs på port 22.
7. Aktivera SFTP-undersystemet och lösenordsautentisering i sshd_config-filen på följande sätt:

   * en) logga in som rot.
   * b) hitta raden som börjar med i filen filen /etc/ssh/sshd_config **PasswordAuthentication**.
   * c) Avkommentera raden och ändra värdet från ”Nej” till ”Ja”.

       ![Linux-mobility](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)
   * d) hitta den rad som börjar med delsystemet och Avkommentera raden.

       ![Linux push mobility](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    
8. Kontrollera att källan datorn Linux variant stöds.

### <a name="install-the-mobility-service-manually"></a>Installera mobilitetstjänsten manuellt
Programvarupaket som används för att installera mobilitetstjänsten finns på processervern i C:\pushinstallsvc\repository. Logga in på processervern och kopiera lämpliga installationspaket till källdatorn enligt tabellen nedan:-

| Källoperativsystem | Mobility servicepaket på processervern |
| --- | --- |
| Windows Server (endast 64-bitars) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4, 6.5, 6.6 (endast 64 bitars) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3 (endast 64 bitars) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz` |
| Oracle Enterprise Linux 6.4, 6.5 (endast 64 bitars) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |

**Installera mobilitetstjänsten manuellt på en Windows server**, gör du följande:

1. Kopiera den **Microsoft ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** paket från processen serversökvägen som anges i tabellen ovan till källdatorn.
2. Installera mobilitetstjänsten genom att köra den körbara filen på källdatorn.
3. Följ instruktionerna för installer.
4. Välj **mobilitetstjänsten** som rollen och klicka på **nästa**.

    ![Installera mobilitetstjänsten](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)
5. Lämna installationskatalogen som standardsökvägen för installationen och klicka på **installera**.
6. I **värden Agent Config** ange IP-adress och HTTPS-port konfigurationsservern.

   * Om du ansluter via internet ange offentliga virtuella IP-adressen och offentlig HTTPS-slutpunkt som port.
   * Ange den interna IP-adressen och 443 för porten om du ansluter via VPN. Lämna **Använd HTTPS** markerad.

     ![Installera mobilitetstjänsten](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)
7. Ange konfigurationsserverns lösenfras och klicka på **OK** att registrera mobilitetstjänsten med konfigurationsservern.

**Köra från kommandoraden:**

1. Kopiera lösenfrasen från CX till filen ”C:\connection.passphrase” på servern och kör det här kommandot. I vårt exempel CX i 104.40.75.37 och HTTPS-porten är 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Installera mobilitetstjänsten manuellt på en Linux-server**:

1. Kopiera lämplig tar arkivet baserat på tabellen ovan, från processervern till källdatorn.
2. Öppna ett shell-program och extrahera komprimerade tar-arkivet till en lokal sökväg genom att köra`tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Skapa en passphrase.txt-fil i den lokala katalogen där du extraherade innehållet i arkivet tar-filen genom att ange  *`echo <passphrase> >passphrase.txt`*  från shell.
4. Installera mobilitetstjänsten genom att ange  *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`* .
5. Ange IP-adress och port:

   * Om du ansluter till konfigurationsservern via internet anger konfiguration serverns virtuella offentliga IP-adress och offentlig HTTPS-slutpunkt i `<IP address>` och `<port>`.
   * Ange den interna IP-adressen och 443 om du ansluter via en VPN-anslutning.

**För körning från kommandoraden**:

1. Kopiera lösenfrasen från CX till filen ”passphrase.txt” på servern och kör det här kommandot. I vårt exempel CX i 104.40.75.37 och HTTPS-porten är 62519:

Installera på en produktionsserver:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

Installera på målservern:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

> [!NOTE]
> När du lägger till datorer i en skyddsgrupp som redan kör en lämplig version av mobilitetstjänsten sedan push-installation har hoppats över.
>
>

## <a name="step-9-enable-protection"></a>Steg 9: Aktivera skydd
Om du vill aktivera skydd av du lägga till virtuella datorer och fysiska servrar i en skyddsgrupp. Observera att innan du börjar:

* Virtuella datorer identifieras var 15: e minut och det kan ta upp till 15 minuter att visas i Azure Site Recovery efter identifiering.
* Ändringar i miljön på den virtuella datorn (till exempel VMware verktyg installation) kan också ta upp till 15 minuter att uppdateras i Site Recovery.
* Du kan kontrollera senast identifierade den **senaste kontakt på** för vCenter server/ESXi-värd på den **Konfigurationsservrar** sidan.
* Om du har en skyddsgrupp som redan har skapats och lägga till en vCenter-servern eller ESXi-värd som det tar femton minuter för Azure Site Recovery-portalen för att uppdatera och för virtuella datorer som ska visas i den **Lägg till datorer i en skyddsgrupp** dialogrutan.
* Markera om du vill gå vidare direkt med att lägga till datorer i skyddsgruppen utan att vänta på den schemalagda identifieringen konfigurationsservern (inte på den) och klicka på den **uppdatera** knappen.
* När du lägger till virtuella datorer eller fysiska datorer i en skyddsgrupp processervern push-meddelanden och automatiskt installerar mobilitetstjänsten på källservern om it inte är installerad.
* Kontrollera att du har konfigurerat dina skyddade datorer enligt beskrivningen i föregående steg för mekanismen för automatisk distribution ska fungera.

Lägg till datorer på följande sätt:

1. Klicka på **skyddade objekt** > **Skyddsgrupp** > **datorer** > **Lägg till datorer** . Som bästa praxis rekommenderar vi att skyddsgrupper bör spegla dina arbetsbelastningar så att du lägger till datorer som kör ett visst program till samma grupp.
2. I **Välj virtuella datorer** om du skyddar fysiska servrar i den **lägga till fysiska datorer** guiden ange IP-adress och eget namn. Välj sedan operativsystemsfamiljen.

    ![Lägg till V Center-servern](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)
3. I **Välj virtuella datorer** om du skyddar virtuella VMware-datorer, Välj en vCenter-server som hanterar dina virtuella datorer (eller EXSi värden som de använder) och välj sedan datorerna.

    ![Lägg till V Center-servern](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png)    
4. I **ange Målresurser** Välj huvudmålservern servrar och lagring ska användas för replikering och välj om inställningarna som ska användas för alla arbetsbelastningar. Välj [Premiumlagringskonto](../storage/common/storage-premium-storage.md) vid konfiguration av skydd för arbetsbelastningar som kräver konsekvent höga i/o-prestanda och låg latens för att vara värd för i/o-intensiv arbetsbelastning. Om du vill använda ett premiumlagringskonto-lagring för din arbetsbelastningsdiskar som du behöver använda Huvudmålet för DS-serien. Du kan inte använda Premium-lagring diskar med Huvudmålet för DS serier.

   > [!NOTE]
   > Vi stöder inte flytt av lagringskonton som skapats med hjälp av den [nya Azure-portalen](../storage/common/storage-create-storage-account.md) över resursgrupper.
   >
   >

    ![vCenter-servern](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)
5. I **ange konton** väljer du det konto som du vill använda för att installera mobilitetstjänsten på skyddade datorer. Autentiseringsuppgifterna för kontot krävs för automatisk installation av mobilitetstjänsten. Om du inte kan välja ett konto kontrollerar du att konfigurerar du enligt beskrivningen i steg 2. Observera att det här kontot inte kan nås av Azure. Windows har server kontot administratörsbehörighet på källservern. Kontot måste vara roten för Linux.

    ![Linux-autentiseringsuppgifter](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)
6. Klicka på kryssmarkeringen att du har lagt till alla datorer i skyddsgruppen och starta inledande replikering för varje dator. Du kan övervaka statusen i den **jobb** sidan.

    ![Lägg till V Center-servern](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)
7. Dessutom kan du övervaka skyddsstatus genom att klicka på **skyddade objekt** > skyddsgruppnamn > **virtuella datorer** . När den inledande replikeringen är klar och datorerna som synkroniserar data visas de **skyddade** status.

    ![Virtual machine-jobb](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)

### <a name="set-protected-machine-properties"></a>Ange skyddad dator egenskaper
1. När en dator har en **skyddade** status som du kan konfigurera egenskaperna för växling vid fel. Skyddet gruppinformation Markera datorn och öppna den **konfigurera** fliken.
2. Du kan ändra det namn som ges till dator i Azure efter växling vid fel och storlek för virtuell dator i Azure. Du kan också välja Azure-nätverk som datorn ska anslutas efter växling vid fel.

   > [!NOTE]
   > [Migrering av nätverk](../resource-group-move-resources.md) mellan resursgrupper i samma prenumeration eller mellan prenumerationer stöds inte för nätverk som används för att distribuera Site Recovery.
   >
   >

    ![Ange egenskaper för virtuell dator](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Tänk på följande:

* Namnet på datorn som Azure måste uppfylla kraven för Azure.
* Som standard inte är den replikerade virtuella datorer i Azure ansluten till ett Azure-nätverk. Om du vill att replikerade Se virtuella datorer kommunicera till att ange samma Azure-nätverk för dessa.
* Om du ändrar storlek på en volym på en virtuell VMware-dator eller fysisk server som den försätts i ett kritiskt tillstånd. Om du behöver ändra storlek på gör du följande:

  * en) ändra inställningen.
  * b) i den **virtuella datorer** väljer du den virtuella datorn och klicka på **ta bort**.
  * c) i **ta bort virtuell dator** väljer alternativet **inaktivera skydd (Använd för återställningsgranskning och volymstorlek)**. Det här alternativet inaktiverar skydd, men behåller återställningspunkter i Azure.

      ![Ange egenskaper för virtuell dator](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)
  * d) återaktivera skydd för den virtuella datorn. När du återaktivera skydd överförs data för den nya storleken volymen till Azure.

## <a name="step-10-run-a-failover"></a>Steg 10: Kör en växling vid fel
Du kan för närvarande bara köra oplanerad växling vid fel för skyddade virtuella VMware-datorer och fysiska servrar. Observera följande:

* Innan du startar en växling vid fel, se till att konfigurations- och master målservrarna körs och är felfria. Annars misslyckas växlingen.
* Källdatorer stänga inte av som en del av en oplanerad redundans. Utföra en oplanerad redundansväxling stoppas datareplikeringen för skyddade servrar. Du måste ta bort datorerna från skyddsgruppen och lägga till dem igen för att kunna börja skydda datorer igen efter en oplanerad redundansväxling har slutförts.
* Om du vill växla över utan att förlora data, se till att är de primära platsen virtuella datorerna avstängda innan du startar växling vid fel.

1. På den **Återställningsplaner** och lägger till en återställningsplan. Ange information om plan och välj **Azure** som mål.

    ![Konfigurera återställningsplan](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)
2. I **Välj virtuell dator** väljer du en skyddsgrupp och välj sedan datorer i gruppen för att lägga till i återställningsplanen. [Läs mer](site-recovery-create-recovery-plans.md) om återställningsplaner.

    ![Lägg till virtuella datorer](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)
3. Om det behövs kan du anpassa planen för att skapa grupper och sekvens ordningen i vilken datorer i återställningen plan har redundansväxlats. Du kan också lägga till efterfrågas manuella åtgärder och skript. Skript när du återställer till Azure kan läggas till med hjälp av [Azure Automation-Runbooks](site-recovery-runbook-automation.md).
4. I den **Återställningsplaner** väljer du planen och klickar på **oplanerad växling**.
5. I **bekräfta redundans** Kontrollera redundansriktning (för Azure) och Välj återställningspunkt för att växla över till.
6. Vänta tills beställningsjobbet slutförts och kontrollera att redundans fungerar som förväntat och att de replikerade virtuella datorerna startas i Azure.

## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>Steg 11: Växla tillbaka misslyckades över datorer från Azure
[Lär dig mer](site-recovery-failback-azure-to-vmware-classic-legacy.md) om hur du hanterar din misslyckade över datorer som körs i Azure tillbaka till din lokala miljö.

## <a name="manage-your-process-servers"></a>Hantera dina servrar
Processervern skickar data för replikering till huvudmålservern i Azure och identifierar nya virtuella VMware-datorer till en vCenter-server. I följande fall kanske du vill ändra processerver i distributionen:

* Om den aktuella processervern stängs av
* Om ditt mål för återställningspunkt (RPO) stiger till en acceptabel nivå för din organisation.

Om det behövs kan du flytta replikeringen av vissa eller alla lokala virtuella VMware-datorer och fysiska servrar till en annan processerver. Exempel:

* **Fel**– om en processerver misslyckas eller inte är tillgänglig kan du flytta replikeringen av den skyddade datorn till en annan processerver. Metadata för källdatorn och replikerade datorn kommer att flyttas till den nya processervern och data synkroniseras. Den nya processervern ansluter automatiskt till vCenter-servern för att utföra automatisk identifiering. Du kan övervaka statusen för servrar på Site Recovery-instrumentpanelen.
* **Belastningsutjämning för att justera Återställningspunktmål**– för förbättrad belastningsutjämning du kan välja en annan process-server i Site Recovery-portalen och flyttar du replikeringen av en eller flera datorer i den för manuell belastningsutjämning. I det här fallet flyttas metadata för de valda datorerna käll- och till den nya processervern. Ursprungliga processervern fortfarande är ansluten till vCenter-servern.

### <a name="monitor-the-process-server"></a>Övervaka processervern
Om en processervern är i ett kritiskt tillstånd visas en varning om status på instrumentpanelen för Site Recovery. Du kan klicka på status för att öppna fliken händelser och sedan öka detaljnivån till specifika jobb på fliken jobb.

### <a name="modify-the-process-server-used-for-replication"></a>Ändra processervern som används för replikering
1. Öppna **servrar** > **Konfigurationsservrar** > konfigurationsservern > **serverinformation**.
2. Klicka på **servrar** > **ändra Processerver** bredvid den server du vill ändra.

    ![Ändra Processerver 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)
3. I **ändra Processerver** > **Målprocesserver** markerar du den nya servern som du vill använda och välj sedan de virtuella datorerna som du vill replikera till den nya servern. Klicka på informationsikonen bredvid namnet på servern för information om ledigt och använt minne. Det genomsnittliga utrymmet som krävs för att replikera alla valda virtuella datorn till den nya processervern visas som hjälper dig att läsa in beslut.

    ![Ändra Processerver 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)
4. Klicka på kryssmarkeringen för att börja replikering till den nya processervern. Observera att om du tar bort alla virtuella datorer från en processerver som var kritiska bör inte längre visas en kritisk varning i instrumentpanelen.

## <a name="third-party-software-notices-and-information"></a>Meddelanden om programvara från tredje part och information
Inte översätta eller lokalisera

Programvara och inbyggd programvara som körs i Microsoft-produkt eller tjänst är baserad på eller innehåller material från projekten i listan nedan (gemensamt kallade ”kod från tredje part”).  Microsoft är inte ursprungliga författaren av koden från tredje part.  Ursprungliga upphovsrättsmeddelandet och licensen, enligt vilka Microsoft fått sådan kod från tredje part, är anges nedan.

Informationen i avsnittet A gäller kod från tredje part-komponenter från projekten i listan nedan. Sådana licenser och information tillhandahålls endast i informationssyfte.  Den här koden från tredje part är att relicensed till dig av Microsoft under Microsofts programvara licensvillkoren för Microsoft-produkt eller tjänst.  

Informationen i avsnittet B gäller kod från tredje part-komponenter som görs tillgängliga för dig av Microsoft under de ursprungliga licensvillkoren.

Fullständig filen kan hittas på den [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft förbehåller sig alla rättigheter som inte uttryckligen beviljas, vare sig indirekt, via estoppel eller på annat sätt.
