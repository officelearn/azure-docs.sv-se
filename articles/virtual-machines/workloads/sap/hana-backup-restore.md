---
title: HANA säkerhetskopiering och återställning för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: HANA säkerhetskopiera och återställa på SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21232e5a678d6deed920e57cd0433a3b85ca4fdc
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987903"
---
# <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

>[!IMPORTANT]
>Den här artikeln är inte en ersättning för dokumentationen för administration av SAP HANA eller SAP Notes. Vi räknar med att du har en djupare förståelse av och expertis i SAP HANA-administration och åtgärder, särskilt för säkerhetskopiering, återställning, hög tillgänglighet och katastrofåterställning. I den här artikeln visas skärmbilder från SAP HANA-Studio. Innehåll, struktur och arten av de olika stegen i SAP-verktyg för fjärrserveradministration och verktyg själva ändras från SAP HANA version till version.

Det är viktigt att du arbeta med steg och processer som tas i miljön och med dina HANA-versioner och versioner. Vissa processer som beskrivs i den här artikeln är förenklad för en bättre förståelse. De är inte avsedd att användas som detaljerade anvisningar för slutlig åtgärden handböcker. Om du vill skapa åtgärden handböcker för dina konfigurationer, testa och arbeta med dina processer och dokumentera de processer som rör dina specifika konfigurationer. 

En av de viktigaste aspekterna av operativa databaserna är att skydda dem mot katastrofhändelser. Orsaken till de här händelserna kan vara allt från enkla användarfel vid naturkatastrofer.

Säkerhetskopiera en databas, med möjlighet att återställa den till valfri punkt i tid, t.ex. innan någon borttagna kritiska data, aktiverar återställning i ett tillstånd som är så nära som möjligt sätt som den var innan avbrott.

Två typer av säkerhetskopior måste utföras för att uppnå återställning:

- Säkerhetskopior av databasen: Fullständiga, inkrementella eller differentiella säkerhetskopieringar
- Säkerhetskopieringar av transaktionsloggen

Förutom fullständig--databassäkerhetskopieringar som utförs på en nivå för programmet, kan du utföra säkerhetskopiering med ögonblicksbilder av lagring. Ögonblicksbilder av lagring ersätta inte säkerhetskopieringar av transaktionsloggen. Säkerhetskopieringar av transaktionsloggen är viktigt att återställa databasen till en viss punkt eller till tomt loggar från redan genomförda transaktioner. Storage-ögonblicksbilder kan snabbare återställning genom att snabbt tillhandahålla en sammanslagning och tydlig bild av databasen. 

SAP HANA på Azure (stora instanser) erbjuder två alternativ för säkerhetskopiering och återställning:

- **Göra det själv (DIY).** När du se till att det finns tillräckligt med ledigt diskutrymme, utföra fullständig databasen och loggsäkerhetskopior med någon av följande metoder för disk-säkerhetskopiering. Du kan säkerhetskopiera antingen direkt till volymer som är kopplad till stora HANA-instansen enheter eller till NFS-resurser som har ställts in på Azure-datorer (VM). Koppla Azure Storage till den virtuella datorn i det senare fallet kan kunder konfigurera en Linux-VM i Azure, och dela lagringsutrymme via en NFS-server som är konfigurerade i den virtuella datorn. Kopiera säkerhetskopior av till ett Azure storage-konto om du säkerhetskopierar mot volymer som kopplas direkt till stora HANA-instansen enheter. Gör detta när du har konfigurerat en Azure-dator som exporterar NFS-resurser som är baserade på Azure Storage. Du kan också använda en Azure Backup-valv eller Azure kall lagring. 

   Ett annat alternativ är att använda ett verktyg för tredje parts data protection säkerhetskopiorna för när de kopieras till ett Azure storage-konto. Gör det själv säkerhetskopieringsalternativ också vara nödvändigt för data som du behöver lagra under längre tidsperioder för efterlevnad och granskning. I samtliga fall kopieras säkerhetskopiorna till NFS-resurser via en virtuell dator och Azure Storage som representeras.

- **Infrastruktur för säkerhetskopiering och återställningsfunktionen.** Du kan också säkerhetskopiera och återställa kärnfunktioner där den underliggande infrastrukturen med SAP HANA på Azure (stora instanser). Det här alternativet uppfyller behovet av säkerhetskopieringar och snabb återställning. Resten av det här avsnittet adresser säkerhetskopiering och återställning av funktionerna som erbjuds med stora HANA-instanser. Det här avsnittet beskriver också relationen som säkerhetskopiering och återställning måste disaster recovery-funktionerna som erbjuds av stora HANA-instanser.

