---
title: HANA säkerhetskopiering och återställning på SAP HANA på Azure (Stora instanser) | Microsoft-dokument
description: Så här utför du HANA-säkerhetskopiering och återställning på SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72430089"
---
# <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

>[!IMPORTANT]
>Den här artikeln ersätter inte SAP HANA-administrationsdokumentationen eller SAP Notes. Vi förväntar oss att du har en gedigen förståelse för och expertis inom SAP HANA administration och drift, särskilt för säkerhetskopiering, återställning, hög tillgänglighet och katastrofåterställning. I den här artikeln visas skärmdumpar från SAP HANA Studio. Innehåll, struktur och arten av skärmarna av SAP administrationsverktyg och verktygen själva kan ändras från SAP HANA release till release.

Det är viktigt att du tränar steg och processer som tas i din miljö och med dina HANA-versioner och utgåvor. Vissa processer som beskrivs i den här artikeln förenklas för en bättre allmän förståelse. De är inte avsedda att användas som detaljerade steg för eventuella drifthandböcker. Om du vill skapa åtgärdshandböcker för dina konfigurationer testar och testar du dina processer och dokumenterar processerna som är relaterade till dina specifika konfigurationer. 

En av de viktigaste aspekterna av driftdatabaser är att skydda dem från katastrofala händelser. Orsaken till dessa händelser kan vara allt från naturkatastrofer till enkla användarfel.

Säkerhetskopiering av en databas, med möjlighet att återställa den till vilken tidpunkt som helst, till exempel innan någon raderade kritiska data, möjliggör återställning till ett tillstånd som är så nära som möjligt till hur det var före avbrottet.

Två typer av säkerhetskopior måste utföras för att uppnå möjligheten att återställa:

- Säkerhetskopiering av databaser: Fullständiga, inkrementella eller differentiella säkerhetskopior
- Säkerhetskopiering av transaktionsloggar

Förutom säkerhetskopieringar i fullständiga databaser som utförs på programnivå kan du utföra säkerhetskopior med ögonblicksbilder av lagring. Ögonblicksbilder av lagring ersätter inte säkerhetskopior av transaktionsloggen. Säkerhetskopiering av transaktionsloggar är fortfarande viktiga för att återställa databasen till en viss tidpunkt eller för att tömma loggarna från redan genomförda transaktioner. Ögonblicksbilder av lagring kan påskynda återställningen genom att snabbt tillhandahålla en roll-forward-bild av databasen. 

SAP HANA på Azure (Stora instanser) erbjuder två alternativ för säkerhetskopiering och återställning:

- **Gör det själv (DIY).** När du har kontrollerat att det finns tillräckligt med diskutrymme kan du utföra fullständiga säkerhetskopieringar av databaser och loggar med hjälp av någon av följande metoder för säkerhetskopiering av diskar. Du kan säkerhetskopiera antingen direkt till volymer som är kopplade till HANA Large Instance-enheterna eller till NFS-resurser som har ställts in i en virtuell Azure-dator (VM). I det senare fallet konfigurerar kunder en Linux-virtuell dator i Azure, bifogar Azure Storage till den virtuella datorn och delar lagringen via en konfigurerad NFS-server i den virtuella datorn. Om du utför säkerhetskopian mot volymer som direkt kopplas till HANA Large Instance-enheter kopierar du säkerhetskopiorna till ett Azure-lagringskonto. Gör detta när du har konfigurerat en virtuell Azure-dator som exporterar NFS-resurser som baseras på Azure Storage. Du kan också använda antingen ett Azure Backup-valv eller Azure cold storage. 

   Ett annat alternativ är att använda ett dataskyddsverktyg från tredje part för att lagra säkerhetskopior när de har kopierats till ett Azure-lagringskonto. Diy backup alternativet kan också vara nödvändigt för data som du behöver lagra under längre tidsperioder för efterlevnad och granskning. I samtliga fall kopieras säkerhetskopiorna till NFS-resurser som representeras via en virtuell dator och Azure Storage.

- **Funktioner för säkerhetskopiering och återställning av infrastruktur.** Du kan också använda funktionerna för säkerhetskopiering och återställning som den underliggande infrastrukturen för SAP HANA på Azure (stora instanser) tillhandahåller. Det här alternativet uppfyller behovet av säkerhetskopior och snabb återställningar. Resten av det här avsnittet behandlar säkerhetskopierings- och återställningsfunktionen som erbjuds med STORA HANA-instanser. Det här avsnittet täcker också relationen som säkerhetskopierar och återställer har till funktionen för haveriberedskap som erbjuds av HANA Stora instanser.

