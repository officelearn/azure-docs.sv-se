---
title: Installera StorSimple-kort för SharePoint | Microsoft-dokument
description: I artikeln beskrivs hur du installerar och konfigurerar eller tar bort StorSimple-kortet för SharePoint i en SharePoint-servergrupp.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: twooley
ms.openlocfilehash: a841ce8b664389ccd8fdf55de9965f09412fecf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930213"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Installera och konfigurera StorSimple-kortet för SharePoint
## <a name="overview"></a>Översikt
StorSimple-kortet för SharePoint är en komponent som gör att du kan tillhandahålla Microsoft Azure StorSimple flexibel lagring och dataskydd till SharePoint-servergrupper. Du kan använda kortet för att flytta BLOB-innehåll (Binary Large Object) från SQL Server-innehållsdatabaserna till Microsoft Azure StorSimple hybridmolnlagringsenhet.

StorSimple-kortet för SharePoint fungerar som en RBS-provider (Remote BLOB Storage) och använder funktionen SQL Server Remote BLOB Storage för att lagra ostrukturerat SharePoint-innehåll (i form av BLOB) på en filserver som backas upp av en StorSimple-enhet.

> [!NOTE]
> StorSimple-kortet för SharePoint stöder Rbs (Remote BLOB Storage) för SharePoint Server 2010 Remote BLOB Storage. Det stöder inte Extern BLOB-lagring av SharePoint Server 2010 (EBS).


* Om du vill hämta StorSimple-kortet för SharePoint går du till [StorSimple Adapter för SharePoint][1] i Microsoft Download Center.
* Information om hur du planerar för RBS- och RBS-begränsningar finns i [Beslut om att använda RBS i SharePoint 2013][2] eller Plan för [RBS (SharePoint Server 2010).][3]

