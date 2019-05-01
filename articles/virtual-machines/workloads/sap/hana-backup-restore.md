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
ms.openlocfilehash: 7c03a7e5763f580bf1e17232a5850064710c8227
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707251"
---
# <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

>[!IMPORTANT]
>Den här dokumentationen är ingen ersättning av dokumentationen för administration av SAP HANA eller SAP Notes. Det förväntas att läsaren har en djupare förståelse av och expertis i SAP HANA-administration och åtgärder, särskilt med ämnen för säkerhetskopiering, återställning, hög tillgänglighet och katastrofåterställning. I den här dokumentationen visas skärmbilder från SAP HANA-Studio. Innehåll, struktur och arten av de olika stegen i SAP-verktyg för fjärrserveradministration och verktyg själva ändras från SAP HANA version till version.

Det är viktigt att du arbeta med steg och processer som tas i miljön och med dina HANA-versioner och versioner. Vissa processer som beskrivs i den här dokumentationen är förenklad för en bättre förståelse och är inte avsedda att användas som detaljerade anvisningar för slutlig åtgärden handböcker. Om du vill skapa åtgärden handböcker för dina konfigurationer måste du testa och arbeta med dina processer och dokumentera de processer som rör dina specifika konfigurationer. 

En av de viktigaste aspekterna till operativsystem databaser är att skydda dem mot katastrofhändelser. Orsaken till de här händelserna kan vara allt från enkla användarfel vid naturkatastrofer.

Säkerhetskopiera en databas, med möjlighet att återställa den till valfri punkt i tiden (till exempel innan någon bort viktiga data), gör det möjligt för återställning till ett tillstånd som är så nära som möjligt till det som finns innan du kan avbrott.

Två typer av säkerhetskopior måste utföras för att uppnå en sådan funktion för att återställa:

- Säkerhetskopior av databasen: Fullständiga, inkrementella eller differentiella säkerhetskopieringar
- Säkerhetskopieringar av transaktionsloggen

Förutom fullständig--databassäkerhetskopieringar som utförs på en nivå för programmet, kan du utföra säkerhetskopiering med ögonblicksbilder av lagring. Ögonblicksbilder av lagring ersätter inte säkerhetskopieringar av transaktionsloggen. Säkerhetskopieringar av transaktionsloggen är viktigt att återställa databasen till en viss punkt eller till tomt loggar från redan genomförda transaktioner. Storage-ögonblicksbilder kan dock snabbare återställning genom att snabbt tillhandahålla en sammanslagning och tydlig bild av databasen. 

SAP HANA på Azure (stora instanser) erbjuder två alternativ för säkerhetskopiering och återställning:

- Gör det själv (DIY). När du har kontrollerat att det finns tillräckligt med diskutrymme kan du utföra fullständiga säkerhetskopior av databasen och loggfiler på något av följande metoder för disk-säkerhetskopiering. Du kan säkerhetskopiera antingen direkt till volymer som är ansluten till stora HANA-instansen enheter eller att Network File-resurser (NFS) och som har ställts in på Azure-datorer (VM). Koppla Azure Storage till den virtuella datorn i det senare fallet kan kunder konfigurera en Linux-VM i Azure, och dela lagringsutrymme via en NFS-server som är konfigurerade i den virtuella datorn. Om du utför säkerhetskopiering mot volymer som kopplas direkt till stora HANA-instansen enheter kan behöva du kopiera säkerhetskopior av till ett Azure storage-konto (när du har konfigurerat en Azure-dator som exporterar NFS-resurser som är baserade på Azure Storage). Du kan också använda en Azure backup-valv eller Azure kall lagring. 

   Ett annat alternativ är att använda ett verktyg för tredje parts data protection säkerhetskopiorna för när de kopieras till ett Azure storage-konto. Gör det själv säkerhetskopieringsalternativ kan också vara nödvändigt för data som du behöver lagra under längre tidsperioder för efterlevnad och granskning. I samtliga fall kopieras säkerhetskopiorna till NFS-resurser via en virtuell dator och Azure Storage som representeras.

- Infrastruktur för säkerhetskopiering och återställningsfunktionen. Du kan också säkerhetskopiera och återställa kärnfunktioner där den underliggande infrastrukturen med SAP HANA på Azure (stora instanser). Det här alternativet uppfyller behovet av säkerhetskopieringar och snabb återställning. Resten av det här avsnittet adresser säkerhetskopiering och återställning av funktionerna som erbjuds med stora HANA-instanser. Det här avsnittet beskriver också relation säkerhetskopiering och återställning måste katastrofen recovery-funktionerna som erbjuds av stora HANA-instanser.