> [!NOTE]
>   Ögonblicksbildtekniken som används av den underliggande infrastrukturen för STORA HANA-instanser har ett beroende av SAP HANA-ögonblicksbilder. Vid denna punkt, SAP HANA ögonblicksbilder fungerar inte tillsammans med flera klienter i SAP HANA multitenant databas behållare. Om bara en klient har distribuerats fungerar SAP HANA-ögonblicksbilder och du kan använda den här metoden.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Använda ögonblicksbilder av SAP HANA på Azure (stora instanser)

Lagringsinfrastrukturen som ligger till grund för SAP HANA på Azure (stora instanser) stöder ögonblicksbilder av lagring av volymer. Både säkerhetskopiering och återställning av volymer stöds, med följande överväganden:

- I stället för fullständiga säkerhetskopieringar av databasen tas ögonblicksbilder av lagringsvolymer ofta.
- När en ögonblicksbild utlöses över /hana/data och /hana/shared, som inkluderar /usr/sap, volymer, initierar ögonblicksbildtekniken en SAP HANA-ögonblicksbild innan den kör ögonblicksbilden av lagringen. Denna SAP HANA ögonblicksbild är installationspunkten för eventuella logg återställningar efter återställning av lagring ögonblicksbild. För att en HANA-ögonblicksbild ska lyckas behöver du en aktiv HANA-instans. I ett HSR-scenario stöds inte en ögonblicksbild av lagring på en aktuell sekundär nod där en HANA-ögonblicksbild inte kan utföras.
- När ögonblicksbilden av lagringen har körts tas SAP HANA-ögonblicksbilden bort.
- Säkerhetskopiering av transaktionsloggar tas ofta och lagras i volymen /hana/logbackups eller i Azure. Du kan utlösa volymen /hana/logbackups som innehåller säkerhetskopior av transaktionsloggen för att ta en ögonblicksbild separat. I så fall behöver du inte köra en HANA-ögonblicksbild.
- Om du måste återställa en databas till en viss tidpunkt, för ett produktionsavbrott, begär du att Microsoft Azure Support eller SAP HANA på Azure återställas till en viss ögonblicksbild av lagring. Ett exempel är en planerad återställning av ett sandlådesystem till dess ursprungliga tillstånd.
- SAP HANA-ögonblicksbilden som ingår i ögonblicksbilden av lagring är en förskjutningspunkt för att tillämpa säkerhetskopior av transaktionsloggen som kördes och lagrades efter att ögonblicksbilden av lagringen togs.
- Dessa transaktionsloggsäkerheter vidtas för att återställa databasen till en viss tidpunkt.

Du kan utföra ögonblicksbilder av lagring som inriktas på tre klasser av volymer:

- En kombinerad ögonblicksbild över /hana/data och /hana/shared, som innehåller /usr/sap. Den här ögonblicksbilden kräver att du skapar en SAP HANA-ögonblicksbild som förberedelse för ögonblicksbilden av lagringen. SAP HANA-ögonblicksbilden säkerställer att databasen är i ett konsekvent tillstånd från lagringssynpunkt. För återställningsprocessen är det en punkt att ställa in på.
- En separat ögonblicksbild över /hana/logbackups.
- En operativsystempartition.

Information om hur du hämtar de senaste ögonblicksbildskripten och dokumentationen finns i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). När du hämtar skriptpaketet för ögonblicksbilder från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)får du tre filer. En av filerna dokumenteras i en PDF-fil för de funktioner som tillhandahålls. När du har hämtat verktygsuppsättningen följer du instruktionerna i "Hämta verktygen för ögonblicksbilder".

## <a name="storage-snapshot-considerations"></a>Ögonblicksbilder av lagring

>[!NOTE]
>Ögonblicksbilder av lagring förbrukar lagringsutrymme som allokeras till HANA-enheter för stora instanser. Tänk på följande aspekter av schemaläggning lagring ögonblicksbilder och hur många ögonblicksbilder lagring att behålla. 

Den specifika mekaniken för ögonblicksbilder av lagring för SAP HANA på Azure (stora instanser) inkluderar:

- En specifik ögonblicksbild av lagring vid den tidpunkt då den tas förbrukar lite lagringsutrymme.
- När datainnehållet ändras och innehållet i SAP HANA-datafiler ändras på lagringsvolymen måste ögonblicksbilden lagra det ursprungliga blocketinnehållet och dataändringarna.
- Därför ökar ögonblicksbilden av lagringsögonblicksbilden i storlek. Ju längre ögonblicksbilden finns, desto större blir lagringsögonblicksbilden.
- Ju fler ändringar som görs i SAP HANA-databasvolymen under livslängden för en ögonblicksbild av lagring, desto större utrymmesförbrukning för lagringsögonblicksbilden.

