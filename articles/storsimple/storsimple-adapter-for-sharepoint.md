---
title: Installera StorSimple Adapter för SharePoint | Microsoft Docs
description: Beskriver hur du installerar och konfigurera eller ta bort StorSimple Adapter för SharePoint i en SharePoint-servergrupp.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: v-sharos
ms.openlocfilehash: a2f8e75578e396085e7d80f43c1180e158967061
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885595"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Installera och konfigurera StorSimple Adapter för SharePoint
## <a name="overview"></a>Översikt
StorSimple Adapter för SharePoint är en komponent som kan du tillhandahålla flexibla Microsoft Azure StorSimple-lagring och dataskydd för SharePoint-servergrupper. Du kan använda kortet för att flytta stora objekt BLOB innehåll (Binary) från SQL Server-innehållsdatabaser till Microsoft Azure StorSimple hybrid cloud-lagringsenhet.

StorSimple Adapter för SharePoint fungerar som en fjärransluten BLOB Storage (RBS)-provider och använder funktionen SQL Server Remote BLOB Storage för att lagra Ostrukturerade SharePoint-innehåll (i form av Blobbar) på en filserver som backas upp av en StorSimple-enhet.

> [!NOTE]
> StorSimple Adapter för SharePoint har stöd för SharePoint Server 2010 Remote BLOB Storage (RBS). Det har inte stöd för SharePoint Server 2010 externa BLOB Storage (EBS).


* Om du vill ladda ned StorSimple Adapter för SharePoint går du till [StorSimple Adapter för SharePoint] [ 1] i Microsoft Download Center.
* Information om planering för RBS och RBS begränsningar, går du till [bestämmer dig att använda RBS i SharePoint 2013] [ 2] eller [planera för RBS (SharePoint Server 2010)] [ 3].