> [!NOTE]
>   Den ögonblicksbild-teknik som används av den underliggande infrastrukturen med stora HANA-instanser har ett beroende på SAP HANA-ögonblicksbilder. SAP HANA-ögonblicksbilder fungerar då inte tillsammans med flera innehavare av behållare för SAP HANA-databas för flera innehavare. Om bara en klient har distribuerats, SAP HANA-ögonblicksbilder fungerar och du kan använda den här metoden.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Använda ögonblicksbilder av lagring av SAP HANA på Azure (stora instanser)

Lagringsinfrastruktur som underliggande SAP HANA på Azure (stora instanser) har stöd för storage-ögonblicksbilder av volymer. Både säkerhetskopierings- och återställa volymer stöds, med följande överväganden:

- I stället för fullständiga databassäkerhetskopieringar tas storage-ögonblicksbilder regelbundet.
- När en ögonblicksbild utlöses via /hana/data och /hana/shared, vilket innefattar /usr/sap, volymer, teknik för ögonblicksbilder initierar en SAP HANA ögonblicksbild innan körs den storage-ögonblicksbilder. SAP HANA-ögonblicksbilder är den installationen för slutlig log återställningar efter återställning av storage-ögonblicksbilder. För en HANA-ögonblicksbilder ska lyckas måste en aktiv HANA-instans. I ett scenario för HSR stöds inte en storage-ögonblicksbilder på en aktuella sekundär nod där en HANA-ögonblicksbilder inte kan utföras.
- När storage ögonblicksbilden har körts, tas SAP HANA-ögonblicksbilder bort.
- Säkerhetskopieringar av transaktionsloggen tas ofta och lagras i /hana/logbackups volymen eller i Azure. Du kan utlösa /hana/logbackups volymen som innehåller säkerhetskopieringarna av transaktionsloggen för att ta en ögonblicksbild separat. I så fall behöver du inte kör en HANA-ögonblicksbilder.
- Om du måste återställa en databas till en viss punkt i tiden för ett avbrott på produktion, begär den Microsoft Azure-supporten eller SAP HANA på Azure återställning till en viss storage-ögonblicksbild. Ett exempel är en planerad återställning av en sandbox-system till det ursprungliga tillståndet.
- SAP HANA-ögonblicksbilder som ingår i storage-ögonblicksbilder är en förskjutning för att tillämpa säkerhetskopieringar av transaktionsloggen som kördes och sparades när lagring ögonblicksbilden togs.
- Dessa säkerhetskopieringar av transaktionsloggen kommer att återställa databasen till en viss punkt i tiden.

Du kan utföra ögonblicksbilder av lagring som är riktade till tre klasser av volymer:

- En kombinerad ögonblicksbild över/hana/data samt/hana/delade, vilket innefattar /usr/sap. Den här ögonblicksbilden kräver skapandet av en SAP HANA-ögonblicksbilder som förberedelse för storage-ögonblicksbilder. SAP HANA-ögonblicksbilder säkerställer att databasen är i ett konsekvent tillstånd från en storage synsätt. För återställningsprocessen, som är en ställa in på.
- En separat ögonblicksbild över/hana/logbackups.
- En partition av operativsystemet.

För att få de senaste ögonblicksbild skript och dokumentation, se [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). När du hämtar ögonblicksbild skriptpaket från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), du får tre filer. En av filerna dokumenteras i en PDF-fil för funktionerna. När du har hämtat verktygsuppsättningen, följer du anvisningarna i ”hämta i Verktyg för ögonblicksbild”.

## <a name="storage-snapshot-considerations"></a>Överväganden för ögonblicksbilder av lagring

>[!NOTE]
>Storage-ögonblicksbilder använda lagringsutrymme som allokerats till stora HANA-instansen-enheter. Överväg följande aspekter för schemaläggning av ögonblicksbilder av lagring och hur många storage-ögonblicksbilder som ska lagras. 

Specifika säkerhetsnivån för storage-ögonblicksbilder för SAP HANA på Azure (stora instanser) är:

- En ögonblicksbild av specifika lagringsutrymmen vid tidpunkten när den tas förbrukar lite lagringsutrymme.
- Ögonblicksbilden behöver lagra det ursprungliga innehållet för block och dataändringarna som innehållsändringar för data och innehållet i SAP HANA-data filer ändras på lagringsvolymen.
- Därför ökar storage-ögonblicksbilder i storlek. Ju längre ögonblicksbilden finns, desto större blir storage-ögonblicksbilder.
- Flera ändringar som görs till SAP HANA-databasvolymen över livslängden för en storage-ögonblicksbilder, den större utrymme för storage-ögonblicksbilder.

SAP HANA på Azure (stora instanser) levereras med fast Volymstorlekar för data och loggvolymer för SAP HANA. Utför ögonblicksbilder av volymerna eats i utrymmet på volymen. Du måste:

- Avgöra när du schemalägger ögonblicksbilder av lagring.
- Övervaka förbrukningen av diskutrymme av lagringsvolymer. 
- Hantera antalet ögonblicksbilder som du lagrar. 

Du kan inaktivera storage-ögonblicksbilder när du importerar stora mängder data eller utföra andra viktiga ändringar till HANA-databas. 


I följande avsnitt innehåller information för att utföra de här ögonblicksbilderna och omfattar allmänna rekommendationer:

- Även om maskinvaran som kan klara 255 ögonblicksbilder per volym, som du vill hålla betydligt lägre än det här talet. Rekommendationen är 250 eller mindre.
- Innan du utför ögonblicksbilder av lagring, övervaka och hålla reda på ledigt utrymme.
- Minska antalet ögonblicksbilder av lagring baserat på ledigt utrymme. Du kan sänka antal ögonblicksbilder som du behåller eller du kan utöka volymer. Du kan beställa ytterligare lagringsutrymme i enheter om 1 TB.
- Inaktivera ögonblicksbilder av lagring på /hana/data volymen under aktiviteter, till exempel data flyttas till SAP HANA med Migreringsverktyg för SAP-plattform (R3load) eller återställning av SAP HANA-databaser från säkerhetskopior. 
- Undvik ögonblicksbilder av lagring om möjligt under större omorganisering av SAP HANA-tabeller.
- Ögonblicksbilder av lagring är en förutsättning för att dra nytta av katastrofen återställningsfunktioner för SAP HANA på Azure (stora instanser).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Krav för att använda självbetjäning storage-ögonblicksbilder

För att säkerställa att ögonblicksbild skriptet har körts, se till att Perl är installerad på Linux-operativsystem på stora HANA-instanser-servern. Perl är förinstallerat på din enhet för stora HANA-instansen. Om du vill kontrollera vilken version av Perl, använder du följande kommando:

`perl -v`

![Den offentliga nyckeln kopieras genom att köra det här kommandot](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Ställ in ögonblicksbilder av lagring

Följ dessa steg om du vill konfigurera ögonblicksbilder av lagring med stora HANA-instanser.
1. Se till att Perl är installerad på Linux-operativsystem på stora HANA-instanser-servern.
1. Ändra/etc/ssh/ssh\_config att lägga till raden _Mac-datorer hmac-sha1_.
1. Skapa ett konto för säkerhetskopiering av SAP HANA på huvudnoden för varje SAP HANA-instans som du kör, om tillämpligt.
1. Installera SAP HANA HDB-klienten på alla servrar som stora SAP HANA-instanser.
1. Skapa en offentlig nyckel för att komma åt det underliggande lagringsinfrastruktur som styr ögonblicksbilder skapas på den första stora instanser av SAP HANA-servern för varje region.
1. Kopiera skript och konfigurationsfilen från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) till platsen för **hdbsql** i SAP HANA-installationen.
1. Ändra den *HANABackupDetails.txt* fil som krävs för lämplig kund-specifikationer.

