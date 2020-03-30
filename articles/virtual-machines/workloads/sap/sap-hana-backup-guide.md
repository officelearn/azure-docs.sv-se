---
title: Backup guide för SAP HANA på Azure Virtuella datorer | Microsoft-dokument
description: Backup guide för SAP HANA ger två stora backup möjligheter för SAP HANA på Azure virtuella datorer
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: bb32350597059209e5baf01d53b0c59fdc2344f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255241"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Reservguide för SAP HANA på virtuella Azure-datorer

## <a name="getting-started"></a>Komma igång

Säkerhetskopieringsguiden för SAP HANA som körs på virtuella Azure-datorer beskriver bara Azure-specifika ämnen. Allmän SAP HANA-säkerhetskopieringsrelaterade objekt finns i SAP HANA-dokumentationen. Vi förväntar oss att du är bekant med princip databas backup strategier, skälen och motiv för att ha en sund och giltig backup strategi, och är medvetna om de krav ditt företag har för säkerhetskopiering förfarande, lagringsperiod av säkerhetskopior och återställa Förfarande.

SAP HANA stöds officiellt på olika Azure VM-typer, till exempel Azure M-Series. En fullständig lista över SAP HANA-certifierade virtuella Azure-datorer och HANA-enheter för stora instanser finns i [Hitta certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Microsoft Azure erbjuder ett antal enheter där SAP HANA körs icke-virtualiserad på fysiska servrar. Den här tjänsten kallas [hana stora instanser](hana-overview-architecture.md). Den här guiden täcker inte säkerhetskopieringsprocesser och verktyg för STORA HANA-instanser. Men kommer att begränsas till virtuella Azure-datorer. Mer information om säkerhetskopiering/återställningsprocesser med STORA HANA-instanser finns i artikeln [HLI Backup and Restore](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore).

Fokus för den här artikeln ligger på tre möjligheter till säkerhetskopiering för SAP HANA på virtuella Azure-datorer:

- HANA säkerhetskopiering via [Azure Backup Services](https://docs.microsoft.com/azure/backup/backup-overview) 
- HANA säkerhetskopiering till filsystemet i en Virtuell Azure Linux-dator (se [SAP HANA Azure Backup på filnivå)](sap-hana-backup-file-level.md)
- HANA-säkerhetskopiering baserad på ögonblicksbilder av lagring med azure-lagringsblobbögonblicksbild manuellt eller Azure Backup-tjänst


SAP HANA erbjuder ett api för säkerhetskopiering, som gör att säkerhetskopieringsverktyg från tredje part kan integreras direkt med SAP HANA. Produkter som Azure Backup-tjänst eller [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) använder det här proprietära gränssnittet för att utlösa SAP HANA-databas eller göra om loggsäkerhetskopior. 


Information om hur du kan hitta vad SAP-programvara som stöds på Azure finns i artikeln [Vad SAP-programvara stöds för Azure-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure).

## <a name="azure-backup-service"></a>Azure-säkerhetskopieringstjänst

Det första scenariot som visas är ett scenario där `backint` Azure Backup Service antingen använder SAP HANA-gränssnittet för att utföra en direktuppspelningskopia med från en SAP HANA-databas. Eller så använder du en mer allmän funktion i Azure Backup-tjänsten för att skapa en konsekvent diskögonblicksbild för program och få den överförd till Azure Backup-tjänsten.

Azure Backup integreras och är certifierat som säkerhetskopieringslösning för SAP HANA med hjälp av det egenutvecklade SAP HANA-gränssnittet som kallas [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Mer information om lösningen, dess funktioner och De Azure-regioner där den är tillgänglig finns i artikeln [Supportmatris för säkerhetskopiering av SAP HANA-databaser på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). Mer information och principer om Azure Backup-tjänst för HANA finns i artikeln [Om SAP HANA-databassäkerhetskopior i Virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/sap-hana-db-about). 

Den andra möjligheten att utnyttja Azure Backup-tjänsten är att skapa en konsekvent säkerhetskopiering av ett program med hjälp av diskögonblicksbilder av Azure Premium Storage. Andra HANA-certifierade Azure-lagringar, som [Azure Ultra-disk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd) och [Azure NetApp-filer](https://azure.microsoft.com/services/netapp/) stöder inte den här typen av ögonblicksbild via Azure Backup-tjänsten. Läsa dessa artiklar:

- [Planera din infrastruktur för VM-säkerhetskopiering i Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Programkonsekvent säkerhetskopiering av virtuella Azure Linux-datorer](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

denna sekvens av aktivitet framträder:

- Azure Backup måste köra ett skript före ögonblicksbild som placerar programmet, i det här fallet SAP HANA, i ett konsekvent tillstånd
- När det här konsekventa tillståndet bekräftas kör Azure Backup diskögonblicksbilderna
- När ögonblicksbilderna har avslutats ångrar Azure Backup den aktivitet som den gjorde i skriptet före ögonblicksbilden
- Efter en lyckad körning kommer Azure Backup att strömma data till säkerhetskopieringsvalvet

När det gäller SAP HANA använder de flesta kunder Azure Write Accelerator för de volymer som innehåller SAP HANA-omloggen. Azure Backup-tjänsten utesluter automatiskt dessa volymer från ögonblicksbilderna. Detta undantag skadar inte HANA:s förmåga att återställa. Även om det skulle blockera möjligheten att återställa med nästan alla andra SAP stöds DBMS.

Nackdelen med denna möjlighet är det faktum att du behöver utveckla din egen pre- och post-snapshot script. Skriptet före ögonblicksbild måste skapa en HANA-ögonblicksbild och hantera eventuella undantagsfall. Medan skriptet efter ögonblicksbilden måste ta bort HANA-ögonblicksbilden igen. Om du vill ha mer information om den logik som krävs börjar du med [SAP-supportanteckning #2039883](https://launchpad.support.sap.com/#/notes/2039883). Övervägandena i avsnittet "SAP HANA-datakonsekvens när du tar ögonblicksbilder av lagring" i den här artikeln gäller helt för den här typen av säkerhetskopiering.

> [!NOTE]
> Diskögonblicksbaserade säkerhetskopior för SAP HANA i distributioner där flera databasbehållare används kräver en minsta utgåva av HANA 2.0 SP04
> 

Se information om ögonblicksbilder av lagring senare i det här dokumentet.

![Den här bilden visar två möjligheter att spara det aktuella VM-tillståndet](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Andra HANA-säkerhetskopieringsmetoder
Det finns tre andra säkerhetskopieringsmetoder eller sökvägar som kan beaktas:

- Säkerhetskopiera mot en NFS-resurs som baseras på Azure NetApp Files (ANF). ANF har återigen möjlighet att skapa ögonblicksbilder av de volymer som du lagrar säkerhetskopior på. Med tanke på det dataflöde som du så småningom behöver för att skriva säkerhetskopior, kan den här lösningen bli en dyr metod. Även om det är lätt att fastställa eftersom HANA kan skriva säkerhetskopior direkt i Azure native NFS-resursen
- Köra HANA-säkerhetskopieringen mot vm-anslutna diskar med Standard SSD eller Azure Premium Storage. Som nästa steg kan du kopiera dessa säkerhetskopior mot Azure Blob-lagring. Denna strategi kan vara prismässigt attraktiv
- Köra HANA-säkerhetskopieringen mot vm-anslutna diskar med Standard SSD eller Azure Premium Storage. Som nästa steg disken blir snapshotted regelbundet. Efter den första ögonblicksbilden kan inkrementella ögonblicksbilder användas för att minska kostnaderna

![Den här bilden visar alternativ för att ta en SAP HANA-filsäkerhetskopiering inuti den virtuella datorn](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Den här bilden visar alternativ för att ta en SAP HANA-filsäkerhetskopiering inuti den virtuella datorn och sedan lagra den för HANA-säkerhetskopieringsfiler någon annanstans med hjälp av olika verktyg. Alla lösningar som inte involverar en säkerhetskopieringstjänst från tredje part eller Azure Backup-tjänst har dock flera hinder gemensamt. Vissa av dem kan listas, som kvarhållningsadministration, automatisk återställningsprocess och automatisk återställning i tid som Azure Backup-tjänst eller andra specialiserade säkerhetskopieringspaket och tjänster från tredje part tillhandahåller. Många av dessa tjänster från tredje part kan köras på Azure. 


## <a name="sap-resources-for-hana-backup"></a>SAP-resurser för HANA-säkerhetskopiering

### <a name="sap-hana-backup-documentation"></a>SAP HANA-säkerhetskopieringsdokumentation

- [Introduktion till SAP HANA Administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planera din strategi för säkerhetskopiering och återställning](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Schema hana backup med ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Schemalägga säkerhetskopieringar av data (SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Vanliga frågor om SAP HANA-säkerhetskopiering i [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Vanliga frågor och svar om SAP HANA-databas och ögonblicksbilder av lagring i [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Olämpliga nätverksfilsystem för säkerhetskopiering och återställning i [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Så här verifierar du riktigheten av SAP HANA-säkerhetskopiering
Oberoende av din säkerhetskopieringsmetod är det absolut nödvändigt att köra en teståterställning mot ett annat system. Den här metoden är ett sätt att se till att en säkerhetskopia är korrekt och interna processer för säkerhetskopiering och återställning fungerar som förväntat. Även återställa säkerhetskopior kan vara ett hinder lokalt på grund av dess infrastrukturkrav, är det mycket lättare att utföra i molnet genom att tillhandahålla nödvändiga resurser tillfälligt för detta ändamål. Det är korrekt att det finns verktyg medföljer HANA som kan kontrollera säkerhetskopieringsfiler på möjligheten att återställa. Syftet med frekventa återställningsövningar är dock att testa processen för en databasåterställning och träna den processen med driftpersonalen.

Tänk på att det inte räcker att göra en enkel återställning och kontroll av om HANA är igång. Du bör köra en tabell konsekvenskontroll för att vara säker på att den återställda databasen är bra. SAP HANA erbjuder flera typer av konsekvenskontroller som beskrivs i [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Information om tabellen konsekvenskontroll kan också hittas på SAP webbplats vid [tabell och katalog konsekvenskontroller](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Fördelar och nackdelar med HANA backup kontra lagring ögonblicksbild

SAP&#39;inte ge företräde åt antingen HANA backup kontra lagring ögonblicksbild. Den listar deras för-och nackdelar, så man kan avgöra vilken man ska använda beroende på situationen och tillgänglig lagringsteknik (se [Planera din backup och återhämtningsstrategi).](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)

På Azure bör du vara medveten om att azure blob snapshot-funktionen inte&#39;inte ger filsystemkonsekvens på flera diskar (se [Använda blob-ögonblicksbilder med PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). 

Dessutom måste man förstå faktureringskonsekvenserna när man ofta arbetar med blob-ögonblicksbilder som beskrivs i den här artikeln: [Förstå hur ögonblicksbilder samlar avgifter](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– det är&#39;inte lika självklart som att använda virtuella Azure-diskar.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA-datakonsekvens när du tar ögonblicksbilder av lagring

Som dokumenterats tidigare är det obligatoriskt att beskriva funktionerna för säkerhetskopiering av ögonblicksbilder i Azure Backup, filsystem och programkonsekvens när du tar ögonblicksbilder av lagring. Det enklaste sättet att undvika problem skulle vara att stänga av SAP HANA, eller kanske hela den virtuella datorn. Något som inte är genomförbart för en produktionsinstans.

> [!NOTE]
> Diskögonblicksbaserade säkerhetskopior för SAP HANA i distributioner där flera databasbehållare används kräver en minsta utgåva av HANA 2.0 SP04
> 

Azure storage, ger inte filsystemkonsekvens över flera diskar eller volymer som är kopplade till en virtuell dator under ögonblicksbildprocessen. Det innebär att programmet konsekvens under ögonblicksbilden måste levereras av programmet, i detta fall SAP HANA själv. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) har viktig information om SAP HANA-säkerhetskopior av ögonblicksbilder av lagring. Till exempel, med XFS filsystem, är det nödvändigt att köra **xfs\_frysa** innan du startar en ögonblicksbild av lagring för att ge ansökan konsekvens (se [xfs\_freeze(8) - Linux man sida](https://linux.die.net/man/8/xfs_freeze) för information om **xfs\_frysa**).

Förutsatt att det finns ett XFS-filsystem som spänner över fyra virtuella Azure-diskar, ger följande steg en konsekvent ögonblicksbild som representerar HANA-dataområdet:

1. Skapa förbereda hana-data
1. Låsa filsystemen för alla diskar/volymer (använd till exempel **xfs\_freeze**)
1. Skapa alla nödvändiga blob-ögonblicksbilder på Azure
1. Låsa upp filsystemet
1. Bekräfta ögonblicksbilden av HANA-data (tar bort ögonblicksbilden)

När du använder Azure Backups kapacitet att utföra konsekventa ögonblicksbildsäkerhetskopior måste steg #1 kodas/skriptas av dig för skriptet före ögonblicksbilden. Azure Backup-tjänsten kör steg #2 och #3. Steg #4 och #5 måste återigen tillhandahållas av koden i skriptet efter ögonblicksbilden. Om du inte använder Azure backup-tjänst måste du också koda/skriptsteg #2 och #3 på egen hand.
Mer information om hur du skapar HANA-dataögonblicksbilder finns i följande artiklar:

- [HANA-dataögonblicksbilder] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Mer information om hur du utför steg #1 finns i artikeln [Skapa en dataögonblicksbild (Inbyggd SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Information om hur du bekräftar/tar bort HANA-dataögonblicksbilder som behov i steg #5 finns i artikeln [Skapa en dataögonblicksbild (Inbyggd SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

Det är viktigt att bekräfta HANA-ögonblicksbilden. På grund &quot;av Copy-on-Write kanske&quot; SAP HANA inte behöver ytterligare diskutrymme i det här läget för att förbereda ögonblicksbilder. Det&#39;inte heller möjligt att starta nya säkerhetskopior förrän SAP HANA-ögonblicksbilden har bekräftats.


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA strategi för schemaläggning av säkerhetskopiering

I SAP HANA-artikeln [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) anges en grundläggande plan för säkerhetskopiering. Förlita dig på SAP-dokumentation kring HANA och dina erfarenheter med andra DBMS för att definiera strategi för säkerhetskopiering/återställning för SAP HANA. Sekvensen av olika typer av säkerhetskopior och kvarhållningsperioden är mycket beroende av de SLA:er du behöver tillhandahålla.


### <a name="sap-hana-backup-encryption"></a>SAP HANA-kryptering för säkerhetskopiering

SAP HANA erbjuder kryptering av data och logg. Om SAP HANA-data och logg inte är krypterade krypteras inte säkerhetskopiorna som standard. SAP HANA erbjuder dock en separat säkerhetskopieringskryptering som dokumenteras i [SAP HANA Backup Encryption](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Om du kör äldre versioner av SAP HANA kan du behöva kontrollera om säkerhetskopieringskryptering redan var en del av de funktioner som redan finns.  


## <a name="next-steps"></a>Nästa steg
* [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) beskriver det filbaserade säkerhetskopieringsalternativet.
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveriberedskap på Azure](hana-overview-high-availability-disaster-recovery.md).
