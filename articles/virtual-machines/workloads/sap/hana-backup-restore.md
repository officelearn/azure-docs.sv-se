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
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 912d4e1af3e1a4d07efddafa38bda5697b226787
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44392278"
---
# <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

>[!IMPORTANT]
>Den här dokumentationen är ingen ersättning av dokumentationen för administration av SAP HANA eller SAP Notes. Det förväntas att läsaren har en djupare förståelse av och expertis i SAP HANA-administration och åtgärder, särskilt med ämnen för säkerhetskopiering, återställning, hög tillgänglighet och katastrofåterställning. I den här dokumentationen visas skärmbilder från SAP HANA-Studio. Innehåll, struktur och arten av de olika stegen i SAP-verktyg för fjärrserveradministration och verktyg själva ändras från SAP HANA version till version.

Det är viktigt att du arbeta med steg och processer som tas i miljön och med dina HANA-versioner och versioner. Vissa processer som beskrivs i den här dokumentationen är förenklad för en bättre förståelse och är inte avsedda att användas som detaljerade anvisningar för slutlig åtgärden handböcker. Om du vill skapa åtgärden handböcker för dina konfigurationer måste du testa och arbeta med dina processer och dokumentera de processer som rör dina specifika konfigurationer. 

En av de viktigaste aspekterna till operativsystem databaser är att skydda dem mot katastrofhändelser. Orsaken till de här händelserna kan vara allt från enkla användarfel vid naturkatastrofer.

Säkerhetskopiera en databas, med möjlighet att återställa den till valfri punkt i tiden (till exempel innan någon bort viktiga data), gör det möjligt för återställning till ett tillstånd som är så nära som möjligt till det som finns innan du kan avbrott.

Två typer av säkerhetskopior måste utföras för bästa resultat:

- Säkerhetskopior: fullständiga, inkrementella eller differentiella säkerhetskopieringar
- Säkerhetskopieringar av transaktionsloggen

Förutom fullständig--databassäkerhetskopieringar som utförs på en nivå för programmet, kan du utföra säkerhetskopiering med ögonblicksbilder av lagring. Ögonblicksbilder av lagring ersätter inte säkerhetskopieringar av transaktionsloggen. Säkerhetskopieringar av transaktionsloggen är viktigt att återställa databasen till en viss punkt eller till tomt loggar från redan genomförda transaktioner. Storage-ögonblicksbilder kan dock snabbare återställning genom att snabbt tillhandahålla en sammanslagning och tydlig bild av databasen. 

SAP HANA på Azure (stora instanser) erbjuder två alternativ för säkerhetskopiering och återställning:

- Gör det själv (DIY). När du beräknar för att säkerställa att det finns tillräckligt med ledigt diskutrymme, utföra fullständig databasen och loggsäkerhetskopior med någon av följande metoder för disk-säkerhetskopiering. Du kan säkerhetskopiera antingen direkt till volymer som är ansluten till stora HANA-instansen enheter eller att Network File-resurser (NFS) och som har ställts in på Azure-datorer (VM). Koppla Azure Storage till den virtuella datorn i det senare fallet kan kunder konfigurera en Linux-VM i Azure, och dela lagringsutrymme via en NFS-server som är konfigurerade i den virtuella datorn. Om du utför säkerhetskopiering mot volymer som kopplas direkt till stora HANA-instansen enheter kan behöva du kopiera säkerhetskopior av till ett Azure storage-konto (när du har konfigurerat en Azure-dator som exporterar NFS-resurser som är baserade på Azure Storage). Du kan också använda en Azure backup-valv eller Azure kall lagring. 

   Ett annat alternativ är att använda ett verktyg för tredje parts data protection säkerhetskopiorna för när de kopieras till ett Azure storage-konto. Gör det själv säkerhetskopieringsalternativ kan också vara nödvändigt för data som du behöver lagra under längre tidsperioder för efterlevnad och granskning. I samtliga fall kopieras säkerhetskopiorna till NFS-resurser via en virtuell dator och Azure Storage som representeras.

- Infrastruktur för säkerhetskopiering och återställningsfunktionen. Du kan också säkerhetskopiera och återställa kärnfunktioner där den underliggande infrastrukturen med SAP HANA på Azure (stora instanser). Det här alternativet uppfyller behovet av säkerhetskopieringar och snabb återställning. Resten av det här avsnittet adresser säkerhetskopiering och återställning av funktionerna som erbjuds med stora HANA-instanser. Det här avsnittet beskriver också relation säkerhetskopiering och återställning måste katastrofen recovery-funktionerna som erbjuds av stora HANA-instanser.

>   [!NOTE]
>   Den ögonblicksbild-teknik som används av den underliggande infrastrukturen med stora HANA-instanser har ett beroende på SAP HANA-ögonblicksbilder. SAP HANA-ögonblicksbilder fungerar då inte tillsammans med flera innehavare av behållare för SAP HANA-databas för flera innehavare. Om bara en klient har distribuerats, SAP HANA-ögonblicksbilder fungerar och den här metoden kan användas.

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Med hjälp av ögonblicksbilder av lagring av SAP HANA på Azure (stora instanser)

Lagringsinfrastruktur som underliggande SAP HANA på Azure (stora instanser) har stöd för storage-ögonblicksbilder av volymer. Både säkerhetskopierings- och återställa volymer stöds, med följande överväganden:

- I stället för fullständiga databassäkerhetskopieringar tas storage-ögonblicksbilder regelbundet.
- När du utlöser en ögonblicksbild över /hana/data och /hana/shared (inklusive /usr/sap) volymer, teknik för ögonblicksbilder initierar en SAP HANA ögonblicksbild innan den kan köras storage-ögonblicksbilder. SAP HANA-ögonblicksbilder är den installationen för slutlig log återställningar efter återställning av storage-ögonblicksbilder. För HANA-ögonblicksbilder ska lyckas måste en aktiv HANA-instans.  I HSR scenariot stöds storage-ögonblicksbilder inte på aktuella sekundära noden där HANA-ögonblicksbilder inte kan utföras.
- När storage-ögonblicksbilder har har körts, tas SAP HANA-ögonblicksbilder bort.
- Säkerhetskopieringar av transaktionsloggen tas ofta och lagras i /hana/logbackups volymen eller i Azure. Du kan utlösa /hana/logbackups volymen som innehåller säkerhetskopieringarna av transaktionsloggen för att ta en ögonblicksbild separat. I så fall behöver du inte köra en HANA-ögonblicksbilder.
- Om du måste återställa en databas till en viss punkt i tiden, begära att Microsoft Azure-supporten (för en produktion avbrott) eller SAP HANA på Azure Service Management-återställning till en viss storage-ögonblicksbild. Ett exempel är en planerad återställning av en sandbox-system till det ursprungliga tillståndet.
- SAP HANA-ögonblicksbilder som ingår i storage-ögonblicksbilder är en förskjutning för att tillämpa säkerhetskopieringar av transaktionsloggen som har körts och lagrat när lagring ögonblicksbilden togs.
- Dessa säkerhetskopieringar av transaktionsloggen kommer att återställa databasen till en viss punkt i tiden.