Få de senaste ögonblicksbild skript och dokumentation från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). De steg som angavs ovan, se [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

### <a name="consideration-for-mcod-scenarios"></a>Överväganden för MCOD scenarier
Om du kör en [MCOD scenariot](https://launchpad.support.sap.com/#/notes/1681092) med flera SAP HANA-instanser på en enhet för stora HANA-instansen, har du separata lagringsvolymer som tillhandahållits för var och en av SAP HANA-instanser. Mer information om MDC och andra överväganden finns i ”viktiga saker att komma ihåg” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Steg 1: Installera SAP HANA HDB-klienten

Linux-operativsystem som är installerade på SAP HANA på Azure (stora instanser) innehåller mappar och skript som krävs för att köra SAP HANA storage-ögonblicksbilder för säkerhetskopiering och katastrofåterställning återställningssyfte. Sök efter nyare versioner i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Den senaste versionen av skripten är 4.0. Olika skript kan ha olika utgåvor av mindre inom samma huvudversion.

Det är ditt ansvar att installera klienten HDB för SAP HANA på stora HANA-instansen enheter medan du installerar SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Steg 2: Ändra/etc/ssh/ssh\_config

Det här steget beskrivs i ”Aktivera kommunikation med lagringsutrymmen” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-3-create-a-public-key"></a>Steg 3: Skapa en offentlig nyckel

Upprätta en procedur inloggning via en offentlig nyckel för att ge åtkomst till lagring ögonblicksbild gränssnitt klientorganisationens stora HANA-instansen. 

På den första SAP HANA på Azure (stora instanser)-server i din klient, skapa en offentlig nyckel för att komma åt lagringsinfrastrukturen. Med en offentlig nyckel är inte ett lösenord krävs för att logga in på gränssnitt som storage ögonblicksbild. Du behöver också att upprätthålla lösenordsinformation med en offentlig nyckel. 

Om du vill generera en offentlig nyckel finns i ”Aktivera kommunikation med lagringsutrymmen” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-4-create-an-sap-hana-user-account"></a>Steg 4: Skapa ett användarkonto för SAP HANA

Skapa ett användarkonto i SAP HANA som storage ögonblicksbild skript kan använda för att starta skapandet av SAP HANA-ögonblicksbilder. Skapa ett användarkonto för SAP HANA SAP HANA-Studio för detta ändamål. Användaren måste skapas under SYSTEMDB och *inte* under SID-databas för MDC. I miljön för enskild behållare skapas användaren i klientdatabasen. Det här kontot måste ha **säkerhetskopiering Admin** och **Catalog Läs** privilegier. 

Om du vill konfigurera och använda ett användarkonto, finns i ”Aktivera kommunikation med SAP HANA” i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Steg 5: Auktorisera användarkontot för SAP HANA

I det här steget ska godkänna du SAP HANA-användarkonto som du skapade så att skript som inte behöver att skicka lösenord vid körning. SAP HANA-kommandot `hdbuserstore` gör det möjligt att skapa en nyckel för SAP HANA-användare. Nyckeln lagras på en eller flera SAP HANA-noder. Användarnyckel kan användare åtkomst till SAP HANA utan att behöva hantera lösenord från inom scripting processen. Scripting processen beskrivs senare i den här artikeln.

>[!IMPORTANT]
>Kör dessa med samma användarkontext som ögonblicksbild-kommandon körs i. I annat fall fungerar kommandona ögonblicksbild inte korrekt.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Steg 6: Hämta ögonblicksbild-skript, konfigurera ögonblicksbilderna och testa konfiguration och anslutning

Hämta den senaste versionen av skript från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Hur skripten installeras ändras från och med version 4.0 av skript. Mer information finns i ”Aktivera kommunikation med SAP HANA” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

De exakta i kommandon finns i ”enkel installation av verktyg för ögonblicksbild (standard)” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Vi rekommenderar användning av standardinstallationen. 

Uppgradera från version 3.x till 4.0, finns i ”uppgradera en befintlig installation” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Om du vill avinstallera 4.0 verktygsuppsättning, finns i ”avinstallation av verktygen ögonblicksbild” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Glöm inte att köra stegen som beskrivs i ”slutföra installationen av verktyg för ögonblicksbild” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Syftet med olika skript och filer beskrivs som de har installerats i ”vad är verktygen ögonblicksbild”? i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Innan du konfigurerar ögonblicksbild-verktyg, se till att du också konfigurerad HANA platser och inställningar på rätt sätt. Mer information finns i ”SAP HANA-konfiguration” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Konfigurationen av verktyget ögonblicksbilduppsättning beskrivs i ”konfigurationsfilen - HANABackupCustomerDetails.txt” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-connectivity-with-sap-hana"></a>Testa anslutningen till SAP HANA

När du placerar alla konfigurationsdata i den *HANABackupCustomerDetails.txt* filen, kontrollera om konfigurationerna som är korrekta för HANA instansdata. Använd skript för `testHANAConnection`, som är oberoende av en konfiguration för SAP HANA skala upp eller skala ut.

Mer information finns i ”Kontrollera anslutningen med SAP HANA - testHANAConnection” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-storage-connectivity"></a>Testa lagringsanslutningen för

I nästa steg är att kontrollera anslutningen till lagring baserat på data som du placerar i den *HANABackupCustomerDetails.txt* konfigurationsfilen. Kör sedan en test-ögonblicksbild. Innan du kör den `azure_hana_backup` kommandot, måste du köra det här testet. Sekvens med kommandon för det här testet, finns i ”Kontrollera anslutningen med storage - testStorageSnapshotConnection” ”i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Efter en lyckad inloggning för virtuell dator gränssnitten för lagring, skriptet fortsätter med fas 2 och skapar en test-ögonblicksbild. Utdata visas här en konfiguration med tre noder skala ut SAP Hana.

Om testet ögonblicksbilden har körts med skriptet kan schemalägga du faktiska storage-ögonblicksbilder. Om det inte lyckas, kan du undersöka problem innan du fortsätter. Test-ögonblicksbild ska vara runt tills de första verkliga ögonblicksbilderna är klar.


### <a name="step-7-perform-snapshots"></a>Steg 7: Utföra ögonblicksbilder

När steg för förberedelse är klar kan du börja konfigurera och schemalägga faktiska storage-ögonblicksbilder. Skriptet som ska schemaläggas fungerar med SAP HANA skala upp och skala ut konfigurationer. Schemalägga skriptet med hjälp av verktyget cron för periodiska och regelbundna körningen av skriptet säkerhetskopieringen. 

Den exakta kommandosyntax och funktioner finns i ”utför ögonblicksbild backup - azure_hana_backup” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

När skriptet `azure_hana_backup` körs skapar den lagring som ögonblicksbild i följande tre faser:

1. Den körs en SAP HANA-ögonblicksbilder.
1. Storage-ögonblicksbilder körs.
1. SAP HANA-ögonblicksbilden som skapades innan du körde storage-ögonblicksbilder tas bort.

Kör skriptet genom att anropa det från HDB körbara mappen som de kopierades. 

Kvarhållningsperioden administreras med antalet ögonblicksbilder som skickas som en parameter när du kör skriptet. Hur lång tid som täcks av storage-ögonblicksbilder är en funktion av körning och antalet ögonblicksbilder som skickas som en parameter när skriptet har körts. 

Om antalet ögonblicksbilder som hålls överstiger det antal som namnges som en parameter i anropet av skriptet, raderas den äldsta storage-ögonblicksbilden med samma etikett innan en ny ögonblicksbild körs. Det tal som du ger eftersom den sista parametern för anropet är det nummer du kan använda för att styra antalet ögonblicksbilder sparas. Med det här talet kan du också styra, indirekt, hur mycket diskutrymme som används för ögonblicksbilder. 


## <a name="snapshot-strategies"></a>Strategier för ögonblicksbild
Frekvensen för ögonblicksbilder för de olika typerna beror på om du använder stora HANA-instansen disaster recovery-funktionen. Den här funktionen är beroende av ögonblicksbilder av lagring, som kan kräva särskilda rekommendationer för frekvens och körningen perioder av storage-ögonblicksbilder. 

Överväganden och rekommendationer som följer, antas att du gör *inte* använda disaster recovery-funktionen som stora HANA-instanser erbjuder. I stället använder storage-ögonblicksbilder för att ha säkerhetskopior och att kunna ge point-in-time-återställning för de senaste 30 dagarna. Med begränsningar av antalet ögonblicksbilder och utrymme kan beakta följande krav:

- Återställningstid för point-in-time-återställning.
- Hur mycket diskutrymme.
- Återställningspunkt och målen för potentiella återställning efter en katastrof.
- Slutlig för körning av HANA-full-databassäkerhetskopieringar mot diskar. När en fullständig databassäkerhetskopia mot diskar eller **backint** gränssnittet utförs, körning av storage-ögonblicksbilder misslyckas. Om du planerar att köra fullständiga databassäkerhetskopieringar ovanpå ögonblicksbilder av lagring, se till att körningen av storage-ögonblicksbilder inaktiveras under den här tiden.
- Antalet ögonblicksbilder per volym, vilket är begränsad till 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Om du inte använder disaster recovery-funktionen för HANA stora instanser, är ögonblicksbild perioden mer sällan. I sådana fall kan du utföra kombinerade ögonblicksbilder på /hana/data och /hana/shared som innehåller /usr/sap, i 12 timmar eller 24-timmarsformat perioder. Behåll ögonblicksbilder för en månad. Detsamma gäller för ögonblicksbilder av säkerhetskopiering loggvolymen. Körningen av SAP HANA säkerhetskopieringar av transaktionsloggen mot loggvolymen säkerhetskopiering sker i 5 minuter för 15 minuters perioder.

Schemalagda lagringsögonblicksbilder utförs bäst med hjälp av cron. Använda samma skript för alla säkerhetskopieringar samt katastrofåterställningskrav. Ändra skriptet indata så att de matchar de olika begärt säkerhetskopieringstider. De här ögonblicksbilderna schemaläggs alla på olika sätt i cron beroende på sin Utförandetid. Det kan vara per timme, varje 12 timmar varje dag eller varje vecka. 

I följande exempel visar ett cron-schema i /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
I exemplet ovan innehåller en timvis kombinerade ögonblicksbild volymerna som innehåller /hana/data och /hana/shared/SID, vilket innefattar /usr/sap platser. Använd den här typen av ögonblicksbild för en snabbare point-in-time-återställning under de senaste två dagarna. Det finns också en daglig ögonblicksbild på dessa volymer. Därför har två dagars täckning som per timme ögonblicksbilder plus fyra veckor täckningen av dagliga ögonblicksbilder. Säkerhetskopiering transaktionsvolymer log också säkerhetskopieras varje dag. Dessa säkerhetskopior behålls i fyra veckor. 

Som du ser i den tredje adressraden crontab är säkerhetskopia av transaktionsloggen HANA schemalagd att köras var femte minut. Starttider för olika cron-jobb som kör lagringsögonblicksbilder ut. På så sätt kan fungerar ögonblicksbilderna inte på samma gång vid en viss punkt i tiden. 

I följande exempel kan du utföra en kombinerad ögonblicksbild som omfattar volymerna som innehåller de /hana/data och /hana/shared/SID, vilket innefattar /usr/sap, platser per timme. Du kan behålla de här ögonblicksbilderna i två dagar. Ögonblicksbilder av säkerhetskopiering transaktionsvolymer log kör på basis av 5 minuter och hålls i fyra timmar. Som är tidigare, säkerhetskopiering av HANA transaktionsloggfilen schemalagd att köras var femte minut. 

Ögonblicksbild av transaktionen-loggvolymen säkerhetskopiering utförs med en 2-minuters fördröjning när säkerhetskopian av transaktionsloggen har startats. Under normala omständigheter loggsäkerhetskopiering för SAP HANA-transaktionen har slutförts inom dessa två minuter. Som tidigare, volymen som innehåller startavbildningen LUN backas upp en gång per dag av storage-ögonblicksbilder och sparas i fyra veckor.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Följande bild illustrerar sekvenser av föregående exempel. Starta om datorn LUN är exkluderad.

![Förhållandet mellan säkerhetskopieringar och ögonblicksbilder](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA utför regelbundna skrivningar till /hana/log volymen för att dokumentera allokerade ändringar till databasen. Med jämna mellanrum skriver en lagringspunkt i SAP HANA till /hana/data volymen. Som anges i crontab kör en säkerhetskopiering av SAP HANA-transaktionsloggen var femte minut. 

Du kan också se att en SAP HANA-ögonblicksbilder körs varje timme till följd av utlöser en kombinerad lagring ögonblicksbild över /hana/data och /hana/shared/SID volymer. När ögonblicksbilden HANA lyckas, ögonblicksbilder kombinerade lagring körs. Som finns beskrivet i crontab, körs storage-ögonblicksbilder på volymen /hana/logbackup var 5 minuter, cirka 2 minuter efter säkerhetskopian av transaktionsloggen HANA.

> 

>[!IMPORTANT]
> Användningen av ögonblicksbilder av lagring för säkerhetskopior av SAP HANA är värdefullt endast när ögonblicksbilder utförs tillsammans med SAP HANA säkerhetskopieringar av transaktionsloggen. Dessa säkerhetskopieringar av transaktionsloggen måste omfatta tidsperioder mellan storage-ögonblicksbilder. 

Om du har angett ett åtagande för användare av en point-in-time-återställning på 30 dagar, måste du:

- Åtkomst till en ögonblicksbild av kombinerade lagring via /hana/data och /hana/shared/SID som är 30 dagar gamla i extrema fall. 
- Har säkerhetskopior av sammanhängande transaktionsloggen som omfattar tiden mellan alla kombinerade storage-ögonblicksbilder. Därför måste äldsta ögonblicksbilden av säkerhetskopiering transaktionsvolymer log vara 30 dagar gamla. Detta inte är fallet om du kopierar säkerhetskopieringarna av transaktionsloggen till en annan NFS-resurs som finns på Azure Storage. I så fall kan du hämta gamla säkerhetskopieringar av transaktionsloggen från den NFS-resursen.

Om du vill dra nytta av ögonblicksbilder av lagring och eventuell storage-replikering av säkerhetskopieringar av transaktionsloggen, ändra platsen till vilken SAP HANA skriver säkerhetskopieringarna av transaktionsloggen. Du kan göra den här ändringen i HANA Studio. 

Även om SAP HANA säkerhetskopierar automatiskt fullständig log segment, ange ett intervall för log-säkerhetskopiering för att vara entydig. Detta gäller särskilt när du använder alternativet disaster recovery eftersom du vanligtvis vill köra säkerhetskopieringar med en deterministisk punkt. I följande fall har 15 minuter angetts som log säkerhetskopieringsintervallet.

![Schemalägg säkerhetskopiering för SAP HANA-loggarna i SAP HANA-Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Du kan också välja säkerhetskopieringar som är kortare än 15 minuter. En mer frekventa inställning används ofta tillsammans med funktioner för haveriberedskap för stora HANA-instanser. Vissa kunder utföra säkerhetskopieringar av transaktionsloggen var femte minut.

Om databasen har aldrig säkerhetskopierats, är det sista steget att utföra en säkerhetskopiering av fil-baserad databas om du vill skapa en enkel säkerhetskopiering post måste ligga inom säkerhetskopieringskatalogen. I annat fall kan inte SAP HANA initiera din angivna loggsäkerhetskopior.

![Gör en filbaserad säkerhetskopia för att skapa en enda post för säkerhetskopiering](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


När din första lyckade storage-ögonblicksbilder körs, kan du ta bort test-ögonblicksbilden som kördes i steg 6. Mer information finns i ”ta bort test ögonblicksbilder - removeTestStorageSnapshot” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Övervaka antal och storlek för ögonblicksbilder på volymen

Du kan övervaka antalet ögonblicksbilder och lagringsanvändningen dessa ögonblicksbilder på en specifik lagringsvolym. Den `ls` kommandot visas inte den ögonblicksbild katalog eller filer. Linux OS-kommandot `du` visar information om dessa ögonblicksbilder av lagring eftersom de lagras på samma volymer. Använd kommandot med följande alternativ:

- `du –sh .snapshot`: Det här alternativet ger en totalsumma på alla ögonblicksbilder i katalogen ögonblicksbild.
- `du –sh --max-depth=1`: Det här alternativet visar en lista över alla ögonblicksbilder som sparas i den **.snapshot** mapp och storleken på varje ögonblicksbild.
- `du –hc`: Det här alternativet innehåller den totala storleken som används av alla ögonblicksbilder.

Med dessa kommandon för att se till att ögonblicksbilder som tas och lagras inte förbrukar alla lagring på volymerna.

>[!NOTE]
>Ögonblicksbilder av startavbildningen LUN inte visas med de tidigare kommandona.

### <a name="get-details-of-snapshots"></a>Hämta information om ögonblicksbilder
Om du vill visa mer information om ögonblicksbilder kan använda skriptet `azure_hana_snapshot_details`. Du kan köra det här skriptet på någon av platserna om det finns en aktiv server i disaster recovery-plats. Skriptet innehåller följande utdata, fördelat på varje volym som innehåller ögonblicksbilder: 
   * Storleken på totalt antal ögonblicksbilder för en volym
   * Följande information i varje ögonblicksbild i volymen: 
      - Namnet på ögonblicksbilden 
      - Tid för skapande 
      - Storleken på ögonblicksbilden
      - Frekvensen för ögonblicksbilden
      - HANA Backup-ID som är associerade med den ögonblicksbilden, om det behövs

Syntaxen för kommandot och utdata finns i ”lista över ögonblicksbilder - azure_hana_snapshot_details” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Minska antalet ögonblicksbilder på en server

Du kan minska antalet vissa etiketter för ögonblicksbilder som du lagrar som tidigare förklarats. Senast två parametrar för kommandot att initiera en ögonblicksbild är etiketten och antalet ögonblicksbilder som du vill behålla.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

I exemplet ovan ögonblicksbild etiketten är **dailyhana**. Antalet ögonblicksbilder med den här etiketten behålls är **28**. När du har besvarat förbrukningen av diskutrymme, kan du minska antalet lagrade ögonblicksbilder. Ett enkelt sätt att minska antalet ögonblicksbilder till 15, till exempel är att köra skriptet med den sista parametern inställd **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Om du kör skriptet med den här inställningen är det antal ögonblicksbilder, vilket innefattar den nya storage-ögonblicksbilden, 15. De senaste 15 ögonblicksbilderna hålls och 15 äldre ögonblicksbilder tas bort.

 >[!NOTE]
 > Det här skriptet minskar antalet ögonblicksbilder endast om det finns fler än en timme gamla ögonblicksbilder. Skriptet tar inte bort ögonblicksbilder som är mindre än en timme gamla. Dessa begränsningar är relaterade till de valfria disaster recovery-funktionerna som erbjuds.

Om du inte längre vill hantera en uppsättning ögonblicksbilder med prefixet säkerhetskopiering **dailyhana** i syntaxexemplen, kör skriptet med **0** som kvarhållning tal. Alla ögonblicksbilder som matchar etiketten tas sedan bort. Ta bort alla ögonblicksbilder kan påverka funktionerna i HANA stora instanser disaster recovery-funktionen.

Ett andra alternativ att ta bort specifika ögonblicksbilder är att använda skriptet `azure_hana_snapshot_delete`. Det här skriptet är utformad för att ta bort en ögonblicksbild eller en uppsättning ögonblicksbilder antingen med hjälp av HANA säkerhetskopie-ID som hittades i HANA Studio eller via ögonblicksbild namnet i sig. Säkerhetskopie-ID är för närvarande endast knuten till ögonblicksbilder som skapats för den **hana** typ av ögonblicksbild. Ta en ögonblicksbild säkerhetskopior av typen **loggar** och **Start** inte utför en SAP HANA ögonblicksbild, så det finns inga säkerhetskopie-ID för dessa ögonblicksbilder. Om ögonblicksbild namnet anges efter alla ögonblicksbilder i olika volymer som matchar namnet på angivna ögonblicksbilden. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Mer information om skriptet finns i ”ta bort en ögonblicksbild - azure_hana_snapshot_delete” i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Kör skriptet som användare **rot**.

>[!IMPORTANT]
>Om det finns data som finns endast på ögonblicksbilden som du tänker ta bort, när ögonblicksbilden har tagits bort, att data går förlorade permanent.


## <a name="file-level-restore-from-a-storage-snapshot"></a>På filnivå återställning från en ögonblicksbild för lagring

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
För ögonblicksbild typer **hana** och **loggar**, du kan få åtkomst till ögonblicksbilder direkt på volymer i den **.snapshot** directory. Det finns en underkatalog för var och en av ögonblicksbilder. Kopiera varje fil i tillståndet den var i vid ögonblicksbild från den underkatalogen i den faktiska katalogstrukturen. 

I den aktuella versionen av skript, det finns *inga* Återställ skriptet för återställning från ögonblicksbilder som självbetjäning. Återställning från ögonblicksbilder kan utföras som en del av självbetjäning disaster recovery-skript på katastrofåterställningsplatsen under redundansväxlingen. Om du vill återställa en önskad ögonblicksbild från de befintliga tillgängliga ögonblicksbilderna, måste du kontakta Microsoft-driftsteamet genom att öppna en tjänstbegäran.

>[!NOTE]
>Återställningen fungerar med en fil inte för ögonblicksbilder av startavbildningen LUN som är oberoende av vilken typ av enheter för stora HANA-instansen. Den **.snapshot** katalogen inte visas i filen Boot.ini LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Återställ till den senaste HANA-ögonblicksbilden

I ett scenario med produktion ned startas hela återställningen från en ögonblicksbild för lagring av som en kundincident med Microsoft Azure-supporten. Det är bara några hög angelägenhetsgrad om data har tagits bort i ett produktionssystem och det enda sättet att hämta det är att återställa produktionsdatabasen.

I annat fall kan en point-in-time-återställning kan vara med låg angelägenhetsgrad och planerade dagar i förväg. Du kan planera återställningen med SAP HANA på Azure i stället för att höja flaggan hög prioritet. Du kan till exempel planerar att uppgradera SAP-program genom att använda ett nytt paket för förbättring. Du måste sedan återgå till en ögonblicksbild som representerar tillstånd innan paketuppgradering förbättring.

Innan du skickar begäran måste du förbereda. SAP HANA på Azure-teamet kan sedan hantera begäran och ge de återställda volymerna. Därefter kan återställa du HANA-databas baserat på ögonblicksbilder.

Möjligheter för att få en ögonblicksbild återställs med den nya verktygsuppsättningen finns i ”hur du återställer en ögonblicksbild” i [manuell återställning guide för SAP HANA på Azure från en ögonblicksbild av storage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Följ dessa steg för att förbereda för begäran.

1. Bestäm vilken ögonblicksbild för att återställa. Endast hana/datavolymen återställs, såvida inte du instruera annars. 

1. Stänga av HANA-instans.

   ![Stäng av HANA-instans](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Demontera datavolymerna på varje nod för HANA-databas. Om datavolymerna är fortfarande monterade för operativsystemet, misslyckas återställningen av ögonblicksbilden.

   ![Avmontera datavolymerna på varje nod för HANA-databas](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Öppna en supportförfrågan för Azure och innehåller instruktioner för återställning av en specifik ögonblicksbild:

   - Under återställningen: SAP HANA på Azure-tjänsten kan be dig att delta i ett konferenssamtal att samordna, kontrollera och bekräfta att rätt storage-ögonblicksbilder har återställts. 

   - Efter återställningen: SAP HANA på Azure-tjänsten meddelar dig när storage-ögonblicksbilder återställs.

1. När återställningsprocessen har slutförts, montera om alla datavolymer.

   ![Montera om alla datavolymer](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



En annan möjlighet för att hämta, till exempel SAP HANA-datafilerna har återställts från en ögonblicksbild för lagring, dokumenteras i steg 7 i [manuell återställning guide för SAP HANA på Azure från en ögonblicksbild av storage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Om du vill återställa från en ögonblicksbildsäkerhetskopia, se [manuell återställning guide för SAP HANA på Azure från en ögonblicksbild av storage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 

>[!Note]
>Om din ögonblicksbild har återställts av Microsoft operations behöver du inte steg 7.


### <a name="recover-to-another-point-in-time"></a>Återställa till en annan tidpunkt
Om du vill återställa till en viss punkt i tiden, finns i ”återställa databasen till följande punkt i tiden” i [manuell återställning guide för SAP HANA på Azure från en ögonblicksbild av storage](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 


## <a name="next-steps"></a>Nästa steg
- Se [Disaster recovery principer och förberedelse av](hana-concept-preparation.md).