Resten av den här översikten beskrivs kortfattat rollen för StorSimple Adapter för SharePoint och SharePoint-kapacitet och prestanda gränser som du bör känna till innan du installerar och konfigurerar kortet. När du har läst den här informationen går du till [StorSimple Adapter för SharePoint-installationen](#storsimple-adapter-for-sharepoint-installation) att börja konfigurera kortet.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple-Adapter för SharePoint-fördelar
Innehållet lagras i en SharePoint-webbplats som Ostrukturerade BLOB-data i en eller flera innehållsdatabaser. Som standard finns de här databaserna på datorer som kör SQL Server och finns i SharePoint-servergruppen. Blobar kan snabbt öka i storlek, förbrukar stora mängder lagring på plats. Därför kanske du vill hitta en annan, mindre dyrt lagringslösning. SQL Server innehåller en teknik som kallas Remote Blob Storage (RBS) som gör att du lagrar BLOB-innehåll i filsystemet utanför SQL Server-databasen. BLOB-objekt kan finnas i filsystemet på den dator som kör SQL Server med RBS, eller de kan lagras i filsystemet på en annan server-datorn.

RBS kräver att du använder en RBS provider, till exempel StorSimple Adapter för SharePoint, för att aktivera RBS i SharePoint. StorSimple Adapter för SharePoint fungerar med RBS, så att du kan flytta BLOB-objekt till en server som backas upp av Microsoft Azure StorSimple-systemet. Microsoft Azure StorSimple sedan lagrar BLOB-data lokalt eller i molnet, baserat på användning. Blobbar som är mest aktiva (kallas vanligtvis nivå 1 eller frekvent lagringsnivå) finnas lokalt. Mindre aktiva data och arkivdata finns i molnet. När du har aktiverat RBS på en innehållsdatabas lagras alla nya blobbinnehåll som skapats i SharePoint på StorSimple-enheten och inte i innehållsdatabasen.

Microsoft Azure StorSimple-implementeringen av RBS ger följande fördelar:

* Genom att flytta BLOB-innehåll till en separat server, kan du minska frågebelastningen på SQL Server, vilket kan förbättra svarstiden för SQL Server. 
* Azure StorSimple använder avduplicering och komprimering för att minska datastorleken.
* Azure StorSimple ger dataskydd i form av lokala och ögonblicksbilder av molnet. Även om du placerar själva databasen på StorSimple-enheten, kan du säkerhetskopiera innehållsdatabasen och Blobar tillsammans i ett kraschkonsekvent sätt. (Flytta innehållsdatabasen till enheten har endast stöd för enhet för StorSimple 8000-serien. Den här funktionen stöds inte för 5000 och 7000-serien.)
* Azure StorSimple innehåller funktioner för katastrofåterställning, inklusive redundans, fil- och återställning (inklusive testning recovery) och snabb återställning av data.
* Du kan använda data recovery programvara, till exempel Kroll Ontrack PowerControls med StorSimple ögonblicksbilder av BLOB-data att utföra återställning på objektnivå av SharePoint-innehåll. (Den här programvaran för återställning av data är en separat köp.)
* StorSimple Adapter för SharePoint som ansluts till Central Administration av SharePoint-portalen så att du kan hantera hela SharePoint-lösningen från en central plats.

Flytta BLOB-innehåll till filsystemet kan ge andra kostnadsbesparingar och fördelar. Till exempel med hjälp av RBS kan minska behovet av dyra nivå 1-lagring och eftersom den krymper innehållsdatabasen RBS kan minska antalet databaser som krävs i SharePoint-servergruppen. Dock kan andra faktorer, till exempel storleksbegränsningar för databasen och mängden icke RBS innehåll också påverka lagringskrav. Läs mer om kostnaderna och fördelarna med att använda RBS [planera för RBS (SharePoint Foundation 2010)] [ 4] och [bestämmer dig att använda RBS i SharePoint 2013] [5].

### <a name="capacity-and-performance-limits"></a>Begränsningar för kapacitet och prestanda
Innan du överväga att använda RBS i din SharePoint-lösning bör du vara medveten om testade prestanda och kapacitetsbegränsningarna för SharePoint Server 2010 och SharePoint Server 2013 och hur dessa gränser är relaterade till acceptabel prestanda. Mer information finns i [programvara gränser och begränsningar för SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Granska följande innan du konfigurerar RBS:

* Se till att den totala storleken på innehållet (storlek för en innehållsdatabas) plus storleken på alla associerade externalized Blobbar inte överskrider storleksgränsen RBS stöds av SharePoint. Den här gränsen är 200 GB. 
  
    **Måttet innehållsdatabasen och BLOB-storlek**
  
  1. Kör den här frågan på Central Administration WFE. Starta hanteringsgränssnittet för SharePoint och ange sedan följande Windows PowerShell-kommando för att hämta storleken på innehållsdatabaserna:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Det här steget hämtar storleken på innehållsdatabasen på disken.
  2. Kör något av följande SQL-frågor i SQL Management Studio rutan SQL server på varje innehållsdatabas och lägga till resultatet till antalet hämtades i steg 1.
     
     På innehållsdatabaser för SharePoint 2013, anger du:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     På innehållsdatabaser för SharePoint 2010, anger du:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Det här steget hämtar storleken på de Blobar som har varit externalized.
* Vi rekommenderar att du lagrar allt innehåll för BLOB- och -databas lokalt på StorSimple-enheten. StorSimple-enheten är ett tvånods-kluster för hög tillgänglighet. Placera innehållsdatabaser och BLOB-objekt på StorSimple-enhet ger hög tillgänglighet.
  
    Använda traditionella SQLServer migration bästa praxis för att flytta innehållsdatabasen till StorSimple-enheten. Flytta databasen förrän alla BLOB-innehåll från databasen har flyttats till filresursen via RBS. Om du väljer att flytta innehållsdatabasen till StorSimple-enheten, rekommenderar vi att du konfigurerar lagringen innehållsdatabas på enheten som en primär volym.
* Det finns inget sätt att garantera att innehållet som lagras lokalt på StorSimple enheten inte kommer att vara nivåindelad till Microsoft Azure-molnlagring i Microsoft Azure StorSimple, om du använder nivåindelade volymer. Därför bör du använda StorSimple lokalt fixerade volymer tillsammans med SharePoint RBS. Detta säkerställer att alla BLOB-innehållet kvar lokalt på StorSimple-enheten och flyttas inte till Microsoft Azure.
* Om du inte sparar innehållsdatabaser på StorSimple-enheten, Använd traditionella SQL Server hög tillgänglighet säkerhetsmetoder som har stöd för RBS. SQL Server-kluster stöder RBS, när SQL Server spegling inte. 

> [!WARNING]
> Om du inte har aktiverat RBS rekommenderar vi inte flytta innehållsdatabasen till StorSimple-enheten. Det här är en otestade konfiguration.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple-Adapter för SharePoint-installationen
Innan du kan installera StorSimple Adapter för SharePoint, måste du konfigurera StorSimple-enheten och kontrollera att SharePoint-servergrupp och SQL Server-instansiering uppfyller alla krav. Den här självstudien beskrivs konfigurationskrav, samt procedurer för att installera och uppgradera StorSimple Adapter för SharePoint.

## <a name="configure-prerequisites"></a>Konfigurera krav
Innan du kan installera StorSimple Adapter för SharePoint måste du kontrollera att StorSimple-enheten, SharePoint-servergrupp och SQL Server-instansiering uppfyller följande krav.

### <a name="system-requirements"></a>Systemkrav
StorSimple Adapter för SharePoint fungerar med följande maskinvara och programvara:

* Operativsystem som stöds – Windows Server 2008 R2 SP1, Windows Server 2012 eller Windows Server 2012 R2
* SharePoint versioner som stöds – SharePoint Server 2010 eller SharePoint Server 2013
* SQL Server-versioner stöds – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition eller SQL Server 2012 Enterprise Edition
* Stöd för StorSimple-enheter – StorSimple 8000-serien, StorSimple 7000-serien och StorSimple 5000-serien.

### <a name="storsimple-device-configuration-prerequisites"></a>Konfigurationskraven för StorSimple-enhet
StorSimple-enheten är en blockenhet och kräver därför en filserver där data kan finnas. Vi rekommenderar att du använder en separat server i stället för en befintlig server från SharePoint-servergruppen. Den här filservern måste finnas på samma lokala nätverk (LAN) som SQL Server-datorn som är värd för innehållsdatabaserna.

> [!TIP]
> * Om du konfigurerar SharePoint-servergruppen för hög tillgänglighet, bör du också distribuera filserver för hög tillgänglighet.
> * Om du inte sparar innehållsdatabasen på StorSimple-enheten använda Metodtips för traditionella hög tillgänglighet som har stöd för RBS. SQL Server-kluster stöder RBS, när SQL Server spegling inte. 


Se till att StorSimple-enheten är korrekt konfigurerad och att rätt volymer att stödja din distribution av SharePoint har konfigurerats och är åtkomlig från SQL Server-datorn. Gå till [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md) om du ännu inte har distribuerat och konfigurerat din StorSimple-enhet. Obs IP-adressen för StorSimple-enhet. Du behöver den under StorSimple Adapter för SharePoint-installationen.

Kontrollera dessutom att volymen som ska användas för BLOB externalization uppfyller följande krav:

* Volymen måste vara formaterad med en storlek på allokeringsenhet 64 KB.
* Din frontwebb (WFE) och programservrar måste kunna komma åt volymen via en Universal Naming Convention (UNC)-sökväg.
* SharePoint-servergruppen måste konfigureras för att skriva till volymen.

> [!NOTE]
> När du installerar och konfigurerar kortet, alla BLOB-externalization måste gå via StorSimple-enheten (enheten kommer presentera volymer för SQL Server och hantera lagringsnivåer). Du kan inte använda andra mål för BLOB externalization.


Om du tänker använda StorSimple Snapshot Manager för att ta ögonblicksbilder BLOB-och databasen måste du installera StorSimple Snapshot Manager på databasservern så att den kan använda SQL-skrivartjänsten för att implementera Windows Volume Shadow Copy Service (VSS).

> [!IMPORTANT]
> StorSimple Snapshot Manager stöder inte VSS-skrivaren för SharePoint och det går inte att ta programkonsekventa ögonblicksbilder av SharePoint-data. I en SharePoint-scenariot ger StorSimple Snapshot Manager endast kraschsäkra säkerhetskopior.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Förutsättningar för konfiguration av SharePoint-servergrupp
Kontrollera att din SharePoint-servergrupp är korrekt konfigurerad, enligt följande:

* Kontrollera att din SharePoint-servergrupp är i felfritt tillstånd och kontrollera följande:
* Alla SharePoint WFE och programservrar som registrerats i servergruppen kör och en från servern där du planerar att installera StorSimple Adapter för SharePoint.
* SharePoint-tidstjänsten (SPTimerV3 eller SPTimerV4) körs på varje WFE-servern och programserver.
* Både SharePoint-tidstjänsten och IIS-programpoolen som den centrala administrationsplatsen för SharePoint körs under har administratörsbehörighet.
* Se till att Internet Explorer förbättrad säkerhetskontext (IE ESC) är inaktiverat. Följ dessa steg om du vill inaktivera IE ESC:
  
  1. Stäng alla instanser av Internet Explorer.
  2. Starta Serverhanteraren.
  3. I den vänstra rutan klickar du på **lokal Server**.
  4. I den högra rutan bredvid **Förbättrad säkerhetskonfiguration**, klickar du på **på**.
  5. Under **administratörer**, klickar du på **av**.
  6. Klicka på **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Remote BLOB Storage (RBS)-krav
Kontrollera att du använder en version av SQL Server stöds. Endast följande versioner är stöds och kan använda RBS:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Blobar kan vara externalized på de volymer som StorSimple-enheten visar till SQL Server. Inga andra mål för BLOB externalization stöds.

När du har slutfört alla nödvändiga konfigurationssteg, går du till [installera StorSimple Adapter för SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Installera StorSimple Adapter för SharePoint
Använd följande steg för att installera StorSimple Adapter för SharePoint. Om du installerar om programvaran, se [uppgradera eller installera om StorSimple Adapter för SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Den tid som krävs för installationen är beroende av det totala antalet SharePoint-databaser i din SharePoint-servergrupp.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurera RBS
När du har installerat StorSimple Adapter för SharePoint kan du konfigurera RBS enligt beskrivningen i följande procedur.

> [!TIP]
> StorSimple Adapter för SharePoint som ansluts till sidan Central Administration av SharePoint så att RBS ska vara aktiverat eller inaktiverat på varje innehållsdatabas i SharePoint-servergruppen. Aktivera eller inaktivera RBS på innehållsdatabasen gör dock en IIS-återställning, som, beroende på din Gruppkonfiguration tillfälligt kan störa tillgängligheten för SharePoint frontwebb (WFE). (I faktorer, till exempel användning av en frontend-belastningsutjämnare, aktuella serverarbetsbelastningen och så vidare, kan det begränsa eller eliminera den här avbrott.) För att skydda användare från ett avbrott, rekommenderar vi att du aktiverar eller inaktiverar RBS endast under ett planerat underhållsfönster.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurera skräpinsamling
När objekt har tagits bort från en SharePoint-webbplats, tas de inte bort automatiskt från RBS store volym. I stället en asynkron bakgrundsprogrammet för underhåll tar bort överblivna Blobar från arkivet för filen. Systemadministratörer kan schemalägga den här processen ska köras med jämna mellanrum eller de kan starta det när det behövs.

Det här programmet för underhåll (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) installeras automatiskt på alla SharePoint WFE-servrar och programservrar när du aktiverar RBS. Programmet installeras på följande plats: *starta enheten*: \Program\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

Information om hur du konfigurerar och använder Underhåll programmet finns i [Underhåll RBS i SharePoint Server 2013][8].

> [!IMPORTANT]
> RBS Underhållaren programmet är resurskrävande. Du bör schemalägga den kan köras under perioder med låg aktivitet på SharePoint-servergruppen.


### <a name="delete-orphaned-blobs-immediately"></a>Ta bort överblivna BLOB-objekt direkt
Om du vill ta bort överblivna Blobar omedelbart kan använda du följande anvisningar. Observera att dessa instruktioner är ett exempel på hur detta kan göras i en SharePoint 2013-miljö med följande komponenter:

* Innehållsdatabasens namn är WSS_Content.
* Namnet på SQL Server är SHRPT13 SQL12\SHRPT13.
* Namnet på webbprogrammet är SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Uppgradera eller installera om StorSimple Adapter för SharePoint
Använd följande procedur när du uppgradera SharePoint server och sedan installera om StorSimple Adapter för SharePoint eller bara uppgradera eller installera om kortet i en befintlig SharePoint-servergrupp.

> [!IMPORTANT]
> Granska följande information innan du försöker uppgradera din SharePoint-programvaran och/eller uppgradera eller installera om StorSimple Adapter för SharePoint:
> 
> * Alla filer som tidigare har flyttats till extern lagring via RBS kan inte tillgängliga förrän ominstallationen har slutförts och funktionen RBS aktiveras igen. Utföra en uppgradering eller ominstallation ett planerat underhållsfönster för att begränsa användarna påverkas.
> * Den tid som krävs för uppgradering/ominstallation kan variera beroende på det totala antalet SharePoint-databaserna i SharePoint-servergruppen.
> * När uppgraderingen eller ominstallation är klar måste du aktivera RBS för innehållsdatabaserna. Se [konfigurera RBS](#configure-rbs) för mer information.
> * Om du konfigurerar RBS för en SharePoint-grupp som har ett stort antal databaser (fler än 200), den **Central Administration av SharePoint** sidan kan timeout. Om det inträffar kan du uppdatera sidan. Detta påverkar inte konfigurationsprocessen.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple-Adapter för SharePoint-borttagning
Följande procedurer beskriver hur du flyttar Blobarna tillbaka till SQL Server-innehållsdatabaser och avinstallera sedan StorSimple Adapter för SharePoint. 

> [!IMPORTANT]
> Du måste flytta Blobar tillbaka till innehållsdatabaser innan du avinstallerar programvaran för nätverkskort.


### <a name="before-you-begin"></a>Innan du börjar
Samla in följande information innan du flyttar data tillbaka till SQL Server-innehållsdatabaser och påbörja borttagningen nätverkskort:

* Namnen på alla databaser som RBS är aktiverat
* UNC-sökväg till den konfigurerade BLOB store

### <a name="move-the-blobs-back-to-the-content-databases"></a>Flytta Blobar tillbaka till innehållsdatabaser
Innan du avinstallerar StorSimple Adapter för SharePoint-programvaran måste du migrera alla Blobar som har externalized tillbaka till SQL Server-innehållsdatabaser. Om du försöker avinstallera StorSimple Adapter för SharePoint innan du flyttar alla Blobbar tillbaka till innehållsdatabaserna visas följande varning.

![Varningsmeddelande](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Om du vill flytta tillbaka Blobarna till innehållsdatabaser
1. Hämta alla externalized objekt.
2. Öppna den **Central Administration av SharePoint** sidan och gå till **systeminställningar**.
3. Under **Azure StorSimple**, klickar du på **konfigurera StorSimple-Adapter**.
4. På den **konfigurera StorSimple-Adapter** klickar du på den **inaktivera** knappen under varje innehåll som du vill ta bort från den externa BLOB storage. 
5. Ta bort objekt från SharePoint och överför dem sedan igen.

Du kan också använda Microsoft `RBS Migrate()` PowerShell-cmdlet som ingår i SharePoint. Mer information finns i [migrerar du innehåll till eller från RBS](https://technet.microsoft.com/library/ff628255.aspx).

När du flyttar Blobarna tillbaka till innehållsdatabasen går du till nästa steg: [Avinstallera kortet](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Avinstallera kortet
När du flyttar Blobarna tillbaka till SQL Server-innehållsdatabaser använder du något av följande alternativ för att avinstallera StorSimple Adapter för SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Du använder installationsprogrammet för att avinstallera kortet
1. Använda ett konto med administratörsbehörighet för att logga in på klientsidan (WFE) webbservern.
2. Dubbelklicka på StorSimple Adapter för SharePoint-installationsprogrammet. Installationsguiden startar.
   
    ![Installationsguiden](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Klicka på **Nästa**. Följande sida visas.
   
    ![Konfigurera guiden Ta bort sida](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Klicka på **ta bort** att välja borttagningen. Följande sida visas.
   
    ![Konfigurera guiden bekräftelsesidan](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Klicka på **ta bort** att bekräfta borttagningen. Förloppssidan följande visas.
   
    ![Installationen pågår guidesidan](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. När borttagningen är klar visas sidan Slutför. Klicka på **Slutför** att stänga installationsguiden.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Du använder Kontrollpanelen för att avinstallera kortet
1. Öppna Kontrollpanelen och klicka sedan på **program och funktioner**.
2. Välj **StorSimple Adapter för SharePoint**, och klicka sedan på **avinstallera**.

## <a name="next-steps"></a>Nästa steg
[Läs mer om StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