> [!NOTE]
>   Den ögonblicksbild-teknik som används av den underliggande infrastrukturen med stora HANA-instanser har ett beroende på SAP HANA-ögonblicksbilder. SAP HANA-ögonblicksbilder fungerar då inte tillsammans med flera innehavare av behållare för SAP HANA-databas för flera innehavare. Om bara en klient har distribuerats, SAP HANA-ögonblicksbilder fungerar och den här metoden kan användas.

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Med hjälp av ögonblicksbilder av lagring av SAP HANA på Azure (stora instanser)

Lagringsinfrastruktur som underliggande SAP HANA på Azure (stora instanser) har stöd för storage-ögonblicksbilder av volymer. Både säkerhetskopierings- och återställa volymer stöds, med följande överväganden:

- I stället för fullständiga databassäkerhetskopieringar tas storage-ögonblicksbilder regelbundet.
- När du utlöser en ögonblicksbild över /hana/data och /hana/shared (inklusive /usr/sap) volymer, teknik för ögonblicksbilder initierar en SAP HANA ögonblicksbild innan den kan köras storage-ögonblicksbilder. SAP HANA-ögonblicksbilder är den installationen för slutlig log återställningar efter återställning av storage-ögonblicksbilder. För HANA-ögonblicksbilder ska lyckas, behöver du en aktiv HANA-instans.  I HSR scenariot stöds storage-ögonblicksbilder inte på aktuella sekundära noden där HANA-ögonblicksbilder inte kan utföras.
- När storage-ögonblicksbilder har har körts, tas SAP HANA-ögonblicksbilder bort.
- Säkerhetskopieringar av transaktionsloggen tas ofta och lagras i /hana/logbackups volymen eller i Azure. Du kan utlösa /hana/logbackups volymen som innehåller säkerhetskopieringarna av transaktionsloggen för att ta en ögonblicksbild separat. I så fall behöver du inte köra en HANA-ögonblicksbilder.
- Om du måste återställa en databas till en viss punkt i tiden, begär den Microsoft Azure-supporten (för en produktion avbrott) eller SAP HANA på Azure återställning till en viss storage-ögonblicksbild. Ett exempel är en planerad återställning av en sandbox-system till det ursprungliga tillståndet.
- SAP HANA-ögonblicksbilder som ingår i storage-ögonblicksbilder är en förskjutning för att tillämpa säkerhetskopieringar av transaktionsloggen som har körts och lagrat när lagring ögonblicksbilden togs.
- Dessa säkerhetskopieringar av transaktionsloggen kommer att återställa databasen till en viss punkt i tiden.

Du kan utföra storage-ögonblicksbilder som riktar in sig på tre klasser av volymer:

- En kombinerad ögonblicksbild över/hana/data samt /hana/shared (inklusive sap/usr /). Den här ögonblicksbilden kräver skapandet av en SAP HANA-ögonblicksbilder som förberedelse för storage-ögonblicksbilder. SAP HANA-ögonblicksbilder ser till att databasen är i ett konsekvent tillstånd från en storage synsätt. Och att för återställningen bearbeta det vill säga en tidpunkt att ställa in dig på.
- En separat ögonblicksbild över/hana/logbackups.
- En partition av operativsystemet.

Få de senaste ögonblicksbild skript och dokumentation från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). När du hämtar ögonblicksbild skriptpaketet från den [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), du får tre filer som en är en PDF-dokumentationen för funktionerna. Se till att du går vidare med anvisningarna i avsnittet ”skaffa verktygen ögonblicksbild' när ladda ned verktygsuppsättningen.

## <a name="storage-snapshot-considerations"></a>Överväganden för ögonblicksbilder av lagring

>[!NOTE]
>Storage-ögonblicksbilder använda lagringsutrymme som har allokerats till stora HANA-instansen-enheter. Du behöver tänka på följande aspekter för schemaläggning av ögonblicksbilder av lagring och hur många storage-ögonblicksbilder som ska lagras. 

Specifika säkerhetsnivån för storage-ögonblicksbilder för SAP HANA på Azure (stora instanser) är:

- En ögonblicksbild av specifika lagringsutrymmen (vid tidpunkten när den tas) förbrukar lite lagringsutrymme.
- Ögonblicksbilden behöver lagra det ursprungliga innehållet i block, samt dataändringarna som innehållsändringar för data och innehållet i SAP HANA-data filer ändras på lagringsvolymen.
- Därför ökar storage-ögonblicksbilder i storlek. Ju längre ögonblicksbilden finns, desto större blir storage-ögonblicksbilder.
- Flera ändringar som görs till SAP HANA-databasvolymen över livslängden för en storage-ögonblicksbilder, den större utrymme för storage-ögonblicksbilder.

SAP HANA på Azure (stora instanser) levereras med fast Volymstorlekar för data och loggvolymer för SAP HANA. Utför ögonblicksbilder av volymerna eats i utrymmet på volymen. Du måste avgöra när du vill schemalägga ögonblicksbilder av lagring. Du måste också övervaka förbrukningen av diskutrymme för lagringsvolymer, samt hantera antalet ögonblicksbilder som du lagrar. Du kan inaktivera storage-ögonblicksbilder när du importerar stora mängder data eller utföra andra viktiga ändringar till HANA-databas. 