SAP HANA på Azure (Stora instanser) levereras med fasta volymstorlekar för SAP HANA-data och loggvolymer. Utföra ögonblicksbilder av dessa volymer äter i din volym utrymme. Du måste:

- Bestäm när lagringsögonblicksbilder ska schemaläggas.
- Övervaka utrymmesförbrukningen för lagringsvolymerna. 
- Hantera antalet ögonblicksbilder som du lagrar. 

Du kan inaktivera ögonblicksbilder av lagring när du antingen importerar massor av data eller utför andra viktiga ändringar i HANA-databasen. 


Följande avsnitt innehåller information om hur du utför dessa ögonblicksbilder och innehåller allmänna rekommendationer:

- Även om maskinvaran kan upprätthålla 255 ögonblicksbilder per volym, vill du stanna långt under detta nummer. Rekommendationen är 250 eller mindre.
- Innan du utför ögonblicksbilder av lagring, övervaka och hålla reda på ledigt utrymme.
- Sänk antalet ögonblicksbilder av lagring baserat på ledigt utrymme. Du kan sänka antalet ögonblicksbilder som du behåller eller utöka volymerna. Du kan beställa ytterligare lagringsutrymme i 1 terabyteenheter.
- Under aktiviteter som att flytta data till SAP HANA med SAP-plattformsmigreringsverktyg (R3load) eller återställa SAP HANA-databaser från säkerhetskopior inaktiverar du ögonblicksbilder av lagring på /hana/datavolymen. 
- Undvik ögonblicksbilder av lagringsögonblicksbilder om möjligt under större omorganisationer av SAP HANA-tabeller.
- Ögonblicksbilder av lagring är en förutsättning för att dra nytta av haveriberedskapsfunktionerna i SAP HANA på Azure (stora instanser).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Förutsättningar för att använda ögonblicksbilder av lagring med självbetjäning

Kontrollera att ögonblicksbildskriptet körs korrekt genom att se till att Perl är installerat på Operativsystemet Linux på HANA-servern för stora instanser. Perl levereras förinstallerad på din HANA Large Instance-enhet. Så här kontrollerar du Perl-versionen:

`perl -v`

![Den offentliga nyckeln kopieras genom att köra det här kommandot](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Konfigurera ögonblicksbilder av lagring

Så här konfigurerar du ögonblicksbilder av lagring med STORA HANA-instanser.
1. Kontrollera att Perl är installerat på Operativsystemet Linux på HANA Large Instances-servern.
1. Ändra /etc/ssh/ssh\_config för att lägga till raden _MACs hmac-sha1_.
1. Skapa ett SAP HANA-användarkonto för säkerhetskopiering på huvudnoden för varje SAP HANA-instans som du kör, om tillämpligt.
1. Installera SAP HANA HDB-klienten på alla SAP HANA Large Instances-servrar.
1. På den första SAP HANA-servern för stora instanser i varje region skapar du en offentlig nyckel för att komma åt den underliggande lagringsinfrastrukturen som styr skapandet av ögonblicksbilder.
1. Kopiera skript och konfigurationsfil från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) till platsen för **hdbsql** i SAP HANA-installationen.
1. Ändra *filen HANABackupDetails.txt* om det behövs för lämpliga kundspecifikationer.

