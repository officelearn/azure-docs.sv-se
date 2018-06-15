---
title: Installera StorSimple Adapter för SharePoint | Microsoft Docs
description: Beskriver hur du installerar och konfigurerar eller ta bort nätverkskortet StorSimple för SharePoint i en SharePoint-servergrupp.
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
ms.openlocfilehash: 2e1b231a5cf13d2655ff66c7e48752729c580f48
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32193826"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Installera och konfigurera StorSimple-kortet för SharePoint
## <a name="overview"></a>Översikt
StorSimple-Adapter för SharePoint är en komponent som du kan ange Microsoft Azure StorSimple flexibel lagring och dataskydd till SharePoint-servergrupper. Du kan använda kortet för att flytta binärt stort objekt (BLOB) innehåll från SQL Server-innehållsdatabaser till Microsoft Azure StorSimple hybrid cloud-lagringsenhet.

StorSimple-kortet för SharePoint fungerar som en Remote BLOB Storage (RBS)-provider och använder funktionen SQL Server Remote BLOB Storage för att lagra Ostrukturerade SharePoint-innehåll (i form av Blobbar) på en filserver som backas upp av en StorSimple-enhet.

> [!NOTE]
> StorSimple-kortet för SharePoint stöder SharePoint Server 2010 Remote BLOB Storage (RBS). Det har inte stöd för SharePoint Server 2010 externa BLOB Storage (EBS).


* Om du vill hämta StorSimple-kortet för SharePoint går du till [StorSimple-kortet för SharePoint] [ 1] i Microsoft Download Center.
* För information om planering för RBS och RBS begränsningar, gå till [beslutar att använda RBS i SharePoint 2013] [ 2] eller [planera för RBS (SharePoint Server 2010)][3].