Följande avsnitt innehåller information för att utföra dessa ögonblicksbilder, inklusive allmänna rekommendationer:

- Även om maskinvaran som kan klara 255 ögonblicksbilder per volym, vill du hålla betydligt lägre än det här talet. Rekommendationen är 250 eller mindre.
- Innan du utför ögonblicksbilder av lagring, övervaka och hålla reda på ledigt utrymme.
- Minska antalet ögonblicksbilder av lagring baserat på ledigt utrymme. Du kan sänka antal ögonblicksbilder som du behåller eller du kan utöka volymer. Du kan beställa ytterligare lagringsutrymme i enheter om 1 TB.
- Inaktivera ögonblicksbilder av lagring på /hana/data volymen under aktiviteter, till exempel data flyttas till SAP HANA med Migreringsverktyg för SAP-plattform (R3load) eller återställning av SAP HANA-databaser från säkerhetskopior. 
- Under större omorganisering av SAP HANA-tabeller, bör ögonblicksbilder av lagring undvikas, om möjligt.
- Ögonblicksbilder av lagring är en förutsättning för att dra nytta av katastrofen återställningsfunktioner för SAP HANA på Azure (stora instanser).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Krav för att använda självbetjäning storage-ögonblicksbilder

För att säkerställa att skriptet ögonblicksbild körs rätt, se till att Perl är installerad på Linux-operativsystem på stora HANA-instanser-servern. Perl finns förinstallerat på din enhet för stora HANA-instansen. Om du vill kontrollera vilken version av Perl, använder du följande kommando:

`perl -v`

![Den offentliga nyckeln kopieras genom att köra det här kommandot](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Ställ in ögonblicksbilder av lagring

Följ dessa steg om du vill konfigurera ögonblicksbilder av lagring med stora HANA-instanser:
1. Se till att Perl är installerad på Linux-operativsystem på stora HANA-instanser-servern.
1. Ändra/etc/ssh/ssh\_config att lägga till raden _Mac-datorer hmac-sha1_.
1. Skapa ett konto för säkerhetskopiering av SAP HANA på huvudnoden för varje SAP HANA-instans som du kör, om tillämpligt.
1. Installera SAP HANA HDB-klienten på alla servrar som stora SAP HANA-instanser.
1. Skapa en offentlig nyckel för att komma åt det underliggande lagringsinfrastruktur som styr ögonblicksbilder skapas på den första stora instanser av SAP HANA-servern för varje region.
1. Kopiera skript och konfigurationsfilen från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) till platsen för **hdbsql** i SAP HANA-installationen.
1. Ändra den *HANABackupDetails.txt* fil som krävs för lämplig kund-specifikationer.

Få de senaste ögonblicksbild skript och dokumentation från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). För detaljerade anvisningar som anges ovan, som avser [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

### <a name="consideration-for-mcod-scenarios"></a>Överväganden för MCOD scenarier
Om du kör en [MCOD scenariot](https://launchpad.support.sap.com/#/notes/1681092) med flera SAP HANA-instanser på en enhet för stora HANA-instansen, har du separata lagringsvolymer som tillhandahållits för var och en av SAP HANA-instanser. Information om hur MDC och andra överväganden, kontrollera [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) kapitel **viktiga saker att komma ihåg**.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Steg 1: Installera SAP HANA HDB-klienten

Linux-operativsystem som är installerade på SAP HANA på Azure (stora instanser) innehåller mappar och skript som krävs för att köra SAP HANA storage-ögonblicksbilder för återställning för säkerhetskopiering och haveriberedskap. Sök efter nyare versioner i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Den senaste versionen av skripten är 4.0. Olika skript kan ha olika utgåvor av mindre inom samma huvudversion.

Det är ditt ansvar att installera klienten HDB för SAP HANA på stora HANA-instansen enheter medan du installerar SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Steg 2: Ändra/etc/ssh/ssh\_config

Det här steget beskrivs i detalj i kontrollerar för nyare versioner i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) i kapitlet **”aktivera kommunikation med lagringsutrymmen”**


### <a name="step-3-create-a-public-key"></a>Steg 3: Skapa en offentlig nyckel

Om du vill aktivera åtkomst till lagring ögonblicksbild gränssnitt klientorganisationens stora HANA-instansen måste du upprätta en procedur inloggning via en offentlig nyckel. På den första SAP HANA på Azure (stora instanser)-server i din klient, skapa en offentlig nyckel som används för att komma åt lagringsinfrastrukturen. Den offentliga nyckeln garanterar att ett lösenord inte krävs att logga in på gränssnitt som storage ögonblicksbild. Skapa en offentlig nyckel innebär också att du inte behöver underhålla autentiseringsuppgifter för lösenord. Den exakta steg hur du skapar offentligt nyckeln beskrivs i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) i kapitlet **”aktivera kommunikation med lagringsutrymmen”**