Hämta de senaste ögonblicksbildskripten och dokumentationen från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Steg som anges tidigare finns i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Ersättning för MCOD-scenarier
Om du kör ett [MCOD-scenario](https://launchpad.support.sap.com/#/notes/1681092) med flera SAP HANA-instanser på en HANA-enhet för stor instans, har du separata lagringsvolymer etablerade för var och en av SAP HANA-instanserna. Mer information om MDC och andra överväganden finns i "Viktiga saker att komma ihåg" i [Microsofts verktyg för ögonblicksbilder för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Steg 1: Installera SAP HANA HDB-klienten

Linux-operativsystemet som är installerat på SAP HANA på Azure (stora instanser) innehåller de mappar och skript som krävs för att köra SAP HANA-lagringsögonblicksbilder för säkerhetskopiering och haveriberedskap. Sök efter nyare versioner i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Det är ditt ansvar att installera SAP HANA HDB-klienten på HANA Large Instance-enheterna medan du installerar SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Steg 2: Ändra /etc/ssh/ssh\_config

Det här steget beskrivs i "Aktivera kommunikation med lagring" i [Microsoft-ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Steg 3: Skapa en offentlig nyckel

Om du vill aktivera åtkomst till lagringsögonblicksgränssnitten för din HANA Large Instance-klient skapar du en inloggningsprocedur via en offentlig nyckel. 

På den första SAP HANA på Azure-servern (Stora instanser) i din klientorganisation skapar du en offentlig nyckel för att komma åt lagringsinfrastrukturen. Med en offentlig nyckel krävs inget lösenord för att logga in på minnesgränssnitten för lagring. Du behöver inte heller underhålla lösenordsuppgifter med en offentlig nyckel. 

Information om hur du skapar en offentlig nyckel finns i "Aktivera kommunikation med lagring" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Steg 4: Skapa ett SAP HANA-användarkonto

Om du vill starta skapandet av SAP HANA-ögonblicksbilder skapar du ett användarkonto i SAP HANA som skript för ögonblicksbilder av lagring kan använda. Skapa ett SAP HANA-användarkonto i SAP HANA Studio för detta ändamål. Användaren måste skapas under SYSTEMDB och *inte* under SID-databasen för MDC. I den enda behållarmiljön skapas användaren i klientdatabasen. Det här kontot måste ha **behörigheten Säkerhetskopieringsadministratör** och **katalogläsning.** 

Information om hur du konfigurerar och använder ett användarkonto finns i Aktivera kommunikation med SAP HANA i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Steg 5: Auktorisera SAP HANA-användarkontot

I det här steget godkänner du det SAP HANA-användarkonto som du har skapat så att skripten inte behöver skicka lösenord vid körning. SAP HANA-kommandot `hdbuserstore` gör det möjligt att skapa en SAP HANA-användarnyckel. Nyckeln lagras på en eller flera SAP HANA-noder. Användarnyckeln låter användaren komma åt SAP HANA utan att behöva hantera lösenord inifrån skriptprocessen. Skriptprocessen beskrivs senare i den här artikeln.

>[!IMPORTANT]
>Kör dessa konfigurationskommandon med samma användarkontext som ögonblicksbildkommandona körs i. Annars fungerar inte ögonblicksbildkommandona korrekt.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Steg 6: Hämta ögonblicksbildskripten, konfigurera ögonblicksbilderna och testa konfigurationen och anslutningen

Hämta den senaste versionen av skripten från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). Hur skripten installeras ändras med release 4.1 av skripten. Mer information finns i "Aktivera kommunikation med SAP HANA" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Den exakta sekvensen av kommandon finns i "Enkel installation av ögonblicksbildverktyg (standard)" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Vi rekommenderar att standardinstallationen används. 

Information om hur du uppgraderar från version 3.x till 4.1 finns i "Uppgradera en befintlig installation" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Information om hur du avinstallerar verktygsuppsättningen 4.1 finns i "Avinstallation av verktyg för ögonblicksbilder" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Glöm inte att köra stegen som beskrivs i "Fullständig installation av ögonblicksbildverktyg" i [Microsofts snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Syftet med de olika skript och filer som de fick installerat beskrivs i "Vilka är dessa ögonblicksbild verktyg?" i [Microsoft snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Innan du konfigurerar verktygen för ögonblicksbilder kontrollerar du att du även har konfigurerat HANA-säkerhetskopieringsplatser och inställningar korrekt. Mer information finns i "SAP HANA-konfiguration" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Konfigurationen av verktygsuppsättningen för ögonblicksbild beskrivs i "Config file - HANABackupCustomerDetails.txt" i [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Testa anslutning med SAP HANA

När du har placerat alla konfigurationsdata i filen *HANABackupCustomerDetails.txt* kontrollerar du om konfigurationerna är korrekta för HANA-instansdata. Använd skriptet `testHANAConnection`, som är oberoende av en SAP HANA-skalnings- eller skalningskonfiguration.

Mer information finns i "Kontrollera anslutningen med SAP HANA - testHANAConnection" i [Microsoft snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Testa lagringsanslutning

Nästa teststeg är att kontrollera anslutningen till lagringen baserat på de data du lägger in i *konfigurationsfilen HANABackupCustomerDetails.txt.* Kör sedan en testögonblicksbild. Innan du `azure_hana_backup` kör kommandot måste du köra det här testet. Sekvensen av kommandon för det här testet finns i "Kontrollera anslutning med lagring - testStorageSnapshotConnection" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Efter en lyckad inloggning till lagringsgränssnitten för virtuella datorer fortsätter skriptet med fas 2 och skapar en ögonblicksbild av test. Utdata visas här för en utskalningskonfiguration med tre noder av SAP HANA.

Om testögonblicksbilden körs med skriptet kan du schemalägga de faktiska ögonblicksbilderna av lagring. Om det inte lyckas, undersöka problemen innan du går vidare. Testögonblicksbilden bör stanna kvar tills de första riktiga ögonblicksbilderna är klara.


### <a name="step-7-perform-snapshots"></a>Steg 7: Utföra ögonblicksbilder

När förberedelsestegen är klara kan du börja konfigurera och schemalägga de faktiska ögonblicksbilderna av lagring. Skriptet som ska schemaläggas fungerar med SAP HANA-skalnings- och skalningskonfigurationer. För periodisk och regelbunden körning av säkerhetskopieringsskriptet schemalägger du skriptet med hjälp av cron-verktyget. 

Den exakta kommandosyntaxen och funktionen finns i "Utför säkerhetskopiering av ögonblicksbilder – azure_hana_backup" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

När skriptet `azure_hana_backup` körs skapas ögonblicksbilden av lagringen i följande tre faser:

1. Den kör en SAP HANA-ögonblicksbild.
1. Den kör en ögonblicksbild av lagring.
1. Den tar bort SAP HANA-ögonblicksbilden som skapades innan lagringsögonblicksbilden kördes.

Om du vill köra skriptet anropar du det från den KÖRBARA HDB-mappen som det kopierades till. 

Kvarhållningsperioden administreras med antalet ögonblicksbilder som skickas som en parameter när du kör skriptet. Den tid som täcks av ögonblicksbilder av lagring är en funktion av körningsperioden och antalet ögonblicksbilder som skickas som en parameter när skriptet körs. 

Om antalet ögonblicksbilder som behålls överskrider det antal som namnges som en parameter i anropet av skriptet, tas den äldsta lagringsögonblicksbilden av samma etikett bort innan en ny ögonblicksbild körs. Numret du anger som den sista parametern i anropet är det nummer som du kan använda för att styra antalet ögonblicksbilder som behålls. Med det här numret kan du också indirekt styra det diskutrymme som används för ögonblicksbilder. 


## <a name="snapshot-strategies"></a>Strategier för ögonblicksbilder
Frekvensen av ögonblicksbilder för de olika typerna beror på om du använder hagen för hana-storinstanskatastrofåterställning. Den här funktionen är beroende av ögonblicksbilder av lagring, vilket kan kräva särskilda rekommendationer för frekvens- och körningsperioderna för lagringsögonblicksbilderna. 

I de överväganden och rekommendationer som följer är antagandet att du *inte* använder den haveriberedskapsfunktion som HANA Stora instanser erbjuder. I stället använder du ögonblicksbilder av lagring för att ha säkerhetskopior och kunna tillhandahålla point-in-time-återställning för de senaste 30 dagarna. Med tanke på begränsningarna i antalet ögonblicksbilder och utrymme bör du tänka på följande krav:

- Återställningstiden för tidsåterställning.
- Det utrymme som används.
- Målen för återställningspunkten och återställningstiden för potentiell återhämtning från en katastrof.
- Den slutliga körningen av HANA full-databas säkerhetskopior mot diskar. När en fullständig databassäkerhetskopiering mot diskar eller **backint-gränssnittet** utförs misslyckas körningen av ögonblicksbilder av lagring. Om du planerar att köra säkerhetskopieringar i full databas ovanpå ögonblicksbilder av lagring kontrollerar du att körningen av ögonblicksbilder av lagring är inaktiverad under den här tiden.
- Antalet ögonblicksbilder per volym, som är begränsat till 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Om du inte använder funktionen för haveriberedskap i STORA HANA-instanser är ögonblicksbildperioden mindre frekvent. I sådana fall utför du de kombinerade ögonblicksbilderna på /hana/data och /hana/shared, som inkluderar /usr/sap, i 12-timmarsperioder eller 24-timmarsperioder. Behåll ögonblicksbilderna i en månad. Detsamma gäller för ögonblicksbilder av loggen backup volym. Körningen av SAP HANA transaktionslogg säkerhetskopior mot loggen backup volymen sker i 5-minuters till 15-minuters perioder.

Schemalagda ögonblicksbilder av lagring utförs bäst med hjälp av cron. Använd samma skript för alla säkerhetskopior och katastrofåterställningsbehov. Ändra skriptindata för att matcha de olika begärda säkerhetskopieringstiderna. Dessa ögonblicksbilder är alla schemalagda på olika sätt i cron beroende på deras körningstid. Det kan vara varje timme, var 12 timmar, dagligen eller veckovis. 

Följande exempel visar ett cron-schema i /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
I föregående exempel täcker en per timme kombinerad ögonblicksbild de volymer som innehåller /hana/data och /hana/shared/SID, som inkluderar /usr/sap, platser. Använd den här typen av ögonblicksbild för en snabbare point-in-time-återställning under de senaste två dagarna. Det finns också en daglig ögonblicksbild på dessa volymer. Så, du har två dagars täckning av varje timme ögonblicksbilder plus fyra veckors täckning av dagliga ögonblicksbilder. Säkerhetskopieringsvolymen för transaktionsloggen säkerhetskopieras också dagligen. Dessa säkerhetskopior hålls i fyra veckor. 

Som du ser i den tredje raden av crontab, är säkerhetskopian av HANA transaktionsloggen schemalagd att köras var 5 minuter. Starttiderna för de olika cron-jobb som kör ögonblicksbilder av lagring fördelas. På så sätt körs inte ögonblicksbilderna på en gång vid en viss tidpunkt. 

I följande exempel utför du en kombinerad ögonblicksbild som täcker de volymer som innehåller /hana/data och /hana/shared/SID, som inkluderar /usr/sap, platser per timme. Du behåller dessa ögonblicksbilder i två dagar. Ögonblicksbilderna av säkerhetskopieringsvolymerna för transaktionsloggen körs på 5-minuters basis och sparas i fyra timmar. Precis som tidigare beräknas säkerhetskopieringen av HANA-transaktionsloggfilen köras var femte minut. 

Ögonblicksbilden av säkerhetskopieringsvolymen för transaktionsloggen utförs med en 2-minuters fördröjning när säkerhetskopieringen av transaktionsloggen har startat. Under normala omständigheter avslutas sap hana-transaktionsloggens säkerhetskopiering inom dessa 2 minuter. Som tidigare säkerhetskopieras volymen som innehåller lun-startstarten en gång per dag av en ögonblicksbild av lagring och sparas i fyra veckor.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Följande bild illustrerar sekvenserna i föregående exempel. Start-LUN är utesluten.

![Relation mellan säkerhetskopior och ögonblicksbilder](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA utför regelbundna skrivningar mot /hana/log-volymen för att dokumentera de bekräftade ändringarna i databasen. Sap HANA skriver regelbundet en savepoint till /hana/datavolymen. Som anges i crontab körs en SAP HANA-transaktionsloggsäkerhet var 5:e minut. 

Du ser också att en SAP HANA-ögonblicksbild körs varje timme som ett resultat av att utlösa en kombinerad ögonblicksbild av lagring över volymerna /hana/data och /hana/shared/SID. När HANA-ögonblicksbilden har lyckats körs den kombinerade ögonblicksbilden av lagring. Enligt instruktioner i crontab körs lagringsögonblicksbilden på volymen /hana/logbackup var 5:e minut, cirka 2 minuter efter säkerhetskopieringen av HANA-transaktionsloggen.

> 

>[!IMPORTANT]
> Användningen av ögonblicksbilder av lagring för SAP HANA-säkerhetskopior är endast värdefull när ögonblicksbilderna utförs tillsammans med SAP HANA-transaktionsloggsäkerheter. Dessa transaktionsloggsäkerheter måste täcka tidsperioderna mellan ögonblicksbilderna av lagring. 

Om du har angett ett åtagande för användare av en point-in-time-återställning på 30 dagar måste du:

- Få tillgång till en kombinerad ögonblicksbild av lagring över /hana/data och /hana/shared/SID som är 30 dagar gammal, i extrema fall. 
- Har sammanhängande säkerhetskopior transaktionslogg som täcker tiden mellan någon av de kombinerade ögonblicksbilderna av lagring. Så, den äldsta ögonblicksbilden av transaktionsloggen backup volym måste vara 30 dagar gammal. Detta är inte fallet om du kopierar säkerhetskopior av transaktionsloggen till en annan NFS-resurs som finns på Azure Storage. I så fall kan du hämta gamla säkerhetskopior av transaktionsloggen från NFS-resursen.

Om du vill dra nytta av ögonblicksbilder av lagring och eventuell lagringsreplikering av säkerhetskopior av transaktionsloggen ändrar du platsen som SAP HANA skriver säkerhetskopior av transaktionsloggen. Du kan göra den här ändringen i HANA Studio. 

Även om SAP HANA säkerhetskopierar fullständiga loggsegment automatiskt anger du ett loggsäkerhetsintervall som ska vara deterministiskt. Detta gäller särskilt när du använder alternativet för haveriberedskap eftersom du vanligtvis vill köra loggsäkerhetskopior med en deterministisk period. I följande fall anges 15 minuter som logbackupintervall.

![Schemalägg SAP HANA-säkerhetskopieringsloggar i SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Du kan också välja säkerhetskopior som är vanligare än var 15:e minut. En mer frekvent inställning används ofta tillsammans med funktioner för haveriberedskap för STORA HANA-instanser. Vissa kunder utför säkerhetskopiering av transaktionsloggar var femte minut.

Om databasen aldrig har säkerhetskopierats är det sista steget att utföra en filbaserad databassäkerhetskopiering för att skapa en enda säkerhetskopia som måste finnas i säkerhetskopieringskatalogen. Annars kan SAP HANA inte initiera angivna loggsäkerhetskopior.

![Skapa en filbaserad säkerhetskopia för att skapa en enda säkerhetskopia](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


När dina första lyckade lagringsögonblicksbilder har körts tar du bort testögonblicksbilden som kördes i steg 6. Mer information finns i "Ta bort ögonblicksbilder av test - ta bortTestStorageSnapshot" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Övervaka antalet och storleken på ögonblicksbilder på diskvolymen

På en viss lagringsvolym kan du övervaka antalet ögonblicksbilder och lagringsförbrukningen för dessa ögonblicksbilder. Kommandot `ls` visar inte ögonblicksbildkatalogen eller filerna. Kommandot `du` Linux OS visar information om dessa ögonblicksbilder av lagring eftersom de lagras på samma volymer. Använd kommandot med följande alternativ:

- `du –sh .snapshot`: Det här alternativet innehåller totalt antal ögonblicksbilder i ögonblicksbildkatalogen.
- `du –sh --max-depth=1`: Det här alternativet visar alla ögonblicksbilder som sparas i **mappen .snapshot** och storleken på varje ögonblicksbild.
- `du –hc`: Det här alternativet anger den totala storleken som används av alla ögonblicksbilder.

Använd dessa kommandon för att se till att ögonblicksbilder som tas och lagras inte förbrukar all lagring på volymerna.

>[!NOTE]
>Ögonblicksbilderna av start-LUN visas inte med föregående kommandon.

### <a name="get-details-of-snapshots"></a>Få information om ögonblicksbilder
Om du vill ha mer information `azure_hana_snapshot_details`om ögonblicksbilder använder du skriptet . Du kan köra skriptet på båda platserna om det finns en aktiv server på platsen för haveriberedskap. Skriptet innehåller följande utdata, uppdelade efter varje volym som innehåller ögonblicksbilder: 
   * Storleken på de totala ögonblicksbilderna i en volym
   * Följande information i varje ögonblicksbild i den volymen: 
      - Namn på ögonblicksbild 
      - Skapa tid 
      - Ögonblicksbildens storlek
      - Ögonblicksbildens frekvens
      - HANA Backup ID som är associerat med ögonblicksbilden, om det är relevant

Syntax för kommandot och utdata finns i "List snapshots - azure_hana_snapshot_details" i [Microsoft snapshot tools for SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Minska antalet ögonblicksbilder på en server

Som tidigare förklarats kan du minska antalet vissa etiketter för ögonblicksbilder som du lagrar. De två sista parametrarna för kommandot för att initiera en ögonblicksbild är etiketten och antalet ögonblicksbilder som du vill behålla.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

I föregående exempel är ögonblicksbildetiketten **dailyhana**. Antalet ögonblicksbilder med den här etiketten som ska behållas är **28**. När du svarar på diskutrymmesförbrukningen kanske du vill minska antalet lagrade ögonblicksbilder. Ett enkelt sätt att minska antalet ögonblicksbilder till 15, till exempel, är att köra skriptet med den sista parametern inställd på **15:**

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Om du kör skriptet med den här inställningen är antalet ögonblicksbilder, som innehåller den nya ögonblicksbilden av lagring, 15. De 15 senaste ögonblicksbilderna sparas och de 15 äldre ögonblicksbilderna tas bort.

 >[!NOTE]
 > Det här skriptet minskar antalet ögonblicksbilder endast om det finns ögonblicksbilder som är mer än en timme gamla. Skriptet tar inte bort ögonblicksbilder som är mindre än en timme gamla. Dessa begränsningar är relaterade till den valfria funktionen för haveriberedskap som erbjuds.

Om du inte längre vill behålla en uppsättning ögonblicksbilder med prefixet för säkerhetskopiering **dailyhana** i syntaxexemplen kör du skriptet med **0** som kvarhållningsnummer. Alla ögonblicksbilder som matchar den etiketten tas sedan bort. Om du tar bort alla ögonblicksbilder kan det påverka funktionerna för hana-funktioner för stora instanser haveriberedskap.

Ett andra alternativ för att ta bort `azure_hana_snapshot_delete`specifika ögonblicksbilder är att använda skriptet . Det här skriptet är utformat för att ta bort en ögonblicksbild eller uppsättning ögonblicksbilder antingen med hjälp av HANA-säkerhetskopierings-ID som finns i HANA Studio eller genom själva ögonblicksbildnamnet. För närvarande är säkerhetskopierings-ID endast kopplat **hana** till ögonblicksbilder som skapats för hana-ögonblicksbildtypen. Ögonblicksbild säkerhetskopior av typen **loggar** och **starta** inte utföra en SAP HANA ögonblicksbild, så det finns inget säkerhetskopierings-ID som finns för dessa ögonblicksbilder. Om ögonblicksbildnamnet anges letar den efter alla ögonblicksbilder på de olika volymer som matchar det angivna ögonblicksbildnamnet. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Mer information om skriptet finns i "Ta bort en ögonblicksbild - azure_hana_snapshot_delete" i [Microsofts ögonblicksbildverktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Kör skriptet som **användarrot**.

>[!IMPORTANT]
>Om det bara finns data på ögonblicksbilden som du planerar att ta bort går dessa data förlorade för alltid när ögonblicksbilden har tagits bort.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Återställning på filnivå från en ögonblicksbild av lagring

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
För ögonblicksbildtyper **hana** och **loggar**kan du komma åt ögonblicksbilder direkt på volymerna i **.snapshot** katalogen. Det finns en underkatalog för var och en av ögonblicksbilderna. Kopiera varje fil i det tillstånd den befann sig i vid punkten för ögonblicksbilden från den underkatalogen till den faktiska katalogstrukturen. 

I den aktuella versionen av skriptet finns det *inget* återställningsskript för återställning av ögonblicksbilder som självbetjäning. Återställning av ögonblicksbilder kan utföras som en del av självbetjäningsskript för haveriberedskap på katastrofåterställningsplatsen under redundans. Om du vill återställa en önskad ögonblicksbild från befintliga tillgängliga ögonblicksbilder måste du kontakta Microsofts driftteam genom att öppna en tjänstbegäran.

>[!NOTE]
>Enkel filåterställning fungerar inte för ögonblicksbilder av start-LUN-oberoende av typen av HANA-enheter för stora instanser. **.snapshot-katalogen** visas inte i lun för start. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Återställ till den senaste HANA-ögonblicksbilden

I ett scenario med produktionsstopp kan processen för att återställa från en ögonblicksbild av lagring startas som en kundincident med Microsoft Azure Support. Det är en brådskande fråga om data har tagits bort i ett produktionssystem och det enda sättet att hämta det är att återställa produktionsdatabasen.

I en annan situation kan en återhämtning i tid vara låg brådskande och planerade dagar i förväg. Du kan planera den här återställningen med SAP HANA på Azure i stället för att höja en flagga med hög prioritet. Du kan till exempel planera att uppgradera SAP-programvaran genom att använda ett nytt förbättringspaket. Du måste sedan återgå till en ögonblicksbild som representerar tillståndet före uppgraderingen av förbättringspaketet.

Innan du skickar begäran måste du förbereda dig. SAP HANA i Azure-teamet kan sedan hantera begäran och tillhandahålla de återställda volymerna. Därefter återställer du HANA-databasen baserat på ögonblicksbilderna.

Möjligheterna att få en ögonblicksbild återställd med den nya verktygsuppsättningen finns i "Så här återställer du en ögonblicksbild" i [manuell återställningsguide för SAP HANA på Azure från en ögonblicksbild av lagring](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

SÃ¥ att förbereda för begäran följer du dessa steg.

1. Bestäm vilken ögonblicksbild som ska återställas. Endast hana/datavolymen återställs om du inte instruerar något annat. 

1. Stäng av HANA-instansen.

   ![Stänga av HANA-instansen](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Avmontera datavolymerna på varje HANA-databasnod. Om datavolymerna fortfarande är monterade i operativsystemet misslyckas återställningen av ögonblicksbilden.

   ![Avmontera datavolymerna på varje HANA-databasnod](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Öppna en Azure-supportbegäran och inkludera instruktioner om återställning av en viss ögonblicksbild:

   - Under återställningen: SAP HANA på Azure Service kan be dig att delta i ett konferenssamtal för att samordna, verifiera och bekräfta att rätt lagringsögonblicksbild återställs. 

   - Efter återställningen: SAP HANA på Azure Service meddelar dig när ögonblicksbilden av lagring återställs.

1. När återställningsprocessen är klar monterar du om alla datavolymer.

   ![Återmontera alla datavolymer](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



En annan möjlighet för att få, till exempel, SAP HANA-datafiler som återställts från en ögonblicksbild av lagring, dokumenteras i steg 7 i [manuell återställningsguide för SAP HANA på Azure från en ögonblicksbild av lagring](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Information om hur du återställer från en säkerhetskopiering av ögonblicksbilder finns i [Guide för manuell återställning för SAP HANA på Azure från en ögonblicksbild av lagring](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Om ögonblicksbilden har återställts av Microsoft-åtgärder behöver du inte göra steg 7.


### <a name="recover-to-another-point-in-time"></a>Återställ till en annan tidpunkt
Information om hur du återställer till en viss tidpunkt finns i "Återställ databasen till följande tidpunkt" i [guide för manuell återställning för SAP HANA på Azure från en ögonblicksbild av lagring](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


## <a name="next-steps"></a>Nästa steg
- Se [Principer för katastrofåterställning och förberedelse](hana-concept-preparation.md).