Resten av den här översikten beskrivs kortfattat rollen för StorSimple-kortet för SharePoint och SharePoint Kapacitets- och gränser som du bör känna till innan du installerar och konfigurerar kortet. När du har läst informationen går du till [StorSimple-kortet för SharePoint-installationen](#storsimple-adapter-for-sharepoint-installation) att börja konfigurera nätverkskortet.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple-kortet för SharePoint-förmåner
Innehåll lagras i en SharePoint-webbplats som Ostrukturerade BLOB-data i en eller flera innehållsdatabaser. Som standard finns de här databaserna på datorer som kör SQL Server och finns i SharePoint-servergrupp. Blobbar kan snabbt ökar i storlek, förbrukar stora mängder lokal lagring. Därför kanske du vill söka efter en annan, mindre kostsam lagringslösning. SQL Server innehåller en teknik som kallas Remote Blob Storage (RBS) där du kan lagra BLOB-innehåll i filsystemet utanför SQL Server-databasen. Blobbar kan finnas i filsystemet på datorn som kör SQL Server med RBS, eller de kan lagras i filsystemet på en annan server-datorn.

RBS kräver att du använder en RBS provider, till exempel StorSimple-kortet för SharePoint, för att aktivera RBS i SharePoint. StorSimple-kortet för SharePoint fungerar med RBS, så att du kan flytta Blobbar till en server säkerhetskopieras av Microsoft Azure StorSimple-system. Microsoft Azure StorSimple sedan lagrar BLOB-data lokalt eller i molnet, baserat på användning. Blobbar som är mycket aktiv (kallas vanligtvis nivå 1 eller varm data) finnas lokalt. Mindre aktiva data och arkiveringsdata finns i molnet. När du aktiverar RBS på en innehållsdatabas lagras alla nya BLOB-innehåll som skapats i SharePoint på StorSimple-enheten och inte i innehållsdatabasen.

Microsoft Azure StorSimple-implementering av RBS ger följande fördelar:

* Genom att flytta BLOB-innehåll till en enskild server, kan du minska frågebelastningen på SQL Server, vilket kan förbättra svarstiden för SQL Server. 
* Azure StorSimple använder deduplicering och komprimering för att minska datastorleken på.
* Azure StorSimple ger dataskydd i form av lokala och molnögonblicksbilder. Även om du placerar själva databasen på StorSimple-enheten, kan du säkerhetskopiera innehållsdatabasen och Blobbar tillsammans i en krasch konsekvent sätt. (Flytta innehållsdatabasen till enheten har endast stöd för enheten StorSimple 8000-serien. Den här funktionen stöds inte för 5000 och 7000-serien.)
* Azure StorSimple innehåller disaster recovery funktioner inklusive redundans, fil- och återställning (inklusive test recovery) och snabb återställning av data.
* Du kan använda data recovery programvara, till exempel Kroll Ontrack PowerControls med StorSimple ögonblicksbilder av BLOB-data för att utföra återställning på objektnivå för SharePoint-innehåll. (Den här programvaran för återställning av data är en separat köp.)
* StorSimple-kortet för SharePoint ansluts till Central Administration av SharePoint-portalen så att du kan hantera hela lösningen SharePoint från en central plats.

Flytta BLOB-innehåll i filsystemet kan ge andra kostnadsbesparingar och fördelar. Till exempel med RBS minska behovet av dyr nivå 1-lagring och eftersom den krymper innehållsdatabasen RBS kan minska antalet databaser som krävs i SharePoint-servergrupp. Andra faktorer, till exempel storleksbegränsningar för databasen och mängden icke RBS innehåll kan dock även påverka lagringsbehov. Mer information om kostnader och fördelarna med att använda RBS finns [planera för RBS (SharePoint Foundation 2010)] [ 4] och [beslutar att använda RBS i SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Kapacitets- och gränser
Innan du överväga att använda RBS i SharePoint-lösningen, bör du vara medveten om testade prestanda och kapacitetsgränserna för SharePoint Server 2010 och SharePoint Server 2013 och hur dessa begränsningar relaterade till acceptabel prestanda. Mer information finns i [programvara gränser och begränsningar för SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Granska följande innan du konfigurerar RBS:

* Kontrollera att den totala storleken på innehållet (storleken på en innehållsdatabas) plus storleken på alla associerade externalized Blobbar inte överskrider storleksgränsen RBS stöds av SharePoint. Den här gränsen är 200 GB. 
  
    **Måttet innehållsdatabasen och BLOB storlek**
  
  1. Kör frågan på Central Administration WFE. Starta hanteringsgränssnittet för SharePoint och ange sedan följande Windows PowerShell-kommando för att hämta storleken på innehållsdatabaserna:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Det här steget hämtar storleken på innehållsdatabasen på disken.
  2. Kör något av följande SQL-frågor i SQL Management Studio rutan SQL server på varje innehållsdatabas och Lägg till resultatet nummer hämtades i steg 1.
     
     Innehållsdatabaser för SharePoint 2013, ange:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     SharePoint 2010 innehållsdatabaser, ange:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Det här steget hämtar storleken på Blobbar som har varit externalized.
* Vi rekommenderar att du lagrar alla BLOB och databasen innehållet lokalt på StorSimple-enheten. StorSimple-enhet är ett kluster med två noder för hög tillgänglighet. Placera innehållsdatabaser och Blobbar på StorSimple-enhet ger hög tillgänglighet.
  
    Använda traditionella SQLServer-migrering bästa praxis för att flytta innehållsdatabasen till StorSimple-enhet. Flytta databasen först när alla BLOB-innehåll från databasen har flyttats till filresursen via RBS. Om du vill flytta innehållsdatabasen till StorSimple-enhet, rekommenderar vi att du konfigurerar innehållsdatabasen lagring på enheten som en primär volym.
* Det finns inget sätt att garantera att innehåll som lagras lokalt på StorSimple enheten inte kommer nivåer till Microsoft Azure-molnlagring i Microsoft Azure StorSimple, om du använder nivåindelade volymer. Vi rekommenderar därför använda tillsammans med SharePoint RBS lokalt Fäst StorSimple-volymer. Detta säkerställer att alla BLOB-innehåll förblir lokalt på StorSimple-enheten och inte har flyttats till Microsoft Azure.
* Om du inte sparar innehållsdatabaserna på StorSimple-enheten använda traditionella SQL Server med hög tillgänglighet bästa praxis som stöder RBS. SQL Server-kluster stöder RBS, när SQL Server spegling inte. 

> [!WARNING]
> Om du inte har aktiverat RBS rekommenderar vi inte flytta innehållsdatabasen till StorSimple-enhet. Detta är en otestade konfiguration.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple-kortet för SharePoint-installationen
Innan du kan installera StorSimple-kortet för SharePoint, måste du konfigurera StorSimple-enheten och kontrollera att SharePoint-servergrupp och instansiering av SQL Server uppfyller alla krav. Den här självstudiekursen beskriver konfigurationskrav, samt procedurer för installation och uppgradering av StorSimple-kortet för SharePoint.

## <a name="configure-prerequisites"></a>Konfigurera krav
Innan du kan installera StorSimple-kortet för SharePoint, kontrollerar du att StorSimple-enhet, SharePoint-servergrupp och instansiering av SQL Server uppfyller följande krav.

### <a name="system-requirements"></a>Systemkrav
StorSimple-kortet för SharePoint fungerar tillsammans med följande maskinvara och programvara:

* Operativsystem som stöds – Windows Server 2008 R2 SP1, Windows Server 2012 eller Windows Server 2012 R2
* SharePoint versioner som stöds – SharePoint Server 2010 eller SharePoint Server 2013
* SQL Server versioner som stöds – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition eller SQL Server 2012 Enterprise Edition
* Stöd för StorSimple-enheter – StorSimple 8000-serien, StorSimple 7000-serien eller StorSimple 5000-serien.

### <a name="storsimple-device-configuration-prerequisites"></a>Konfigurationskrav för StorSimple-enhet
StorSimple-enhet är en blockenhet och kräver därför en filserver där data kan finnas. Vi rekommenderar att du använder en separat server i stället för en befintlig server från SharePoint-servergruppen. Den här filservern måste finnas på samma lokala nätverk (LAN) som SQL Server-datorn som är värd för innehållsdatabaserna.

> [!TIP]
> * Om du konfigurerar SharePoint-servergruppen för hög tillgänglighet bör du också distribuera filserver för hög tillgänglighet.
> * Om du inte sparar innehållsdatabasen på StorSimple-enheten, Använd traditionella hög tillgänglighet säkerhetsmetoder som har stöd för RBS. SQL Server-kluster stöder RBS, när SQL Server spegling inte. 


Se till att din StorSimple-enhet är korrekt konfigurerat och att volymerna som stöd för SharePoint-distributionen har konfigurerats och är åtkomlig från SQL Server-datorn. Gå till [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md) om du ännu inte har distribuerat och konfigurerat din StorSimple-enhet. Observera IP-adressen för StorSimple-enhet. Du behöver den under StorSimple nätverkskort för SharePoint-installationen.

Kontrollera dessutom att volymen som ska användas för BLOB-externalization uppfyller följande krav:

* Volymen måste vara formaterad med en storlek på allokeringsenhet 64 KB.
* Din frontwebb (WFE) och programservrar måste kunna komma åt volymen via en Universal Naming Convention (UNC)-sökväg.
* SharePoint-servergruppen måste konfigureras för att skriva till volymen.

> [!NOTE]
> När du installerar och konfigurerar kortet alla BLOB externalization måste gå igenom StorSimple-enhet (enheten kommer presentera volymer för SQL Server och hantera lagringsnivåerna). Du kan inte använda några andra mål för BLOB-externalization.


Om du planerar att använda StorSimple Snapshot Manager för att ta ögonblicksbilder av data BLOB och databas måste du installera StorSimple Snapshot Manager på servern så att den kan använda SQL-skrivartjänsten för att implementera Windows Volume Shadow Copy Service (VSS).

> [!IMPORTANT]
> StorSimple Snapshot Manager stöder inte VSS-skrivaren för SharePoint och det går inte att ta programkonsekventa ögonblicksbilder av SharePoint-data. I en SharePoint-scenariot ger StorSimple Snapshot Manager endast kraschsäkra säkerhetskopior.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Konfigurationskrav för SharePoint-grupp
Kontrollera att SharePoint-servergrupp är korrekt konfigurerad, enligt följande:

* Kontrollera att SharePoint-servergrupp är i felfritt tillstånd och kontrollera följande:
* Alla SharePoint WFE och programservrar som registrerats i servergruppen körs och kan pingas från servern där du ska installera StorSimple-kortet för SharePoint.
* SharePoint-tidstjänsten (SPTimerV3 eller SPTimerV4) körs på varje WFE-servern och programservern.
* Både SharePoint-tidstjänsten och IIS-programpoolen som webbplatsen SharePoint Central Administration körs under har administratörsbehörighet.
* Se till att Internet Explorer förbättrad säkerhetskontext (IE ESC) är inaktiverad. Följ dessa steg om du vill inaktivera IE ESC:
  
  1. Stäng alla instanser av Internet Explorer.
  2. Starta Serverhanteraren.
  3. I den vänstra rutan klickar du på **lokal Server**.
  4. I den högra rutan bredvid **Förbättrad säkerhetskonfiguration**, klickar du på **på**.
  5. Under **administratörer**, klickar du på **av**.
  6. Klicka på **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Krav för Remote BLOB Storage (RBS)
Kontrollera att du använder en version av SQL Server stöds. Endast följande versioner är kunna använda RBS och som stöds:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Blobbar kan vara externalized på de volymer som StorSimple-enhet som presenterar till SQL Server. Inga mål för BLOB-externalization stöds.

Gå till när du har slutfört alla nödvändiga konfigurationssteg [installerar StorSimple-kort för SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Installera StorSimple-kortet för SharePoint
Använd följande steg för att installera StorSimple-kortet för SharePoint. Om du installerar om programmet, se [uppgradera eller installera om StorSimple-kortet för SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Den tid som krävs för installationen är beroende av det totala antalet SharePoint-databaserna i SharePoint-servergrupp.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurera RBS
När du har installerat StorSimple-kortet för SharePoint Konfigurera RBS enligt beskrivningen i följande procedur.

> [!TIP]
> StorSimple-kortet för SharePoint ansluts till Central Administration av SharePoint-sida tillåter RBS aktiverat eller inaktiverat på varje innehållsdatabas i SharePoint-gruppen. Aktivera eller inaktivera RBS på innehållsdatabasen gör emellertid en IIS-återställning som, beroende på din Gruppkonfiguration tillfälligt kan störa tillgängligheten för SharePoint frontwebb (WFE). (I faktorer, till exempel användning av en frontend-belastningsutjämnare, aktuella serverbelastningar och så vidare, kan det begränsa eller eliminera den här avbrott.) För att skydda användare från ett avbrott, rekommenderar vi att du aktiverar eller inaktiverar RBS endast under ett planerat underhåll.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurera skräpinsamling
När objekt tas bort från en SharePoint-webbplats, är de inte bort automatiskt från RBS store volym. I stället en asynkron Underhåll bakgrundsprogrammet tar bort överblivna Blobbar från filarkivet. Administratörer kan schemalägga processen ska köras regelbundet eller de kan starta den vid behov.

Det här programmet för underhåll (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) installeras automatiskt på alla SharePoint WFE-servrar och programservrar när du aktiverar RBS. Programmet installeras på följande plats: *starta enheten*: \Program\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

Information om hur du konfigurerar och använder Underhåll programmet finns [Underhåll RBS i SharePoint Server 2013][8].

> [!IMPORTANT]
> RBS Underhållaren programmet är resurskrävande. Du schemalägga den kan köras under perioder med lågt på SharePoint-servergruppen.


### <a name="delete-orphaned-blobs-immediately"></a>Ta bort överblivna Blobbar omedelbart
Du kan använda följande instruktioner om du behöver ta bort överblivna Blobbar omedelbart. Observera att dessa instruktioner är ett exempel på hur detta kan göras i en SharePoint 2013-miljö med följande komponenter:

* Innehållsdatabasens namn är WSS_Content.
* Namnet på SQL Server är SHRPT13 SQL12\SHRPT13.
* Namnet på webbprogrammet är SharePoint-80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Uppgradera eller installera om StorSimple-kortet för SharePoint
Använd följande procedur när du uppgraderar SharePoint server och sedan installera om StorSimple-kortet för SharePoint eller bara uppgradera eller installera om kortet i en befintlig SharePoint-servergrupp.

> [!IMPORTANT]
> Granska följande information innan du försöker att uppgradera din SharePoint-programvaran och/eller uppgradera eller installera om StorSimple-kortet för SharePoint:
> 
> * Alla filer som tidigare har flyttats till extern lagring via RBS blir inte tillgängliga förrän ominstallationen har slutförts och funktionen RBS aktiveras igen. Om du vill begränsa effekten av användaren utför en uppgradering eller ominstallation under ett planerat underhåll.
> * Den tid som krävs för uppgradering eller ominstallation kan variera beroende på det totala antalet SharePoint-databaserna i SharePoint-servergrupp.
> * När uppgraderingen eller ominstallation är klar måste du aktivera RBS för innehållsdatabaserna. Se [konfigurera RBS](#configure-rbs) för mer information.
> * Om du konfigurerar RBS för en SharePoint-grupp som har ett stort antal databaser (större än 200), den **Central Administration av SharePoint** sida kan timeout. Om det inträffar kan du uppdatera sidan. Detta påverkar inte konfigurationen.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple-kortet för borttagning av SharePoint
I följande procedurer beskrivs hur du flyttar Blobar tillbaka till innehållsdatabaser för SQL Server och sedan avinstallera StorSimple-kortet för SharePoint. 

> [!IMPORTANT]
> Du måste flytta Blobar tillbaka till innehållsdatabaserna innan du avinstallerar kort-programvaran.


### <a name="before-you-begin"></a>Innan du börjar
Samla in följande information innan du flyttar data tillbaka till innehållsdatabaser för SQL Server och påbörja borttagningen nätverkskort:

* Namnen på alla databaser som RBS är aktiverad
* UNC-sökväg till den konfigurerade blobstore

### <a name="move-the-blobs-back-to-the-content-databases"></a>Flytta Blobar tillbaka till innehållsdatabaserna
Innan du avinstallerar StorSimple-kortet för SharePoint-programvaran måste du migrera alla Blobbar som har externalized tillbaka till SQL Server-innehållsdatabaserna. Om du försöker avinstallera StorSimple-kortet för SharePoint innan du flyttar alla BLOB tillbaka till innehållsdatabaserna visas följande varningsmeddelande.

![Varningsmeddelande](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Om du vill flytta tillbaka den BLOB-objekt till innehållsdatabaserna
1. Hämta alla externalized objekt.
2. Öppna den **Central Administration av SharePoint** sidan och bläddra till **systeminställningar**.
3. Under **Azure StorSimple**, klickar du på **konfigurera StorSimple nätverkskort**.
4. På den **konfigurera StorSimple nätverkskort** klickar du på den **inaktivera** knapp under varje innehållsdatabaser som du vill ta bort från den externa BLOB storage. 
5. Ta bort objekt från SharePoint och överför dem igen.

Du kan också använda Microsoft` RBS Migrate()` PowerShell-cmdlet som ingår i SharePoint. Mer information finns i [migrerar innehåll till eller från RBS](https://technet.microsoft.com/library/ff628255.aspx).

När du flyttar Blobar tillbaka till innehållsdatabasen går du till nästa steg: [avinstallerar adapter](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Avinstallera adapter
När du flyttar Blobar tillbaka till innehållsdatabaser för SQL Server med någon av följande alternativ för att avinstallera StorSimple-kortet för SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Använda installationsprogrammet för att avinstallera kortet
1. Använd ett konto med administratörsbehörighet för att logga in på web front-end (WFE)-server.
2. Dubbelklicka på StorSimple-kortet för SharePoint-installationsprogrammet. Startar installationsguiden.
   
    ![Installationsguiden](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Klicka på **Nästa**. Följande sida visas.
   
    ![Guiden Ta bort sidan för installationsprogrammet](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Klicka på **ta bort** att välja borttagningen. Följande sida visas.
   
    ![Konfigurera guiden bekräftelsesidan](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Klicka på **ta bort** att bekräfta borttagningen. Förloppssidan följande visas.
   
    ![Installationsprogrammet guidesidan pågår](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. När borttagningen är klar visas sidan Slutför. Klicka på **Slutför** att stänga installationsguiden.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Använda Kontrollpanelen för att avinstallera kortet
1. Öppna Kontrollpanelen och klicka sedan på **program och funktioner**.
2. Välj **StorSimple-kortet för SharePoint**, och klicka sedan på **avinstallera**.

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