Du kan utföra storage-ögonblicksbilder som riktar in sig på tre klasser av volymer:

- En kombinerad ögonblicksbild över/hana/data samt /hana/shared (inklusive sap/usr /). Den här ögonblicksbilden kräver skapandet av en SAP HANA-ögonblicksbilder som förberedelse för storage-ögonblicksbilder. SAP HANA-ögonblicksbilder ser till att databasen är i ett konsekvent tillstånd från en storage synsätt. Och att för återställningen bearbeta det vill säga en tidpunkt att ställa in dig på.
- En separat ögonblicksbild över/hana/logbackups.
- En partition av operativsystemet.

Få de senaste ögonblicksbild skript och dokumentation från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). 

## <a name="storage-snapshot-considerations"></a>Överväganden för ögonblicksbilder av lagring

>[!NOTE]
>Storage-ögonblicksbilder använda lagringsutrymme som har allokerats till stora HANA-instansen-enheter. Du behöver tänka på följande aspekter för schemaläggning av ögonblicksbilder av lagring och hur många storage-ögonblicksbilder som ska lagras. 

Specifika säkerhetsnivån för storage-ögonblicksbilder för SAP HANA på Azure (stora instanser) är:

- En ögonblicksbild av specifika lagringsutrymmen (vid tidpunkten när den tas) förbrukar lite lagringsutrymme.
- Ögonblicksbilden behöver lagra det ursprungliga innehållet i block, samt dataändringarna som innehållsändringar för data och innehållet i SAP HANA-data filer ändras på lagringsvolymen.
- Därför ökar storage-ögonblicksbilder i storlek. Ju längre ögonblicksbilden finns, desto större blir storage-ögonblicksbilder.
- Flera ändringar som görs till SAP HANA-databasvolymen över livslängden för en storage-ögonblicksbilder, den större utrymme för storage-ögonblicksbilder.

SAP HANA på Azure (stora instanser) levereras med fast Volymstorlekar för data och loggvolymer för SAP HANA. Utför ögonblicksbilder av volymerna eats i utrymmet på volymen. Du måste avgöra när du schemalägger ögonblicksbilder av lagring. Du måste också övervaka förbrukningen av diskutrymme för lagringsvolymer, samt hantera antalet ögonblicksbilder som du lagrar. Du kan inaktivera storage-ögonblicksbilder när du importerar stora mängder data eller utföra andra viktiga ändringar till HANA-databas. 


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
1. Kopiera skript och konfigurationsfilen från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) till platsen för **hdbsql** i SAP HANA-installationen.
1. Ändra den *HANABackupDetails.txt* fil som krävs för lämplig kund-specifikationer.

Få de senaste ögonblicksbild skript och dokumentation från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). 