### <a name="step-4-create-an-sap-hana-user-account"></a>Steg 4: Skapa ett användarkonto för SAP HANA

Om du vill initiera skapandet av SAP HANA-ögonblicksbilder måste du skapa ett användarkonto i SAP HANA som kan använda för skript för storage-ögonblicksbilder. Skapa ett användarkonto för SAP HANA SAP HANA-Studio för detta ändamål. Användaren måste skapas under SYSTEMDB och inte under SID-databasen för MDC. I miljön för enskild behållare skapas användaren i klientdatabasen. Det här kontot måste ha följande behörigheter: **Säkerhetskopiera Admin** och **Catalog Läs**. Visa hur du ställer in användaren och använder användaren läsa [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) i kapitlet **”aktivera kommunikation med SAP HANA”**


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Steg 5: Auktorisera användarkontot för SAP HANA

I det här steget ska godkänna du SAP HANA-användarkonto som du skapade, så att skript som inte behöver att skicka lösenord vid körning. SAP HANA-kommandot `hdbuserstore` gör det möjligt att skapa en SAP HANA användarnyckel, som är lagrad på en eller flera SAP HANA-noder. Användarnyckel kan användare åtkomst till SAP HANA utan att behöva hantera lösenord från inom scripting processen. Scripting processen beskrivs senare i den här artikeln.

>[!IMPORTANT]
>Kör dessa med samma användarkontext ögonblicksbild-kommandona körs i. I annat fall fungerar kommandona ögonblicksbild inte korrekt.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Steg 6: Hämta ögonblicksbild-skript, konfigurera ögonblicksbilderna och testa konfiguration och anslutning

Hämta den senaste versionen av skript från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Hur skript som ska installeras har ändrats majorly med version 4.0 av skript. Mer information, Läs [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) i kapitlet **”aktivera kommunikation med SAP HANA”**