I resten av den här översikten beskrivs kortfattat rollen för StorSimple-kortet för SharePoint och de kapacitets- och prestandagränser för SharePoint som du bör känna till innan du installerar och konfigurerar kortet. När du har granskat den här informationen går du till [StorSimple-kortet för SharePoint-installation för](#storsimple-adapter-for-sharepoint-installation) att börja konfigurera kortet.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple-adapter för SharePoint-fördelar
På en SharePoint-webbplats lagras innehåll som ostrukturerade BLOB-data i en eller flera innehållsdatabaser. Som standard finns dessa databaser på datorer som kör SQL Server och finns i SharePoint-servergruppen. BLOBs kan snabbt öka i storlek, förbrukar stora mängder lokal lagring. Därför kanske du vill hitta en annan, billigare lagringslösning. SQL Server tillhandahåller en teknik som kallas RBS (Remote Blob Storage) som gör att du kan lagra BLOB-innehåll i filsystemet utanför SQL Server-databasen. Med RBS kan BLOBs finnas i filsystemet på datorn som kör SQL Server, eller så kan de lagras i filsystemet på en annan serverdator.

RBS kräver att du använder en RBS-leverantör, till exempel StorSimple-kortet för SharePoint, för att aktivera RBS i SharePoint. StorSimple-kortet för SharePoint fungerar med RBS, så att du kan flytta BLOBs till en server som backas upp av Microsoft Azure StorSimple-systemet. Microsoft Azure StorSimple lagrar sedan BLOB-data lokalt eller i molnet, baserat på användning. BLOBs som är mycket aktiva (vanligtvis kallas nivå 1 eller heta data) finns lokalt. Mindre aktiva data och arkiveringsdata finns i molnet. När du har aktiverat RBS i en innehållsdatabas lagras allt nytt BLOB-innehåll som skapas i SharePoint på StorSimple-enheten och inte i innehållsdatabasen.

Microsoft Azure StorSimple-implementeringen av RBS ger följande fördelar:

* Genom att flytta BLOB-innehåll till en separat server kan du minska frågebelastningen på SQL Server, vilket kan förbättra SQL Server-svarstider. 
* Azure StorSimple använder deduplicering och komprimering för att minska datastorleken.
* Azure StorSimple ger dataskydd i form av lokala ögonblicksbilder och ögonblicksbilder i molnet. Om du placerar själva databasen på StorSimple-enheten kan du säkerhetskopiera innehållsdatabasen och BLOBs tillsammans på ett konsekvent kraschsätt. (Det går bara att flytta innehållsdatabasen till enheten för StorSimple 8000-serien. Den här funktionen stöds inte för 5000- eller 7000-serien.)
* Azure StorSimple innehåller funktioner för haveriberedskap, inklusive redundans- och volymåterställning (inklusive teståterställning) och snabb återställning av data.
* Du kan använda dataåterställningsprogram, till exempel Kroll Ontrack PowerControls, med StorSimple-ögonblicksbilder av BLOB-data för att utföra återställning på objektnivå av SharePoint-innehåll. (Denna dataåterställningsprogramvara är ett separat köp.)
* StorSimple-kortet för SharePoint ansluts till SharePoint Central Administration-portalen, så att du kan hantera hela SharePoint-lösningen från en central plats.

Flytta BLOB-innehåll till filsystemet kan ge andra kostnadsbesparingar och fördelar. Med hjälp av RBS kan till exempel minska behovet av dyr nivå 1-lagring, och eftersom det förminskar innehållsdatabasen kan RBS minska antalet databaser som krävs i SharePoint-servergruppen. Andra faktorer, till exempel begränsningar för databasens storlek och mängden icke-RBS-innehåll, kan dock också påverka lagringskraven. Mer information om kostnader och fördelar med att använda RBS finns i [Planera för RBS (SharePoint Foundation 2010)][4] och [besluta att använda RBS i SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Kapacitets- och prestandagränser
Innan du överväger att använda RBS i SharePoint-lösningen bör du vara medveten om de testade prestanda- och kapacitetsgränserna för SharePoint Server 2010 och SharePoint Server 2013 och hur dessa gränser relaterar till godtagbara prestanda. Mer information finns i [Programvarugränser och gränser för SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Granska följande innan du konfigurerar RBS:

* Kontrollera att den totala storleken på innehållet (storleken på en innehållsdatabas plus storleken på associerade externa BLOBs) inte överskrider rbs-storleksgränsen som stöds av SharePoint. Den här gränsen är 200 GB. 
  
    **Så här mäter du innehållsdatabas och BLOB-storlek**
  
  1. Kör den här frågan på WFE för central administration. Starta SharePoint Management Shell och ange sedan följande Windows PowerShell-kommando för att hämta storleken på innehållsdatabaserna:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Det här steget hämtar storleken på innehållsdatabasen på disken.
  2. Kör en av följande SQL-frågor i SQL Management Studio i SQL-serverrutan i varje innehållsdatabas och lägg till resultatet i det nummer som erhålls i steg 1.
     
     I SharePoint 2013-innehållsdatabaser anger du:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     I Innehållsdatabaser för SharePoint 2010 anger du:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Det här steget får storleken på de BLOBs som har externaliserats.
* Vi rekommenderar att du lagrar allt BLOB- och databasinnehåll lokalt på StorSimple-enheten. StorSimple-enheten är ett kluster med två noder för hög tillgänglighet. Om du placerar innehållsdatabaser och BLOB:er på StorSimple-enheten finns hög tillgänglighet.
  
    Använd traditionella metodtips för SQL Server-migrering för att flytta innehållsdatabasen till StorSimple-enheten. Flytta databasen först när allt BLOB-innehåll från databasen har flyttats till filresursen via RBS. Om du väljer att flytta innehållsdatabasen till StorSimple-enheten rekommenderar vi att du konfigurerar innehållsdatabaslagringen på enheten som en primär volym.
* Om du använder nivåindelade volymer i Microsoft Azure StorSimple kan det inte garanteras att innehåll som lagras lokalt på StorSimple-enheten inte kommer att nivåindelas till Microsoft Azure-molnlagring. Därför rekommenderar vi att du använder StorSimple lokalt fästa volymer i samband med SharePoint RBS. Detta säkerställer att allt BLOB-innehåll finns kvar lokalt på StorSimple-enheten och inte flyttas till Microsoft Azure.
* Om du inte lagrar innehållsdatabaserna på StorSimple-enheten använder du traditionella metodtips för bästa SQL Server-tillgänglighet som stöder RBS. SQL Server-klustring stöder RBS, medan SQL Server-spegling inte gör det. 

> [!WARNING]
> Om du inte har aktiverat RBS rekommenderar vi inte att du flyttar innehållsdatabasen till StorSimple-enheten. Detta är en oprövad konfiguration.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple-adapter för SharePoint-installation
Innan du kan installera StorSimple-kortet för SharePoint måste du konfigurera StorSimple-enheten och se till att SharePoint-servergruppen och SQL Server-instansieringen uppfyller alla förutsättningar. Den här självstudien beskriver konfigurationskrav samt procedurer för installation och uppgradering av StorSimple-kortet för SharePoint.

## <a name="configure-prerequisites"></a>Konfigurera förutsättningar
Innan du kan installera StorSimple-kortet för SharePoint kontrollerar du att StorSimple-enheten, SharePoint-servergruppen och SQL Server-instansieringen uppfyller följande förutsättningar.

### <a name="system-requirements"></a>Systemkrav
StorSimple-adaptern för SharePoint fungerar med följande maskinvara och programvara:

* Operativsystem som stöds – Windows Server 2008 R2 SP1, Windows Server 2012 eller Windows Server 2012 R2
* SharePoint-versioner som stöds – SharePoint Server 2010 eller SharePoint Server 2013
* SQL Server-versioner – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition eller SQL Server 2012 Enterprise Edition
* StorSimple-enheter som stöds – StorSimple 8000-serien, StorSimple 7000-serien eller StorSimple 5000-serien.

### <a name="storsimple-device-configuration-prerequisites"></a>Förutsättningar för konfiguration av StorSimple-enhet
StorSimple-enheten är en blockenhet och kräver därför en filserver där data kan vara värd. Vi rekommenderar att du använder en separat server i stället för en befintlig server från SharePoint-servergruppen. Den här filservern måste finnas i samma lokala nätverk (LAN) som DEN SQL Server-dator som är värd för innehållsdatabaserna.

> [!TIP]
> * Om du konfigurerar SharePoint-servergruppen för hög tillgänglighet bör du distribuera filservern för hög tillgänglighet också.
> * Om du inte lagrar innehållsdatabasen på StorSimple-enheten använder du traditionella metodtips för bästa hög tillgänglighet som stöder RBS. SQL Server-klustring stöder RBS, medan SQL Server-spegling inte gör det. 


Kontrollera att StorSimple-enheten är korrekt konfigurerad och att lämpliga volymer som stöder Din SharePoint-distribution är konfigurerade och tillgängliga från SQL Server-datorn. Gå till [Distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md) om du ännu inte har distribuerat och konfigurerat din StorSimple-enhet. Observera StorSimple-enhetens IP-adress. du behöver det under StorSimple Adapter för SharePoint-installation.

Kontrollera dessutom att volymen som ska användas för BLOB-externalisering uppfyller följande krav:

* Volymen måste formateras med en 64 KB allokeringsenhetsstorlek.
* Din webbklämmning (WFE) och programservrar måste kunna komma åt volymen via en UNC-sökväg (Universal Naming Convention).
* SharePoint-servergruppen måste vara konfigurerad för att skriva till volymen.

> [!NOTE]
> När du har installerat och konfigurerat kortet måste all BLOB-externalisering gå via StorSimple-enheten (enheten visar volymerna till SQL Server och hanterar lagringsnivåerna). Du kan inte använda några andra mål för BLOB-externalisering.


Om du planerar att använda StorSimple Snapshot Manager för att ta ögonblicksbilder av BLOB- och databasdata måste du installera StorSimple Snapshot Manager på databasservern så att den kan använda SQL Writer-tjänsten för att implementera VSS (Windows Volume Shadow Copy Service).

> [!IMPORTANT]
> StorSimple Snapshot Manager stöder inte SharePoint VSS Writer och kan inte ta programkonsekventa ögonblicksbilder av SharePoint-data. I ett SharePoint-scenario tillhandahåller StorSimple Snapshot Manager endast kraschkonsekventa säkerhetskopior.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Förutsättningar för konfiguration av SharePoint-servergrupp
Kontrollera att din SharePoint-servergrupp är korrekt konfigurerad enligt följande:

* Kontrollera att din SharePoint-servergrupp är i felfritt tillstånd och kontrollera följande:
* Alla SharePoint WFE och programservrar som är registrerade i servergruppen körs och kan pingas från den server där du kommer att installera StorSimple-kortet för SharePoint.
* SharePoint Timer-tjänsten (SPTimerV3 eller SPTimerV4) körs på varje WFE-server och programserver.
* Både SharePoint Timer-tjänsten och IIS-programpoolen som sharepoint-central administration-webbplatsen körs på har administratörsbehörighet.
* Kontrollera att IE ESC (Internet Explorer Enhanced Security Context) är inaktiverat. Så här inaktiverar du IE ESC:
  
  1. Stäng alla instanser av Internet Explorer.
  2. Starta Serverhanteraren.
  3. Klicka på Lokal **server**i den vänstra rutan.
  4. Klicka på **På**i den högra rutan **bredvid IE Enhanced Security Configuration**i den högra rutan.
  5. Klicka på **Av**under **Administratörer.**
  6. Klicka på **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Rbs-förutsättningar (Remote BLOB Storage)
Kontrollera att du använder en version av SQL Server som stöds. Endast följande versioner stöds och kan använda RBS:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

BLOBs kan endast externaliseras på de volymer som StorSimple-enheten presenterar för SQL Server. Inga andra mål för BLOB-externalisering stöds.

När du har slutfört alla nödvändiga konfigurationssteg går du till [Installera StorSimple-kortet för SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Installera StorSimple-kortet för SharePoint
Använd följande steg för att installera StorSimple-kortet för SharePoint. Om du installerar om programvaran läser du [Uppgradera eller installera om StorSimple-kortet för SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Hur länge installationen ska behövas beror på det totala antalet SharePoint-databaser i SharePoint-servergruppen.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurera RBS
När du har installerat StorSimple-kortet för SharePoint konfigurerar du RBS enligt beskrivningen i följande procedur.

> [!TIP]
> StorSimple-kortet för SharePoint ansluts till sidan Central administration i SharePoint, vilket gör att RBS kan aktiveras eller inaktiveras i varje innehållsdatabas i SharePoint-servergruppen. Om du aktiverar eller inaktiverar RBS i innehållsdatabasen kan dock en IIS-återställning, som, beroende på servergruppens konfiguration, tillfälligt kan störa tillgängligheten för SharePoint-webbfronten (WFE). (Faktorer som användning av en frontend belastningsutjämnare, den aktuella servern arbetsbelastning, och så vidare, kan begränsa eller eliminera denna störning.) För att skydda användare från störningar rekommenderar vi att du endast aktiverar eller inaktiverar RBS under ett planerat underhållsfönster.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurera skräpinsamling
När objekt tas bort från en SharePoint-webbplats tas de inte bort automatiskt från RBS-butiksvolymen. I stället tar ett asynkront, bakgrundsunderhållsprogram bort överblivna BLOB:er från filarkivet. Systemadministratörer kan schemalägga den här processen så att den körs med jämna mellanrum eller så kan de starta den när det behövs.

Det här underhållsprogrammet (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) installeras automatiskt på alla SharePoint WFE-servrar och programservrar när du aktiverar RBS. Programmet installeras på följande plats: *startenhet*:\Program\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

Information om hur du konfigurerar och använder underhållsprogrammet finns [i Underhåll rbs i SharePoint Server 2013][8].

> [!IMPORTANT]
> RBS-underhållsprogrammet är resurskrävande. Du bör schemalägga den så att den bara körs under perioder av ljusaktivitet i SharePoint-servergruppen.


### <a name="delete-orphaned-blobs-immediately"></a>Ta bort överblivna BLOBs omedelbart
Om du behöver ta bort överblivna BLOBs omedelbart kan du använda följande instruktioner. Observera att dessa instruktioner är ett exempel på hur detta kan göras i en SharePoint 2013-miljö med följande komponenter:

* Namnet på innehållsdatabasen är WSS_Content.
* SQL Server-namnet är SHRPT13-SQL12\SHRPT13.
* Webbprogrammets namn är SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Uppgradera eller installera om StorSimple-kortet för SharePoint
Använd följande procedur för att uppgradera SharePoint-servern och sedan installera om StorSimple-kortet för SharePoint eller för att helt enkelt uppgradera eller installera om kortet i en befintlig SharePoint-servergrupp.

> [!IMPORTANT]
> Läs följande information innan du försöker uppgradera SharePoint-programvaran och/eller uppgradera eller installera om StorSimple-kortet för SharePoint:
> 
> * Filer som tidigare flyttats till extern lagring via RBS kommer inte att vara tillgängliga förrän ominstallationen är klar och RBS-funktionen är aktiverad igen. Om du vill begränsa användarnas påverkan utför du en uppgradering eller ominstallation under ett planerat underhållsfönster.
> * Hur länge som krävs för uppgraderingen/ominstallationen kan variera beroende på det totala antalet SharePoint-databaser i SharePoint-servergruppen.
> * När uppgraderingen/ominstallationen är klar måste du aktivera RBS för innehållsdatabaserna. Mer information finns i [Konfigurera RBS.](#configure-rbs)
> * Om du konfigurerar RBS för en SharePoint-servergrupp som har ett mycket stort antal databaser (större än 200) kan sidan Central administration i **SharePoint** ta time out. Om det inträffar uppdaterar du sidan. Detta påverkar inte konfigurationsprocessen.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple-kort för borttagning av SharePoint
Följande procedurer beskriver hur du flyttar tillbaka BLOB:erna till SQL Server-innehållsdatabaserna och sedan avinstallerar StorSimple-kortet för SharePoint. 

> [!IMPORTANT]
> Du måste flytta tillbaka BLOB:erna till innehållsdatabaserna innan du avinstallerar adapterprogrammet.


### <a name="before-you-begin"></a>Innan du börjar
Samla in följande information innan du flyttar tillbaka data till SQL Server-innehållsdatabaserna och påbörjar borttagningen av kortet:

* Namnen på alla databaser för vilka RBS är aktiverat
* UNC-sökvägen till det konfigurerade BLOB-arkivet

### <a name="move-the-blobs-back-to-the-content-databases"></a>Flytta tillbaka BLOB:erna till innehållsdatabaserna
Innan du avinstallerar StorSimple-kortet för SharePoint-programvara måste du migrera alla BLOB:er som externaliserades tillbaka till SQL Server-innehållsdatabaserna. Om du försöker avinstallera StorSimple-kortet för SharePoint innan du flyttar tillbaka alla BLOB-databaser till innehållsdatabaserna visas följande varningsmeddelande.

![Varningsmeddelande](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Så här flyttar du tillbaka BLOB:erna till innehållsdatabaserna
1. Hämta var och en av de externa objekten.
2. Öppna sidan **Central administration i SharePoint** och bläddra till **Systeminställningar**.
3. Klicka på **Konfigurera StorSimple-kort**under **Azure StorSimple**.
4. På sidan **Konfigurera StorSimple-kort** klickar du på knappen **Inaktivera** under var och en av de innehållsdatabaser som du vill ta bort från extern BLOB-lagring. 
5. Ta bort objekten från SharePoint och ladda sedan upp dem igen.

Du kan också använda `RBS Migrate()` den Microsoft PowerShell-cmdlet som ingår i SharePoint. Mer information finns i [Migrera innehåll till eller från RBS](https://technet.microsoft.com/library/ff628255.aspx).

När du har flyttat tillbaka BLOB:erna till innehållsdatabasen går du till nästa steg: [Avinstallera kortet](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Avinstallera kortet
När du har flyttat tillbaka BLOB:erna till SQL Server-innehållsdatabaserna använder du något av följande alternativ för att avinstallera StorSimple-kortet för SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Så här använder du installationsprogrammet för att avinstallera kortet
1. Använd ett konto med administratörsbehörighet för att logga in på WFE-servern (Web Front-end).
2. Dubbelklicka på StorSimple-kortet för SharePoint-installationsprogrammet. Installationsguiden startar.
   
    ![Guiden Konfigurera installationer](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Klicka på **Nästa**. Följande sida visas.
   
    ![Ta bort sida för installationsguiden](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Klicka på **Ta bort** för att välja borttagningsprocessen. Följande sida visas.
   
    ![Bekräftelsesida för guiden Konfigurera installationsprogrammet](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Klicka på **Ta bort** för att bekräfta borttagningen. Följande förloppssida visas.
   
    ![Förloppssida för installationsguiden](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. När borttagningen är klar visas slutsidan. Stäng installationsguiden genom att klicka på **Slutför.**

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Så här använder du Kontrollpanelen för att avinstallera kortet
1. Öppna Kontrollpanelen och klicka sedan på **Program och funktioner**.
2. Välj **StorSimple-adapter för SharePoint**och klicka sedan på **Avinstallera**.

## <a name="next-steps"></a>Nästa steg
[Läs mer om StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