### <a name="consideration-for-mcod-scenarios"></a>Överväganden för MCOD scenarier
Om du kör en [MCOD scenariot](https://launchpad.support.sap.com/#/notes/1681092) med flera SAP HANA-instanser på en enhet för stora HANA-instansen, har du separata lagringsvolymer som tillhandahållits för var och en av SAP HANA-instanser. Du kan inte initiera separat ögonblicksbilder på varje HANA-instans system-ID (SID) i den aktuella versionen av själva ögonblicksbild automation. Funktionen ger söker efter de registrerade SAP HANA-instanserna på servern i konfigurationsfilen (se nedan) och kör en samtidig ögonblicksbild av volymer med alla instanser som är registrerade på enheten.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Steg 1: Installera klienten för SAP HANA HDB

Linux-operativsystem som är installerade på SAP HANA på Azure (stora instanser) innehåller mappar och skript som krävs för att köra SAP HANA storage-ögonblicksbilder för återställning för säkerhetskopiering och haveriberedskap. Sök efter nyare versioner i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Den senaste versionen av skripten är 3.x. Olika skript kan ha olika utgåvor av mindre inom samma huvudversion.

>[!IMPORTANT]
>När du flyttar från version 2.1 till version 3.x av skript, Observera att strukturen för konfigurationsfilen och vissa syntax har ändrats. Se bildtexter i de olika avsnitten. 

Det är ditt ansvar att installera klienten HDB för SAP HANA på stora HANA-instansen enheter medan du installerar SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Steg 2: Ändra/etc/ssh/ssh\_config

Ändra `/etc/ssh/ssh_config` genom att lägga till den _Mac-datorer hmac-sha1_ rad som visas här:
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>Steg 3: Skapa en offentlig nyckel

Om du vill aktivera åtkomst till lagring ögonblicksbild gränssnitt klientorganisationens stora HANA-instansen måste du upprätta en procedur inloggning via en offentlig nyckel. På den första SAP HANA på Azure (stora instanser)-server i din klient, skapa en offentlig nyckel som används för att komma åt lagringsinfrastrukturen. Den offentliga nyckeln garanterar att ett lösenord inte krävs att logga in på gränssnitt som storage ögonblicksbild. Skapa en offentlig nyckel innebär också att du inte behöver underhålla autentiseringsuppgifter för lösenord. Kör följande kommando för att generera den offentliga nyckeln i Linux på stora instanser av SAP HANA-servern:
```
  ssh-keygen –t dsa –b 1024
```
Den nya platsen är **_/root/.ssh/id\_dsa.pub**. Ange inte en faktiska lösenord, eller så du måste ange lösenordet varje gång du loggar in. Välj i stället **RETUR** två gånger för att ta bort kravet att ”ange lösenord” för att logga in.

Se till att den offentliga nyckeln har korrigerats som förväntat genom att ändra mappar som ska **/root/.ssh/** och sedan köra den `ls` kommando. Om nyckeln finns kan du kopiera den genom att köra följande kommando:

![Den offentliga nyckeln kopieras genom att köra det här kommandot](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

Nu kan kontakta SAP HANA på Azure Service Management och ge dem med den offentliga nyckeln. Servicerepresentant använder den offentliga nyckeln för att registrera den i den underliggande lagringsinfrastrukturen som är vis för din klient för stora HANA-instansen.

### <a name="step-4-create-an-sap-hana-user-account"></a>Steg 4: Skapa ett användarkonto för SAP HANA

Om du vill initiera skapandet av SAP HANA-ögonblicksbilder måste du skapa ett användarkonto i SAP HANA som kan använda för skript för storage-ögonblicksbilder. Skapa ett användarkonto för SAP HANA SAP HANA-Studio för detta ändamål. Användaren måste skapas under SYSTEMDB och inte under SID-databasen för MDC. Användaren är inställningen under klientdatabasen i enskild behållare-miljö. Det här kontot måste ha följande behörigheter: **säkerhetskopiering Admin** och **Catalog Läs**. I det här exemplet är användarnamnet **SCADMIN**. Användarens kontonamn som skapats i HANA Studio är skiftlägeskänsligt. Se till att välja **nr** för att kräva att användaren ändrar lösenord på deras nästa inloggning.

![Skapa en användare i HANA-Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

Om du använder MCOD distributioner med flera SAP HANA-instanser på en enhet, måste du upprepa det här steget för varje SAP HANA-instans.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Steg 5: Auktorisera användarkontot för SAP HANA

I det här steget ska godkänna du SAP HANA-användarkonto som du skapade, så att skript som inte behöver att skicka lösenord vid körning. SAP HANA-kommandot `hdbuserstore` gör det möjligt att skapa en SAP HANA användarnyckel, som är lagrad på en eller flera SAP HANA-noder. Användarnyckel kan användare åtkomst till SAP HANA utan att behöva hantera lösenord från inom scripting processen. Scripting processen beskrivs senare i den här artikeln.

>[!IMPORTANT]
>Kör följande kommando under användaren skripten planeras som ska köras. I annat fall fungerar skriptet inte korrekt.

Ange den `hdbuserstore` -kommandot enligt följande:

**För icke - MDC HANA-installation**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**För MDC HANA-installation**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

I följande exempel, användaren är **SCADMIN01**, värdnamnet är **lhanad01**, och instans-antalet är **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Om du använder en HANA MCOD-distribution med flera SAP HANA-instanser på en enhet, måste du Upprepa steget för varje SAP HANA-instans och den associerade säkerhetskopiering användaren på enheten.

Om du har en konfiguration för SAP HANA-skalbarhet som du behöver hantera alla skript från en enda server. I det här exemplet, SAP HANA-nyckeln **SCADMIN01** måste ändras för varje värd på ett sätt som visar vilka värdgrupper som är relaterad till nyckeln. Ändra SAP HANA-konto med vilket instans av HANA-Databasobjekt för säkerhetskopiering. Nyckeln måste ha administratörsbehörighet på den värd som den är tilldelad och skala ut konfigurationer säkerhetskopiering användaren måste ha åtkomsträttigheter till SAP HANA-instanser. Förutsatt att de tre noderna för skalbar har namnen **lhanad01**, **lhanad02**, och **lhanad03**, de kommandon som ser ut så här:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Steg 6: Få ögonblicksbild-skript, konfigurera ögonblicksbilderna och testa konfiguration och anslutning

Hämta den senaste versionen av skript från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Kopiera skript som laddas ned och filen till arbetskatalogen för **hdbsql**. För den aktuella HANA installationer är den här katalogen i följande format: /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_test_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

När du hanterar Perl-skript: 

- Ändra aldrig skripten såvida du inte instruerats av Microsoft Operations.
- När du ombeds att ändra skriptet eller en parameterfil, Använd alltid Linux textredigerare, till exempel ”vi” och inte en Windows-redigerare t.ex. anteckningar. Formatet kan skadas om du använder en Windows-redigerare.
- Använd alltid senaste skripten. Du kan hämta den senaste versionen från GitHub.
- Använd samma version av skripten i miljön.
- Testa skripten och bekanta med de parametrar som krävs och utdata från skriptet innan du använder dem direkt i produktionssystemet.
- Ändra inte monteringspunktsnamn på servern som etablerats av Microsoft Operations. Dessa skript är beroende av dessa standard monteringspunkter ska vara tillgängliga för en lyckad körning.


Syftet med olika skript och filer är följande:

- **Azure\_hana\_backup.pl**: det här skriptet kommer med verktyget Linux Cron schemaläggning för att köra ögonblicksbilder av lagring på HANA-data och delade volymer, / hana/logbackups volymen eller operativsystemet.
- **Azure\_hana\_replikering\_status.pl**: det här skriptet innehåller grundläggande information kring replikeringsstatus från produktionsplatsen till katastrofåterställningsplatsen. Skript-Övervakare för att se till att replikeringen sker och det visar storleken på objekt som replikeras. Den ger även vägledning om en replikering tar för lång tid eller om länken inte fungerar.
- **Azure\_hana\_ögonblicksbild\_details.pl**: det här skriptet visar en lista över grundläggande information om alla ögonblicksbilder, per volym, som finns i din miljö. Det här skriptet kan köras på den primära servern eller på en server-enhet på disaster recovery-plats. Skriptet innehåller följande information, fördelat på varje volym som innehåller ögonblicksbilder:
   * Storleken på totalt antal ögonblicksbilder för en volym
   * Följande information i varje ögonblicksbild i volymen: 
      - Namnet på ögonblicksbilden 
      - Tid för skapande 
      - Storleken på ögonblicksbilden
      - Frekvensen för ögonblicksbilden
      - HANA Backup-ID som är associerade med den ögonblicksbilden, om det behövs
- **Azure\_hana\_ögonblicksbild\_delete.pl**: det här skriptet tar bort en ögonblicksbild för lagring eller en uppsättning ögonblicksbilder. Du kan använda SAP HANA säkerhetskopie-ID som hittades i HANA Studio eller namnet på storage-ögonblicksbilder. Säkerhetskopie-ID är för närvarande endast kopplad till ögonblicksbilder som skapats för HANA data/log/klusterdelade volymer. Annars, om ett ögonblicksbild-ID anges den strävar efter alla ögonblicksbilder som matchar de angivna ögonblicksbild-ID.  
- **testHANAConnection.pl**: det här skriptet testar anslutningen till SAP HANA-instans och krävs för att konfigurera storage-ögonblicksbilder.
- **testStorageSnapshotConnection.pl**: det här skriptet har två syften. Först ser till att den stora HANA-instansen-enhet som kör skripten har åtkomst till den virtuella datorn för tilldelade lagring och till lagringsgränssnittet för ögonblicksbild av stora HANA-instanser. Andra syftet är att skapa en tillfällig ögonblicksbild för HANA-instansen som du vill testa. Det här skriptet ska köras för varje HANA-instans på en server för att se till att säkerhetskopiering skripten fungerar som förväntat.
- **removeTestStorageSnapshot.pl**: det här skriptet tar bort test-ögonblicksbilden som skapades med skriptet **testStorageSnapshotConnection.pl**.
- **Azure\_hana\_dr\_failover.pl**: det här skriptet initierar en DR-redundans i en annan region. Skriptet måste köras på den stora HANA-instansen enheten i regionen för Haveriberedskap eller på enheten du vill växla över till. Det här skriptet stoppar storage-replikering från primär sida till sekundär sida, återställer den senaste ögonblicksbilden på DR-volymer och ger monteringspunkter för DR volymer.
- **Azure\_hana\_testa\_dr\_failover.pl**: det här skriptet utför ett redundanstest i DR-plats. Till skillnad från skriptet azure_hana_dr_failover.pl avbryter den här körningen inte storage-replikering från primär till sekundär. I stället kloner replikerad lagring volymer skapas på haveriberedskapssidan och monteringspunkter klonade volymer tillhandahålls. 
- **HANABackupCustomerDetails.txt**: den här filen är en ändringsbar konfigurationsfil som du behöver ändra för att anpassa sig till din SAP HANA-konfiguration. Den *HANABackupCustomerDetails.txt* filen är den kontroll och konfiguration för det skript som körs storage-ögonblicksbilder. Justera filen för dina syften och inställningar. Du får den **Backup Lagringsnamnet** och **Storage IP-adress** från SAP HANA på Azure Service Management när du distribuerar dina instanser. Du kan inte ändra sekvensen, beställning eller avstånd i någon av variablerna i den här filen. Om du gör skript körs inte korrekt. Dessutom visas IP-adressen för noden skala upp eller den överordnade noden (om skalbar) från SAP HANA på Azure Service Management. Du kan också veta hur många av HANA-instans som är tillgängliga under installationen av SAP HANA. Nu kan behöva du lägga till ett namn på säkerhetskopia i konfigurationsfilen.

För skala upp eller skala ut distribution, konfigurationsfilen skulle se ut i följande exempel när du har fyllt i servernamnet för den stora HANA-instansen enheten och serverns IP-adress. Fyll i alla fält som behövs för varje SAP HANA-SID som du vill säkerhetskopiera eller återställa.

Du kan också kommentera ut rader av instanser som du inte vill säkerhetskopiera för en viss tid genom att lägga till en ”#” framför ett obligatoriskt fält. Du behöver också att ange alla SAP HANA-instanser som finns på en server om du inte behöver att säkerhetskopiera eller återställa den särskilda instansen. Formatet måste hållas för alla fält, eller alla skript genererar ett felmeddelande och skriptet avslutas. Du kan ta bort ytterligare nödvändiga rader med alla SID information om du inte använder efter de senaste SAP HANA-instansen som används. Alla rader måste vara antingen ifyllt, utkommenterad eller tas bort.

>[!IMPORTANT]
>Strukturen för filen ändrats med övergången från version 2.1 till version 3.x. Om du vill använda 3.x version skript kan behöva du anpassa filstruktur configuration. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

För varje instans som du konfigurerar på stora HANA-instansen-enhet, eller för skalbar konfigurationen, måste du definiera data på följande sätt:

    
```
######***SID #1 Information***#####
SID1: h01
###Provided by Microsoft Operations###
SID1 Storage Backup Name: clt1h01backup
SID1 Storage IP Address: 172.18.18.11
######     Customer Provided    ######
SID1 HANA instance number: 00
SID1 HANA HDBuserstore Name: SCADMINH01
```
Upprepa den här konfigurationen på varje nod för skalbarhet och HANA System Replication konfigurationer. Det här måttet ser till att i fel fall, säkerhetskopior och eventuell lagringsreplikering fortsätter att fungera.   

När du placerar alla konfigurationsdata i den *HANABackupCustomerDetails.txt* filen, kontrollera om konfigurationerna som är korrekta för HANA instansdata. Använd skript för `testHANAConnection.pl`, som är oberoende av en konfiguration för SAP HANA skala upp eller skala ut.

```
testHANAConnection.pl
```

Om du har en konfiguration för utbyggnad av SAP HANA kan du kontrollera att den överordnade HANA-instansen har åtkomst till alla nödvändiga HANA-servrar och instanser. Det finns inga parametrar att testskriptet, men du måste lägga till dina data till den *HANABackupCustomerDetails.txt* konfigurationsfil för att skriptet ska köras. Endast shell-kommando felkoder returneras så att det inte är möjligt för skriptet att fel kontrollera varje instans. Men det är möjligt ger skriptet vissa användbara kommentarer som du kan kontrollera.

Om du vill köra skriptet anger du följande kommando:
```
 ./testHANAConnection.pl
```
Om skriptet hämtar har statusen för HANA-instans, visas ett meddelande om att en HANA-anslutning har upprättats.


I nästa steg är att kontrollera anslutningen till lagring baserat på data som du placerar i den *HANABackupCustomerDetails.txt* configuration filen och sedan köra en test-ögonblicksbild. Innan du kan köra den `azure_hana_backup.pl` skript, måste du köra det här testet. Om en volym som innehåller inga ögonblicksbilder, är det omöjligt att avgöra om volymen är tom eller om det finns ett SSH-fel för att hämta information för ögonblicksbild. Därför körs två steg:

- Verifierar att klientens storage virtuell dator och gränssnitt som är tillgängliga för skript för att köra ögonblicksbilder.
- Skapas en test- eller dummy, ögonblicksbild för varje volym av HANA-instans.

Därför ingår HANA-instans som ett argument. Om körningen misslyckas, går det inte att ange fel vid sökning efter anslutning till lagringsplatsen. Även om det finns ingen felkontroll, ger skriptet användbara tips.

1. Kör de kommandon du utför det här testet:

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   Både användarnamn för lagring och lagring IP-adress har angetts till dig vid övergång av den stora HANA-instansen-enheten.

1. Kör skriptet för enhetstest:
   ```
    ./testStorageSnapshotConnection.pl
   ```

Skriptet försöker logga in till storage med hjälp av den offentliga nyckeln som anges i föregående steg i installationen, och med de data som konfigurerats i den *HANABackupCustomerDetails.txt* fil. Om inloggningen lyckas visas följande innehåll:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Om det uppstår i ansluter till storage-konsolen utdata ser ut så här:

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

Efter en lyckad inloggning för virtuell dator gränssnitten för lagring, skriptet fortsätter med fas 2 och skapar en test-ögonblicksbild. Utdata visas här för en konfiguration med tre noder skala ut SAP Hana:

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

Om testet ögonblicksbilden har körts har med skript, Fortsätt med att konfigurera faktiska storage-ögonblicksbilder. Om du inte lyckas kan du undersöka problem innan du går vidare. Test-ögonblicksbild ska vara runt tills de första verkliga ögonblicksbilderna är klar.


### <a name="step-7-perform-snapshots"></a>Steg 7: Utför ögonblicksbilder

När steg för förberedelse är klar kan du börja konfigurera faktiska ögonblicksbild lagringskonfigurationen. Skriptet som ska schemaläggas fungerar med SAP HANA skala upp och skala ut konfigurationer. Schemalägga skriptet med hjälp av verktyget cron för periodiska och regelbundna körningen av skriptet säkerhetskopieringen. 

Du kan skapa tre typer av säkerhetskopior av ögonblicksbilder:
- **HANA**: en kombinerad ögonblicksbildsäkerhetskopia där de volymer som innehåller/hana/data och/hana/delade (som innehåller /usr/sap samt) omfattas av samordnad ögonblicksbilden. En enda filåterställning går från den här ögonblicksbilden.
- **Loggar**: en som ögonblicksbildsäkerhetskopia av/hana/logbackups volymen. Inga HANA-ögonblicksbilder utlöses för att köra den här ögonblicksbilden för lagring. Den här lagringsvolymen är avsedd att innehålla säkerhetskopieringarna av transaktionsloggen SAP HANA. Dessa utförs oftare för att begränsa log tillväxt och förhindra förlust av data. En enda filåterställning går från den här ögonblicksbilden. Inte lägre frekvensen under 3 minuter.
- **Start**: en ögonblicksbild av volymen som innehåller start logiskt enhetsnummer (LUN) för den stora HANA-instansen. Den här säkerhetskopian kan endast med den typ I SKU: er för stora HANA-instanser. Du kan inte utföra enkla återställningar från ögonblicksbilden av den volym som innehåller startar LUN.


>[!NOTE]
> Anropssyntaxen för dessa tre typer av ögonblicksbilder som har ändrats med övergången till version 3.x skripten som stöder MCOD distributioner. Det finns inget behov att ange en instans HANA SID längre. Du måste se till att SAP HANA-instanser av en enhet är konfigurerad i konfigurationsfilen *HANABackupCustomerDetails.txt*.

>[!NOTE]
> När du kör skriptet för första gången kan visas det ett oväntat fel i miljön med multi-SID. Köra skriptet åtgärdar problemet.



Den nya anropssyntaxen för att köra storage-ögonblicksbilder med skriptet *azure_hana_backup.pl* ser ut så här:

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

Information om parametrarna är följande: 

- Den första parametern karaktäriserar typen av ögonblicksbild-säkerhetskopiering. De värden som tillåts är **hana**, **loggar**, och **Start**. 
- Parametern **<HANA Large Instance Type>** krävs för start volymen säkerhetskopieringar. Det finns två giltiga värden med ”TypeI” eller ”TypeII” beroende på den HANA stora instans enheten. Att ta reda på vilken typ din enhet är finns i [SAP HANA (stora instanser) översikt och arkitektur på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- Parametern **< snapshot_prefix >** är en ögonblicksbild eller säkerhetskopiering etiketten för typ av ögonblicksbild. Den har två syften: en är att ge den ett namn så att du vet vad de här ögonblicksbilderna om. Andra syftet är att skriptet *azure\_hana\_backup.pl* att fastställa antalet ögonblicksbilder av lagring som bevaras under specifika etiketten. Om du schemalägger två säkerhetskopior på grund av storage-ögonblicksbilder av samma typ (t.ex. **hana**), med två olika etiketter och definiera att 30 ögonblicksbilder ska finnas för varje kan du få 60 storage-ögonblicksbilder av volymerna som påverkas. Endast alfanumeriska (”A-Z, a-z, 0-9”), understreck (”_”) och bindestreck (”-”) tecken tillåts. 
- Parametern **< snapshot_frequency >** är reserverad för framtida utveckling och har inte påverkas. Ange den till ”3min” när du kör säkerhetskopior av typen **log**, och att ”15 min” när du kör de andra typerna av säkerhetskopiering.
- Parametern **<number of snapshots retained>** definierar kvarhållningen av ögonblicksbilder indirekt genom att definiera hur många ögonblicksbilder med samma ögonblicksbild prefix (etikett). Den här parametern är viktigt för schemalagda körningar via cron. Om antalet ögonblicksbilder med samma snapshot_prefix överstiger det antal som anges av den här parametern, raderas den äldsta ögonblicksbilden innan du kör en ny storage-ögonblicksbild.

När det gäller en skalbar utför skriptet ytterligare kontroller för att säkerställa att du kan komma åt alla HANA-servrar. Skriptet kontrollerar också att alla HANA-instanser returnera rätt status för instanserna innan skapas en ögonblicksbild av SAP HANA. SAP HANA-ögonblicksbilder följs av en storage-ögonblicksbilder.

Körningen av skriptet `azure_hana_backup.pl` skapar lagringen ögonblicksbild i följande tre faser:

1. Kör SAP HANA-ögonblicksbilder
1. Kör en storage-ögonblicksbilder
1. Tar bort SAP HANA-ögonblicksbilden som skapades före körning av storage-ögonblicksbilder

För att köra skriptet anropar du den från den körbara HDB-mappen som de kopierades. 

Kvarhållningsperioden administreras med antalet ögonblicksbilder som skickas som en parameter när du kör skriptet. Hur lång tid som täcks av storage-ögonblicksbilder är en funktion av körning och antalet ögonblicksbilder som skickas som en parameter när du kör skriptet. Om antalet ögonblicksbilder som hålls överstiger det antal som namnges som en parameter i anropet av skriptet, raderas den äldsta storage-ögonblicksbilden med samma etikett innan en ny ögonblicksbild körs. Det tal som du ger eftersom den sista parametern för anropet är det nummer du kan använda för att styra antalet ögonblicksbilder sparas. Med det här talet kan du också styra, indirekt, hur mycket diskutrymme används för ögonblicksbilder. 

> [!NOTE]
>När du ändrar etiketten startas inventeringen igen. Du måste vara strikt i etikettering, så din ögonblicksbilder inte tas bort av misstag.

## <a name="snapshot-strategies"></a>Strategier för ögonblicksbild
Frekvensen för ögonblicksbilder för de olika typerna beror på om du använder stora HANA-instansen disaster recovery-funktionen. Den här funktionen är beroende av ögonblicksbilder av lagring, som kan kräva särskilda rekommendationer för frekvens och körningen perioder av storage-ögonblicksbilder. 

Överväganden och rekommendationer som följer, antas att du gör *inte* använda disaster recovery-funktionen som stora HANA-instanser erbjuder. I stället använder storage-ögonblicksbilder för att ha säkerhetskopior och att kunna ge point-in-time-återställning för de senaste 30 dagarna. Med begränsningar av antalet ögonblicksbilder och utrymme kan har kunder vara följande krav:

- Återställningstid för point-in-time-återställning.
- Hur mycket diskutrymme.
- Återställningspunkt och målen för potentiella återställning efter en katastrof.
- Slutlig för körning av HANA-full-databassäkerhetskopieringar mot diskar. När en fullständig databassäkerhetskopia mot diskar eller **backint** gränssnittet utförs, körning av storage-ögonblicksbilder misslyckas. Om du planerar att köra fullständiga databassäkerhetskopieringar ovanpå ögonblicksbilder av lagring, se till att körningen av storage-ögonblicksbilder inaktiveras under den här tiden.
- Antalet ögonblicksbilder per volym (begränsat till 250).


För kunder som inte använder disaster recovery-funktionen för HANA stora instanser, är den ögonblicksbild perioden uppstår mer sällan. I sådana fall kan kunder utföra kombinerade ögonblicksbilder på /hana/data och /hana/shared (inklusive /usr/sap) i 12 timmar eller 24-timmarsformat punkter och de hålla ögonblicksbilder för en månad. Samma sak gäller med ögonblicksbilder av säkerhetskopiering loggvolymen. Körningen av SAP HANA säkerhetskopieringar av transaktionsloggen mot loggvolymen säkerhetskopiering sker dock i 5 minuter för 15 minuters perioder.

Schemalagda lagringsögonblicksbilder utförs bäst med hjälp av cron. Använda samma skript för alla säkerhetskopior och disaster recovery behov och att du ändrar skriptet indata för att matcha de olika begärt säkerhetskopieringstider. Dessa ögonblicksbilder är alla schemalagda på olika sätt i cron beroende på sin Utförandetid: per timme, 12 timmar, varje dag eller varje vecka. 

Följande är ett exempel på ett cron-schemat i/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
I exemplet ovan är en timvis kombinerade ögonblicksbild som fullständigt täcker alla volymer som innehåller data/hana/och /hana/shared (inklusive/usr/sap) platser. Använd den här typen av ögonblicksbild för en snabbare point-in-time-återställning under de senaste två dagarna. Det finns också en ögonblicksbild på dessa volymer. Därför har två dagars täckning som per timme ögonblicksbilder, plus fyra veckor täckningen av dagliga ögonblicksbilder. Dessutom säkerhetskopieras säkerhetskopiering transaktionsvolymer log varje dag. Dessa säkerhetskopior behålls i fyra veckor samt. Som du ser i den tredje adressraden crontab är säkerhetskopia av transaktionsloggen HANA schemalagd att köras var femte minut. Starttider för olika cron-jobb som kör lagringsögonblicksbilder ut, så att dessa ögonblicksbilder inte körs på en gång vid en viss punkt i tiden. 

I följande exempel kan du utföra en kombinerad ögonblicksbild som omfattar volymerna som innehåller de/hana/data och hana/delade (inklusive/usr/sap) platserna per timme. Du kan behålla de här ögonblicksbilderna i två dagar. Ögonblicksbilder av säkerhetskopiering transaktionsvolymer log utförs på basis av 5 minuter och hålls i 4 timmar. Som är tidigare, säkerhetskopiering av HANA transaktionsloggfilen schemalagd att köras var femte minut. Ögonblicksbild av transaktionen-loggvolymen säkerhetskopiering utförs med en 2-minuters fördröjning när säkerhetskopian av transaktionsloggen har startats. Loggsäkerhetskopiering för SAP HANA-transaktion bör slutföras under normala omständigheter inom dessa två minuter. Som tidigare, volymen som innehåller startavbildningen LUN backas upp en gång per dag av storage-ögonblicksbilder och sparas i fyra veckor.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

Följande bild illustrerar sekvenser av det tidigare exemplet, exklusive start LUN:

![Förhållandet mellan säkerhetskopieringar och ögonblicksbilder](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA utför regelbundna skrivningar till /hana/log volymen för att dokumentera allokerade ändringar till databasen. Med jämna mellanrum skriver en lagringspunkt i SAP HANA till /hana/data volymen. Som anges i crontab körs en säkerhetskopiering av SAP HANA-transaktionsloggen var femte minut. Du kan också se att en SAP HANA-ögonblicksbilder körs varje timme till följd av utlöser en kombinerad storage ögonblicksbild över /hana/data och /hana/shared volymer. När ögonblicksbilden HANA lyckas, utförs kombinerade storage-ögonblicksbilder. Som finns beskrivet i crontab, körs storage-ögonblicksbilder på volymen /hana/logbackup var 5 minuter, cirka 2 minuter efter säkerhetskopian av transaktionsloggen HANA.

> 

>[!IMPORTANT]
> Användningen av ögonblicksbilder av lagring för säkerhetskopior av SAP HANA är värdefullt endast när ögonblicksbilder utförs tillsammans med SAP HANA säkerhetskopieringar av transaktionsloggen. Dessa säkerhetskopieringar av transaktionsloggen måste omfatta tidsperioder mellan storage-ögonblicksbilder. 

Om du har angett ett åtagande för användare av en point-in-time-återställning på 30 dagar, måste du:

- Komma åt en kombinerad lagring ögonblicksbild över /hana/data och /hana/shared som är 30 dagar i extrema fall.
- Har säkerhetskopior av sammanhängande transaktionsloggen som omfattar tiden mellan alla kombinerade storage-ögonblicksbilder. Därför måste äldsta ögonblicksbilden av säkerhetskopiering transaktionsvolymer log vara 30 dagar gamla. Detta gäller inte om du kopierar säkerhetskopieringarna av transaktionsloggen till en annan NFS-resurs som finns på Azure storage. I så fall kan du hämta gamla säkerhetskopieringar av transaktionsloggen från den NFS-resursen.

Om du vill dra nytta av ögonblicksbilder av lagring och eventuell storage-replikering av säkerhetskopieringar av transaktionsloggen, som du behöver ändra den plats som SAP HANA skriver säkerhetskopieringarna av transaktionsloggen. Du kan göra den här ändringen i HANA Studio. Även om SAP HANA säkerhetskopierar automatiskt fullständig log segment, bör du ange ett intervall för log-säkerhetskopiering för att vara entydig. Detta gäller särskilt när du använder alternativet disaster recovery eftersom du vanligtvis vill köra säkerhetskopieringar med en deterministisk punkt. I följande fall har 15 minuter angetts som log säkerhetskopieringsintervallet.

![Schemalägg säkerhetskopiering för SAP HANA-loggarna i SAP HANA-Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Du kan också välja säkerhetskopieringar som är kortare än 15 minuter. En mer frekventa inställning används ofta tillsammans med funktioner för haveriberedskap för stora HANA-instanser. Vissa kunder utföra säkerhetskopieringar av transaktionsloggen var femte minut.  

Om databasen har aldrig säkerhetskopierats, är det sista steget att utföra en säkerhetskopiering av fil-baserad databas om du vill skapa en enkel säkerhetskopiering post måste ligga inom säkerhetskopieringskatalogen. I annat fall kan inte SAP HANA initiera din angivna loggsäkerhetskopior.

![Gör en filbaserad säkerhetskopia för att skapa en enda post för säkerhetskopiering](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


När din första lyckade storage-ögonblicksbilder har körts, kan du ta bort test-ögonblicksbilden som kördes i steg 6. Du gör detta genom att köra skriptet `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl
```

Följande är ett exempel på utdata från skriptet:
```
Checking Snapshot Status for h80
**********************Checking access to Storage**********************
Storage Snapshot Access successful.
**********************Getting list of volumes that match HANA instance specified**********************
Collecting set of volumes hosting HANA matching pattern *h80* ...
Volume show completed successfully.
Adding volume hana_data_h80_mnt00001_t020_vol to the snapshot list.
Adding volume hana_log_backups_h80_t020_vol to the snapshot list.
Adding volume hana_shared_h80_t020_vol to the snapshot list.
**********************Adding list of snapshots to volume list**********************
Collecting set of snapshots for each volume hosting HANA matching pattern *h80* ...
**********************Displaying Snapshots by Volume**********************
hana_data_h80_mnt00001_t020_vol
Test_HANA_Snapshot.2018-02-06_1753.3
Test_HANA_Snapshot.2018-02-06_1815.2
….
Command completed successfully.
Exiting with return code: 0
Command completed successfully.
```


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Övervakning av antal och storlek för ögonblicksbilder på volymen

Du kan övervaka antalet ögonblicksbilder och lagringsanvändningen dessa ögonblicksbilder på en specifik lagringsvolym. Den `ls` kommandot visas inte den ögonblicksbild katalog eller filer. Dock Linux OS-kommandot `du` visar information om dessa ögonblicksbilder av lagring, eftersom de lagras på samma volymer. Kommandot kan användas med följande alternativ:

- `du –sh .snapshot`: Det här alternativet ger en totalsumma på alla ögonblicksbilder i katalogen ögonblicksbild.
- `du –sh --max-depth=1`: Det här alternativet visar en lista över alla ögonblicksbilder som sparas i den **.snapshot** mapp och storleken på varje ögonblicksbild.
- `du –hc`: Det här alternativet ger den totala storleken som används av alla ögonblicksbilder.

Med dessa kommandon för att se till att ögonblicksbilder som tas och lagras inte förbrukar alla lagring på volymerna.

>[!NOTE]
>Ögonblicksbilder av startavbildningen LUN visas inte med föregående kommandon.

### <a name="getting-details-of-snapshots"></a>Hämtning av information om ögonblicksbilder
Om du vill visa mer information om ögonblicksbilder kan du också använda skriptet `azure_hana_snapshot_details.pl`. Det här skriptet kan köras på någon av platserna om det finns en aktiv server i disaster recovery-plats. Skriptet innehåller följande utdata, fördelat på varje volym som innehåller ögonblicksbilder: 
   * Storleken på totalt antal ögonblicksbilder för en volym
   * Följande information i varje ögonblicksbild i volymen: 
      - Namnet på ögonblicksbilden 
      - Tid för skapande 
      - Storleken på ögonblicksbilden
      - Frekvensen för ögonblicksbilden
      - HANA Backup-ID som är associerade med den ögonblicksbilden, om det behövs

Följande är ett exempel på skript körning syntax:

```
./azure_hana_snapshot_details.pl 
```

Eftersom skriptet försöker hämta HANA säkerhetskopie-ID, måste den ansluta till SAP HANA-instans. Den här anslutningen kräver konfigurationsfilen *HANABackupCustomerDetails.txt* anges korrekt. Utdata för två ögonblicksbilder på en volym kan se ut så här:

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Minska antalet ögonblicksbilder på en server

Du kan minska antalet vissa etiketter för ögonblicksbilder som du lagrar som beskrevs tidigare. Senast två parametrar för kommandot att initiera en ögonblicksbild är etiketten och antalet ögonblicksbilder som du vill behålla.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

I exemplet ovan ögonblicksbild etiketten är **dailyhana** och antalet ögonblicksbilder med den här etiketten ska behållas är **28**. När du har besvarat förbrukningen av diskutrymme, kan du minska antalet lagrade ögonblicksbilder. Ett enkelt sätt att minska antalet ögonblicksbilder till 15, till exempel är att köra skriptet med den sista parametern inställd **15**:

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

Om du kör skriptet med den här inställningen är antalet ögonblicksbilder, inklusive den nya storage-ögonblicksbilden 15. De senaste 15 ögonblicksbilderna hålls och 15 äldre ögonblicksbilder tas bort.

 >[!NOTE]
 > Det här skriptet minskar antalet ögonblicksbilder endast om det finns fler än 1 timme gamla ögonblicksbilder. Skriptet tar inte bort ögonblicksbilder som är mindre än 1 timme gamla. Dessa begränsningar är relaterade till de valfria disaster recovery-funktionerna som erbjuds.

Om du inte längre vill hantera en uppsättning ögonblicksbilder med säkerhetskopiering etiketten **hanadaily** i syntaxexemplen, kan du köra skriptet med **0** som kvarhållning tal. Alla ögonblicksbilder som matchar etiketten tas sedan bort. Ta bort alla ögonblicksbilder kan dock påverka funktionerna i HANA stora instanser disaster recovery-funktionen.

Ett andra alternativ att ta bort specifika ögonblicksbilder är att använda skriptet `azure_hana_snapshot_delete.pl`. Det här skriptet är utformad för att ta bort en ögonblicksbild eller en uppsättning ögonblicksbilder antingen med hjälp av HANA säkerhetskopie-ID som hittades i HANA Studio eller via ögonblicksbild namnet i sig. Säkerhetskopie-ID är för närvarande endast knuten till ögonblicksbilder som skapats för den **hana** typ av ögonblicksbild. Ta en ögonblicksbild säkerhetskopior av typen **loggar** och **Start** utför inte en SAP HANA-ögonblicksbilder och så det finns inga säkerhetskopie-ID för dessa ögonblicksbilder. Om ögonblicksbild namnet anges efter alla ögonblicksbilder i olika volymer som matchar namnet på angivna ögonblicksbilden. 

Anropa skriptet måste du ange SID för HANA-instansen med hjälp av anropssyntaxen för skriptet:

```
./azure_hana_snapshot_delete.pl <SID>

```

Kör skriptet som användare **rot**.

Om du väljer en ögonblicksbild kan du ta bort varje ögonblicksbild individuellt. Du först ange den volym som innehåller ögonblicksbilden och ange sedan namnet på ögonblicksbilden. Om ögonblicksbilden finns på volymen och är mer än 1 timme gamla, tas den bort. Du kan hitta volymnamn och ögonblicksbildnamnen genom att köra den `azure_hana_snapshot_details` skript. 

>[!IMPORTANT]
>Om det finns data som endast finns på ögonblicksbilden tas som bort när ögonblicksbilden har tagits bort, att data går förlorade permanent.

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>På filnivå återställning från en ögonblicksbild för lagring
För ögonblicksbild typer **hana** och **loggar**, du kan få åtkomst till ögonblicksbilder direkt på volymer i den **.snapshot** directory. Det finns en underkatalog för var och en av ögonblicksbilder. Du kan kopiera varje fil i tillståndet den var i vid ögonblicksbild från den underkatalogen i den faktiska katalogstrukturen. I den aktuella versionen av skriptet finns **nr** återställa skript som finns för återställning från ögonblicksbilder som en självbetjäning (även om återställning från ögonblicksbilder kan utföras som en del av DR självbetjäning skript på DR-plats under en redundansväxling). Genom att öppna en tjänstbegäran för att återställa en önskad ögonblicksbild från de befintliga tillgängliga ögonblicksbilderna måste du kontakta Microsoft operations-teamet.

>[!NOTE]
>Med en fil fungerar inte återställningen för ögonblicksbilder av startavbildningen LUN som är oberoende av vilken typ av enheter för stora HANA-instansen. Den **.snapshot** directory exponeras inte i filen Boot.ini LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Återställ till den senaste HANA-ögonblicksbilden

I ett scenario med produktion ned kan hela återställningen från en ögonblicksbild för lagring av initieras som en kundincident med Microsoft Azure-supporten. Det är bara några hög angelägenhetsgrad om data har tagits bort i ett produktionssystem, och det enda sättet att hämta det är att återställa produktionsdatabasen.

I annat fall kan en point-in-time-återställning kan vara med låg angelägenhetsgrad och planerade dagar i förväg. Du kan planera återställningen med SAP HANA på Azure Service Management i stället för att höja flaggan hög prioritet. Du kan till exempel planerar att uppgradera SAP-program genom att använda ett nytt paket för förbättring. Du måste sedan återgå till en ögonblicksbild som representerar tillstånd innan paketuppgradering förbättring.

Innan du skickar begäran måste du förbereda. SAP HANA på Azure Service Management-teamet kan sedan hantera begäran och ge de återställda volymerna. Därefter kan återställa du HANA-databas baserat på ögonblicksbilder. 

Följande visar hur du förbereder för begäran:

>[!NOTE]
>Användargränssnittet kan skilja sig från följande skärmbilder, beroende på SAP HANA-versionen som du använder.

1. Bestäm vilken ögonblicksbild för att återställa. Endast hana/datavolymen återställs, såvida inte du instruera annars. 

1. Stänga av HANA-instans.

 ![Stäng av HANA-instans](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Demontera datavolymerna på varje nod för HANA-databas. Om datavolymerna är fortfarande monterade för operativsystemet, misslyckas återställningen av ögonblicksbilden.
 ![Avmontera datavolymerna på varje nod för HANA-databas](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Öppna en supportförfrågan för Azure och innehåller instruktioner för återställning av en specifik ögonblicksbild.

 - Under återställningen: SAP HANA på Azure Service Management kanske du ombeds att delta i ett konferenssamtal så samordning, verifiering och bekräfta att rätt storage-ögonblicksbilder har återställts. 

 - Efter återställningen: SAP HANA på Azure Service Management meddelar dig när lagring ögonblicksbild har återställts.

1. När återställningsprocessen har slutförts, montera om alla datavolymer.

 ![Montera om alla datavolymer](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

1. Välj återställningsalternativ SAP HANA-Studio, om de inte automatiskt kommer när du återansluter till HANA-Databasobjekt via SAP HANA-Studio. I följande exempel visas en återställning till den senaste HANA ögonblicksbilden. Storage-ögonblicksbilder bäddar in en HANA-ögonblicksbilder. Om du återställer till den senaste ögonblicksbilden för lagring bör den vara senaste HANA-ögonblicksbilder. (Om du återställer till en äldre storage-ögonblicksbilder, du måste hitta HANA-ögonblicksbilder baserat på den tid som storage ögonblicksbilden togs.)

 ![Välj Återställ alternativ för SAP HANA-Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

1. Välj **återställa databasen till en specifik data säkerhetskopierings- eller ögonblicksbild**.

 ![Fönstret Välj återställningstyp](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

1. Välj **ange säkerhetskopiering utan katalog**.

 ![Fönstret Ange Säkerhetskopians plats](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

1. I den **måltypen** väljer **ögonblicksbild**.

 ![Ange att säkerhetskopiera till Återställ fönster](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

1. Välj **Slutför** att starta återställningsprocessen.

 ![Välj ”Slutför” om du vill påbörja återställningsprocessen](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

1. HANA-databas har återställts och återställt till HANA-ögonblicksbilder som ingår i storage-ögonblicksbilder.

 ![HANA-databas återställs och återställas till HANA-ögonblicksbild](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>Återställ till det senaste tillståndet

Följande process återställer HANA-ögonblicksbilder som ingår i storage-ögonblicksbilder. Den återställer sedan säkerhetskopieringarna av transaktionsloggen till det senaste tillståndet för databasen innan du återställer storage-ögonblicksbilder.

>[!IMPORTANT]
>Innan du fortsätter måste du kontrollera att du har en fullständig och sammanhängande kedja av säkerhetskopieringar av transaktionsloggen. Du kan inte återställa det aktuella tillståndet för databasen utan dessa säkerhetskopior.

1. Slutför steg 1 – 6 i [Återställ till den senaste HANA-ögonblicksbilden](#recovering-to-the-most-recent-hana-snapshot).

1. Välj **återställa databasen till det senaste tillståndet**.

 ![Välj ”återställa databasen till det senaste tillståndet”](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

1. Ange platsen för de senaste HANA säkerhetskopieringarna. Platsen måste innehålla alla de HANA säkerhetskopieringarna av transaktionsloggen från HANA-ögonblicksbilder till det senaste tillståndet.

 ![Ange platsen för de senaste HANA loggsäkerhetskopior](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

1. Välj en säkerhetskopia som bas som du vill återställa databasen från. I det här exemplet är HANA ögonblicksbilden i skärmbilden HANA-ögonblicksbilder som användes i storage-ögonblicksbilder. 

 ![Välj en säkerhetskopia som bas som du vill återställa databasen från](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

1. Rensa den **Använd Delta säkerhetskopior** markerar du kryssrutan om deltan inte finns mellan tiden för HANA-ögonblicksbilder och det senaste tillståndet.

 ![Avmarkera kryssrutan ”Använd Delta säkerhetskopior” om det finns inga deltan](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

1. I fönstret Sammanfattning Välj **Slutför** att starta återställningen.

 ![Klicka på ”Slutför” i fönstret Sammanfattning](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>Återställa till en annan tidpunkt
Om du vill återställa till en tidpunkt mellan HANA-ögonblicksbilder (ingår i storage-ögonblicksbilder) och ett som är senare än HANA ögonblicksbild point-in-time-återställning, utför du följande steg:

1. Kontrollera att du har alla de säkerhetskopieringar av transaktionsloggen från HANA-ögonblicksbilder för den tid som du vill återställa till.
1. Börjar under [återställa till det senaste tillståndet](#recovering-to-the-most-recent-state).
1. I steg 2 i proceduren i det **ange återställningstyp** väljer **återställa databasen till den följande**, och ange sedan punkten i tiden. 
1. Slutför steg 3 – 6.

## <a name="monitor-the-execution-of-snapshots"></a>Övervaka körningen av ögonblicksbilder

När du använder storage-ögonblicksbilder av stora HANA-instanser måste du också övervaka körningen av dessa ögonblicksbilder. Skriptet som körs en storage-ögonblicksbilder skriver utdata till en fil och sparar det på samma plats som Perl-skript. En separat fil skrivs för varje ögonblicksbild för lagring. Utdata från varje fil visar de olika faserna att ögonblicksbild skriptet körs:

1. Söker efter de volymer som behöver skapa en ögonblicksbild.
1. Söker efter ögonblicksbilder som kommer från dessa volymer.
1. Tar bort eventuell befintlig ögonblicksbilder för att matcha antalet ögonblicksbilder som du har angett.
1. Skapar en SAP HANA-ögonblicksbilder.
1. Skapar ögonblicksbild för lagring över volymerna.
1. Tar bort SAP HANA-ögonblicksbilder.
1. Byter namn på den senaste ögonblicksbilden till **.0**.

Den viktigaste delen av skriptet rådgivningsnämnden för ändringar identifieras är den här delen:
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
Från det här exemplet kan du se hur skriptet registrerar skapandet av HANA-ögonblicksbilder. Den här processen initieras om skalbarhet på huvudnoden. Huvudnoden initierar synkron skapandet av SAP HANA-ögonblicksbilder på alla arbetsnoder. Storage-ögonblicksbilder tas sedan. Efter lyckad körning av storage-ögonblicksbilder, tas HANA-ögonblicksbilder bort. Borttagningen av HANA-ögonblicksbilder initieras från den överordnade noden.


**Nästa steg**
- Se [Disaster Recovery principer och förberedelse av](hana-concept-preparation.md).