De exakta i kommandon finns i kapitlet **”enkel installation av verktyg för ögonblicksbild (standard)”** dokumentets [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Vi rekommenderar användning av standardinstallationen. Om du vill uppgradera från version 3.x till 4.0, kontrollera avsnittet **”uppgradera en befintlig installation”** av [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). För att avinstallera 4.0 verktygsuppsättning, följer du anvisningarna i **'Avinstallation av verktygen ögonblicksbild'** i [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Glöm inte att köra stegen som beskrivs i **'Fullständig installation av verktyg för ögonblicksbild'** av [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Syftet med olika skript och filer som de har installerats listas och beskrivs i **”vad är verktygen ögonblicksbild”?** dokumentets [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Innan du konfigurerar ögonblicksbild-verktyg, se till att du också HANA platser och inställningar är rätt konfigurerad enligt **SAP HANA-konfiguration** dokumentets [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Konfigurationen av ögonblicksbild verktygsuppsättningen beskrivs i detalj i **Config fil – HANABackupCustomerDetails.txt** dokumentets [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="testing-connectivity-with-sap-hana"></a>Testar anslutningen med SAP HANA

När du placerar alla konfigurationsdata i den *HANABackupCustomerDetails.txt* filen, kontrollera om konfigurationerna som är korrekta för HANA instansdata. Använd skript för `testHANAConnection`, som är oberoende av en konfiguration för SAP HANA skala upp eller skala ut.

Mer information finns i **”Kontrollera anslutningen med SAP HANA - testHANAConnection'** dokumentets [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

#### <a name="testing-storage-connectivity"></a>Testa storage-anslutning

I nästa steg är att kontrollera anslutningen till lagring baserat på data som du placerar i den *HANABackupCustomerDetails.txt* configuration filen och sedan köra en test-ögonblicksbild. Innan du kan köra den `azure_hana_backup` kommandot, måste du köra det här testet. Sekvens med kommandon för det här testet visas i **”Kontrollera anslutningen med storage - testStorageSnapshotConnection'** dokumentets [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Efter en lyckad inloggning för virtuell dator gränssnitten för lagring, skriptet fortsätter med fas 2 och skapar en test-ögonblicksbild. Utdata visas här för en konfiguration med tre noder skala ut SAP Hana:

Om testet ögonblicksbilden har körts har med skript, kan du fortsätta med schemaläggning faktiska storage-ögonblicksbilder. Om du inte lyckas kan du undersöka problem innan du går vidare. Test-ögonblicksbild ska vara runt tills de första verkliga ögonblicksbilderna är klar.


### <a name="step-7-perform-snapshots"></a>Steg 7: Utföra ögonblicksbilder

När steg för förberedelse är klar kan du börja konfigurera och schemalägga faktiska storage-ögonblicksbilder. Skriptet som ska schemaläggas fungerar med SAP HANA skala upp och skala ut konfigurationer. Schemalägga skriptet med hjälp av verktyget cron för periodiska och regelbundna körningen av skriptet säkerhetskopieringen. 

För exakta kommandosyntax och funktioner kan läsa **'Utför ögonblicksbildsäkerhetskopia - azure_hana_backup'** dokumentets [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).  

Körningen av skriptet `azure_hana_backup` skapar lagringen ögonblicksbild i följande tre faser:

1. Kör SAP HANA-ögonblicksbilder
1. Kör en storage-ögonblicksbilder
1. Tar bort SAP HANA-ögonblicksbilden som skapades före körning av storage-ögonblicksbilder

För att köra skriptet anropar du den från den körbara HDB-mappen som de kopierades. 

Kvarhållningsperioden administreras med antalet ögonblicksbilder som skickas som en parameter när du kör skriptet. Hur lång tid som täcks av storage-ögonblicksbilder är en funktion av körning och antalet ögonblicksbilder som skickas som en parameter när du kör skriptet. Om antalet ögonblicksbilder som hålls överstiger det antal som namnges som en parameter i anropet av skriptet, raderas den äldsta storage-ögonblicksbilden med samma etikett innan en ny ögonblicksbild körs. Det tal som du ger eftersom den sista parametern för anropet är det nummer du kan använda för att styra antalet ögonblicksbilder sparas. Med det här talet kan du också styra, indirekt, hur mycket diskutrymme används för ögonblicksbilder. 


## <a name="snapshot-strategies"></a>Strategier för ögonblicksbild
Frekvensen för ögonblicksbilder för de olika typerna beror på om du använder stora HANA-instansen disaster recovery-funktionen. Den här funktionen är beroende av ögonblicksbilder av lagring, som kan kräva särskilda rekommendationer för frekvens och körningen perioder av storage-ögonblicksbilder. 

Överväganden och rekommendationer som följer, antas att du gör *inte* använda disaster recovery-funktionen som stora HANA-instanser erbjuder. I stället använder storage-ögonblicksbilder för att ha säkerhetskopior och att kunna ge point-in-time-återställning för de senaste 30 dagarna. Med begränsningar av antalet ögonblicksbilder och utrymme kan har kunder vara följande krav:

- Återställningstid för point-in-time-återställning.
- Hur mycket diskutrymme.
- Återställningspunkt och målen för potentiella återställning efter en katastrof.
- Slutlig för körning av HANA-full-databassäkerhetskopieringar mot diskar. När en fullständig databassäkerhetskopia mot diskar eller **backint** gränssnittet utförs, körning av storage-ögonblicksbilder misslyckas. Om du planerar att köra fullständiga databassäkerhetskopieringar ovanpå ögonblicksbilder av lagring, se till att körningen av storage-ögonblicksbilder inaktiveras under den här tiden.
- Antalet ögonblicksbilder per volym (begränsat till 250).

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

För kunder som inte använder disaster recovery-funktionen för HANA stora instanser, är den ögonblicksbild perioden uppstår mer sällan. I sådana fall kan kunder utföra kombinerade ögonblicksbilder på /hana/data och /hana/shared (inklusive /usr/sap) i 12 timmar eller 24-timmarsformat punkter och de hålla ögonblicksbilder för en månad. Samma sak gäller med ögonblicksbilder av säkerhetskopiering loggvolymen. Körningen av SAP HANA säkerhetskopieringar av transaktionsloggen mot loggvolymen säkerhetskopiering sker dock i fem 15 minuters perioder.

Schemalagda lagringsögonblicksbilder utförs bäst med hjälp av cron. Använda samma skript för alla säkerhetskopior och disaster recovery behov och att du ändrar skriptet indata för att matcha de olika begärt säkerhetskopieringstider. Dessa ögonblicksbilder är alla schemalagda på olika sätt i cron beroende på sin Utförandetid: per timme, 12 timmar, varje dag eller varje vecka. 

Följande är ett exempel på ett cron-schemat i/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
I exemplet ovan är en timvis kombinerade ögonblicksbild som fullständigt täcker alla volymer som innehåller data/hana/och /hana/shared/SID (inklusive/usr/sap) platser. Använd den här typen av ögonblicksbild för en snabbare point-in-time-återställning under de senaste två dagarna. Det finns också en ögonblicksbild på dessa volymer. Därför har två dagars täckning som per timme ögonblicksbilder, plus fyra veckor täckningen av dagliga ögonblicksbilder. Dessutom säkerhetskopieras säkerhetskopiering transaktionsvolymer log varje dag. Dessa säkerhetskopior behålls i fyra veckor samt. Som du ser i den tredje adressraden crontab är säkerhetskopia av transaktionsloggen HANA schemalagd att köras var femte minut. Starttider för olika cron-jobb som kör lagringsögonblicksbilder ut, så att dessa ögonblicksbilder inte körs på en gång vid en viss punkt i tiden. 

I följande exempel du utföra en kombinerad ögonblicksbild som täcker de volymer som innehåller/hana/data och /hana/shared/SID (inklusive/usr/sap) platser per timme. Du kan behålla de här ögonblicksbilderna i två dagar. Ögonblicksbilder av säkerhetskopiering transaktionsvolymer log utförs på basis av 5 minuter och hålls i 4 timmar. Som är tidigare, säkerhetskopiering av HANA transaktionsloggfilen schemalagd att köras var femte minut. Ögonblicksbild av transaktionen-loggvolymen säkerhetskopiering utförs med en 2-minuters fördröjning när säkerhetskopian av transaktionsloggen har startats. Loggsäkerhetskopiering för SAP HANA-transaktion bör slutföras under normala omständigheter inom dessa två minuter. Som tidigare, volymen som innehåller startavbildningen LUN backas upp en gång per dag av storage-ögonblicksbilder och sparas i fyra veckor.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Följande bild illustrerar sekvenser av det tidigare exemplet, exklusive start LUN:

![Förhållandet mellan säkerhetskopieringar och ögonblicksbilder](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA utför regelbundna skrivningar till /hana/log volymen för att dokumentera allokerade ändringar till databasen. Med jämna mellanrum skriver en lagringspunkt i SAP HANA till /hana/data volymen. Som anges i crontab körs en säkerhetskopiering av SAP HANA-transaktionsloggen var femte minut. Du kan också se att en SAP HANA-ögonblicksbilder körs varje timme till följd av utlöser en kombinerad storage ögonblicksbild över /hana/data och /hana/shared/SID volymer. När ögonblicksbilden HANA lyckas, utförs kombinerade storage-ögonblicksbilder. Som finns beskrivet i crontab, körs storage-ögonblicksbilder på volymen /hana/logbackup var 5 minuter, cirka 2 minuter efter säkerhetskopian av transaktionsloggen HANA.

> 

>[!IMPORTANT]
> Användningen av ögonblicksbilder av lagring för säkerhetskopior av SAP HANA är värdefullt endast när ögonblicksbilder utförs tillsammans med SAP HANA säkerhetskopieringar av transaktionsloggen. Dessa säkerhetskopieringar av transaktionsloggen måste omfatta tidsperioder mellan storage-ögonblicksbilder. 

Om du har angett ett åtagande för användare av en point-in-time-återställning på 30 dagar, måste du:

- Komma åt en kombinerad lagring ögonblicksbild över /hana/data och /hana/shared/SID som är 30 dagar i extrema fall.
- Har säkerhetskopior av sammanhängande transaktionsloggen som omfattar tiden mellan alla kombinerade storage-ögonblicksbilder. Därför måste äldsta ögonblicksbilden av säkerhetskopiering transaktionsvolymer log vara 30 dagar gamla. Detta gäller inte om du kopierar säkerhetskopieringarna av transaktionsloggen till en annan NFS-resurs som finns på Azure storage. I så fall kan du hämta gamla säkerhetskopieringar av transaktionsloggen från den NFS-resursen.

Om du vill dra nytta av ögonblicksbilder av lagring och eventuell storage-replikering av säkerhetskopieringar av transaktionsloggen, som du behöver ändra den plats som SAP HANA skriver säkerhetskopieringarna av transaktionsloggen. Du kan göra den här ändringen i HANA Studio. Även om SAP HANA säkerhetskopierar automatiskt fullständig log segment, bör du ange ett intervall för log-säkerhetskopiering för att vara entydig. Detta gäller särskilt när du använder alternativet disaster recovery eftersom du vanligtvis vill köra säkerhetskopieringar med en deterministisk punkt. I följande fall har 15 minuter angetts som log säkerhetskopieringsintervallet.

![Schemalägg säkerhetskopiering för SAP HANA-loggarna i SAP HANA-Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Du kan också välja säkerhetskopieringar som är kortare än 15 minuter. En mer frekventa inställning används ofta tillsammans med funktioner för haveriberedskap för stora HANA-instanser. Vissa kunder utföra säkerhetskopieringar av transaktionsloggen var femte minut.  

Om databasen har aldrig säkerhetskopierats, är det sista steget att utföra en säkerhetskopiering av fil-baserad databas om du vill skapa en enkel säkerhetskopiering post måste ligga inom säkerhetskopieringskatalogen. I annat fall kan inte SAP HANA initiera din angivna loggsäkerhetskopior.

![Gör en filbaserad säkerhetskopia för att skapa en enda post för säkerhetskopiering](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


När din första lyckade storage-ögonblicksbilder har körts, måste du ta bort test-ögonblicksbilden som kördes i steg 6. Läs **”ta bort test ögonblicksbilder - removeTestStorageSnapshot'** i dokumentet [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) mer information. 


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Övervakning av antal och storlek för ögonblicksbilder på volymen

Du kan övervaka antalet ögonblicksbilder och lagringsanvändningen dessa ögonblicksbilder på en specifik lagringsvolym. Den `ls` kommandot visas inte den ögonblicksbild katalog eller filer. Dock Linux OS-kommandot `du` visar information om dessa ögonblicksbilder av lagring, eftersom de lagras på samma volymer. Kommandot kan användas med följande alternativ:

- `du –sh .snapshot`: Det här alternativet ger en totalsumma på alla ögonblicksbilder i katalogen ögonblicksbild.
- `du –sh --max-depth=1`: Det här alternativet visar en lista över alla ögonblicksbilder som sparas i den **.snapshot** mapp och storleken på varje ögonblicksbild.
- `du –hc`: Det här alternativet innehåller den totala storleken som används av alla ögonblicksbilder.

Med dessa kommandon för att se till att ögonblicksbilder som tas och lagras inte förbrukar alla lagring på volymerna.

>[!NOTE]
>Ögonblicksbilder av startavbildningen LUN visas inte med föregående kommandon.

### <a name="getting-details-of-snapshots"></a>Hämtning av information om ögonblicksbilder
Om du vill visa mer information om ögonblicksbilder kan du också använda skriptet `azure_hana_snapshot_details`. Det här skriptet kan köras på någon av platserna om det finns en aktiv server i disaster recovery-plats. Skriptet innehåller följande utdata, fördelat på varje volym som innehåller ögonblicksbilder: 
   * Storleken på totalt antal ögonblicksbilder för en volym
   * Följande information i varje ögonblicksbild i volymen: 
      - Namnet på ögonblicksbilden 
      - Tid för skapande 
      - Storleken på ögonblicksbilden
      - Frekvensen för ögonblicksbilden
      - HANA Backup-ID som är associerade med den ögonblicksbilden, om det behövs

För syntaxen för kommandot och utdata kontrollen **”lista över ögonblicksbilder - azure_hana_snapshot_details'** i dokumentet [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Minska antalet ögonblicksbilder på en server

Du kan minska antalet vissa etiketter för ögonblicksbilder som du lagrar som beskrevs tidigare. Senast två parametrar för kommandot att initiera en ögonblicksbild är etiketten och antalet ögonblicksbilder som du vill behålla.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

I exemplet ovan ögonblicksbild etiketten är **dailyhana** och antalet ögonblicksbilder med den här etiketten ska behållas är **28**. När du har besvarat förbrukningen av diskutrymme, kan du minska antalet lagrade ögonblicksbilder. Ett enkelt sätt att minska antalet ögonblicksbilder till 15, till exempel är att köra skriptet med den sista parametern inställd **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Om du kör skriptet med den här inställningen är antalet ögonblicksbilder, inklusive den nya storage-ögonblicksbilden 15. De senaste 15 ögonblicksbilderna hålls och 15 äldre ögonblicksbilder tas bort.

 >[!NOTE]
 > Det här skriptet minskar antalet ögonblicksbilder endast om det finns fler än 1 timme gamla ögonblicksbilder. Skriptet tar inte bort ögonblicksbilder som är mindre än 1 timme gamla. Dessa begränsningar är relaterade till de valfria disaster recovery-funktionerna som erbjuds.

Om du inte längre vill hantera en uppsättning ögonblicksbilder med prefixet säkerhetskopiering **dailyhana** i syntaxexemplen, kan du köra skriptet med **0** som kvarhållning tal. Alla ögonblicksbilder som matchar etiketten tas sedan bort. Ta bort alla ögonblicksbilder kan dock påverka funktionerna i HANA stora instanser disaster recovery-funktionen.

Ett andra alternativ att ta bort specifika ögonblicksbilder är att använda skriptet `azure_hana_snapshot_delete`. Det här skriptet är utformad för att ta bort en ögonblicksbild eller en uppsättning ögonblicksbilder antingen med hjälp av HANA säkerhetskopie-ID som hittades i HANA Studio eller via ögonblicksbild namnet i sig. Säkerhetskopie-ID är för närvarande endast knuten till ögonblicksbilder som skapats för den **hana** typ av ögonblicksbild. Ta en ögonblicksbild säkerhetskopior av typen **loggar** och **Start** utför inte en SAP HANA-ögonblicksbilder och så det finns inga säkerhetskopie-ID för dessa ögonblicksbilder. Om ögonblicksbild namnet anges efter alla ögonblicksbilder i olika volymer som matchar namnet på angivna ögonblicksbilden. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

För information om skriptet finns **”ta bort en ögonblicksbild - azure_hana_snapshot_delete'** i dokumentet [Microsoft ögonblicksbild verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Kör skriptet som användare **rot**.

>[!IMPORTANT]
>Om det finns data som endast finns på ögonblicksbilden tas som bort när ögonblicksbilden har tagits bort, att data går förlorade permanent.

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>På filnivå återställning från en ögonblicksbild för lagring

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
För ögonblicksbild typer **hana** och **loggar**, du kan få åtkomst till ögonblicksbilder direkt på volymer i den **.snapshot** directory. Det finns en underkatalog för var och en av ögonblicksbilder. Du kan kopiera varje fil i tillståndet den var i vid ögonblicksbild från den underkatalogen i den faktiska katalogstrukturen. I den aktuella versionen av skriptet finns **nr** återställa skript som finns för återställning från ögonblicksbilder som en självbetjäning (även om återställning från ögonblicksbilder kan utföras som en del av DR självbetjäning skript på DR-plats under en redundansväxling). Genom att öppna en tjänstbegäran för att återställa en önskad ögonblicksbild från de befintliga tillgängliga ögonblicksbilderna måste du kontakta Microsoft operations-teamet.

>[!NOTE]
>Med en fil fungerar inte återställningen för ögonblicksbilder av startavbildningen LUN som är oberoende av vilken typ av enheter för stora HANA-instansen. Den **.snapshot** directory exponeras inte i filen Boot.ini LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Återställ till den senaste HANA-ögonblicksbilden

I ett scenario med produktion ned kan hela återställningen från en ögonblicksbild för lagring av initieras som en kundincident med Microsoft Azure-supporten. Det är bara några hög angelägenhetsgrad om data har tagits bort i ett produktionssystem, och det enda sättet att hämta det är att återställa produktionsdatabasen.

I annat fall kan en point-in-time-återställning kan vara med låg angelägenhetsgrad och planerade dagar i förväg. Du kan planera återställningen med SAP HANA på Azure i stället för att höja flaggan hög prioritet. Du kan till exempel planerar att uppgradera SAP-program genom att använda ett nytt paket för förbättring. Du måste sedan återgå till en ögonblicksbild som representerar tillstånd innan paketuppgradering förbättring.

Innan du skickar begäran måste du förbereda. SAP HANA på Azure-teamet kan sedan hantera begäran och ge de återställda volymerna. Därefter kan återställa du HANA-databas baserat på ögonblicksbilder.

Möjligheterna att en ögonblicksbild återställs med nya verktyg som finns dokumenterade i avsnittet **”så här återställer du en ögonblicksbild '** dokumentets [manuell återställning Guide för SAP HANA på Azure Storage-ögonblicksbilder](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Följande visar hur du förbereder för begäran:

1. Bestäm vilken ögonblicksbild för att återställa. Endast hana/datavolymen återställs, såvida inte du instruera annars. 

1. Stänga av HANA-instans.

   ![Stäng av HANA-instans](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Demontera datavolymerna på varje nod för HANA-databas. Om datavolymerna är fortfarande monterade för operativsystemet, misslyckas återställningen av ögonblicksbilden.
   ![Avmontera datavolymerna på varje nod för HANA-databas](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Öppna en supportförfrågan för Azure och innehåller instruktioner för återställning av en specifik ögonblicksbild.

   - Under återställningen: SAP HANA på Azure kan be dig att delta i ett konferenssamtal så samordning, verifiering och bekräfta att rätt storage-ögonblicksbilder har återställts. 

   - Efter återställningen: SAP HANA på Azure-tjänsten meddelar dig när lagring ögonblicksbild har återställts.

1. När återställningsprocessen har slutförts, montera om alla datavolymer.

   ![Montera om alla datavolymer](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



En annan möjlighet för att hämta, till exempel SAP HANA-datafilerna har återställts från en ögonblicksbild av storage dokumenteras i steg 7 i dokumentet [manuell återställning Guide för SAP HANA på Azure Storage-ögonblicksbilder](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Dokumentet [manuell återställning Guide för SAP HANA på Azure Storage-ögonblicksbilder](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) visas av aktivitetssekvensen Återställ från ögonblicksbildsäkerhetskopia. Använd den dokumentationen för körningen av en återställning. 

>[!Note]
>Steg 7 är inte nödvändigt att köra om du har fått din ögonblicksbild som återställs av Microsoft operations.


### <a name="recover-to-another-point-in-time"></a>Återställa till en annan tidpunkt
Dokumentet [manuell återställning Guide för SAP HANA på Azure Storage-ögonblicksbilder](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) visas av aktivitetssekvensen Återställ till en viss punkt i tiden i avsnittet **”återställa databasen till följande punkt i tiden”**. Använd den dokumentationen för körningen av en återställning till en viss punkt i tiden. 


## <a name="next-steps"></a>Nästa steg
- Se [Disaster Recovery principer och förberedelse av](hana-concept-preparation.md).
