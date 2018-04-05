---
title: Hög tillgänglighet och katastrofåterställning återställning av SAP HANA i Azure (stora instanser) | Microsoft Docs
description: Skapa hög tillgänglighet och planera för katastrofåterställning för SAP HANA i Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/01/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6c939e0fb59c7fce2c1c34aca1b77bd0b8cec0c5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Stora instanser för SAP HANA hög tillgänglighet och katastrofåterställning recovery på Azure 

>[!IMPORTANT]
>Den här dokumentationen finns ingen ersättning för SAP HANA administration dokumentation eller SAP anteckningar. Det förväntas att läsaren har en god förståelse av och kunskap om SAP HANA-administration och åtgärder, särskilt med hjälp av för säkerhetskopiering, återställning, hög tillgänglighet och katastrofåterställning. Skärmbilder från SAP HANA Studio visas i den här dokumentationen. Innehåll, struktur och uppbyggnad skärmar SAP Administrationsverktyg och verktyg själva kan ändras från SAP HANA versionen versionen.

Det är viktigt att utöva steg och processer som vidtas i din miljö och med HANA versioner och utgåvor. Vissa processer som beskrivs i den här dokumentationen är förenklad för en bättre förståelse och är inte avsedda att användas som detaljerade anvisningar för eventuell åtgärden handböcker. Om du vill skapa åtgärden handböcker för dina konfigurationer behöver du testa och utnyttja dina processer och dokumentera de processer som är relaterade till dina specifika konfigurationer. 


Hög tillgänglighet och katastrofåterställning (DR) är viktiga aspekter av din verksamhetskritiska SAP HANA i Azure (stora instanser)-server. Det är viktigt att arbeta med SAP, din systemintegreraren eller Microsoft skapa och implementera rätt hög tillgänglighet och strategi för katastrofåterställning korrekt. Det är också viktigt att tänka på återställningspunktmål (RPO) och mål, som är specifika för din miljö.

Microsoft stöder vissa funktioner för SAP HANA hög tillgänglighet med HANA stora instanser. Dessa funktioner är:

- **Storage-replikering**: lagring systemets möjlighet att replikera alla data till en annan HANA stora instans stämpel i en annan Azure-region. SAP HANA fungerar oberoende av den här metoden. Den här funktionen är disaster recovery standardmekanism erbjuds för HANA stora instanser.
- **HANA system replication**: den [replikering av alla data i SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) till ett separat system för SAP HANA. Mål minimeras via datareplikering med jämna mellanrum. SAP HANA stöder asynkrona, synkron i minnet och synkront läge. Synkront läge används endast för SAP HANA-system som är i samma datacenter eller mindre än 100 km från varandra. Med den aktuella designen av HANA stora instans stämplar kan HANA system replikering användas för hög tillgänglighet i en region. HANA system replication kräver en tredje parts omvänd proxy eller Routning komponent för konfigurationer med katastrofåterställning i en annan Azure-region. 
- **Värd för automatisk redundans**: en lokal fault-lösning för SAP HANA som är ett alternativ till HANA system replikering. Om huvudnoden blir otillgänglig, du konfigurera en eller flera vänteläge SAP HANA-noder i skalbar läge och SAP HANA växlar automatiskt över till en vänteläge nod.

SAP HANA i Azure (stora instanser) erbjuds i två Azure-regioner tre geopolitiska områden (USA, Östra Australien och Europa). Två områden inom ett geopolitiska område där HANA stora instans stämplar är anslutna till kretsar separat dedikerade nätverk. De används för att replikera lagring ögonblicksbilder för att tillhandahålla disaster recovery-metoder. Replikeringen upprättas inte som standard men har angetts för kunder som order disaster recovery-funktioner. Storage-replikering är beroende av användningen av lagringsutrymme ögonblicksbilder för HANA stora instanser. Det går inte att välja en Azure-region som en DR-region som är i ett annat geopolitiska område. 

I följande tabell visar de kombinationer av och stöds för närvarande hög tillgänglighet och katastrofåterställning återställningsmetoder:

| Scenario som stöds i HANA stora instanser | Alternativ för hög tillgänglighet | Återställningsalternativ för katastrofåterställning | Kommentarer |
| --- | --- | --- | --- |
| Enkel nod | Inte tillgängligt. | Dedikerad DR-installationen.<br /> Multipurpose DR-installationen. | |
| Värd för automatisk redundans: N + m<br /> inklusive 1 + 1 | Möjligt med vänteläge tar rollen active.<br /> HANA styr växeln roll. | Dedikerad DR-installationen.<br /> Multipurpose DR-installationen.<br /> DR synkronisering med storage-replikering. | HANA volym anger är kopplade till alla noder (n + m).<br /> DR-plats måste ha samma antal noder. |
| HANA system replikering | Möjligt med primär eller sekundär installationen.<br /> Sekundär flyttar till primär roll i fall växling vid fel.<br /> HANA system replikering och OS kontroll av redundans. | Dedikerad DR-installationen.<br /> Multipurpose DR-installationen.<br /> DR synkronisering med storage-replikering.<br /> DR med hjälp av HANA system replikering är inte möjligt utan tredjepartskomponenter ännu. | Separat uppsättning diskvolymer är kopplade till varje nod.<br /> Endast volymerna på sekundär replik i produktionsplatsen replikeras till DR-plats.<br /> En uppsättning volymer krävs på DR-plats. | 

En dedikerad DR-installationen är där HANA stora instans-enhet i DR-plats inte används för att köra andra arbetsbelastning eller icke-produktion system. Enheten är passiva och distribueras endast om en katastrof redundans körs. Den här installationen är dock inte ett önskade alternativ för många kunder.

> [!NOTE]
> [SAP HANA MCOD distributioner](https://launchpad.support.sap.com/#/notes/1681092) (flera HANA instanser på en enhet) som ovanpå scenarier arbete med hög tillgänglighet och Katastrofåterställning metoder som visas i tabellen. Ett undantag är användning av HANA System replikering med en automatisk redundanskluster utifrån Pacemaker. Sådana fall stöder bara en HANA instans per enhet. För [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) distributioner, endast icke-storage-baserade hög tillgänglighet och Katastrofåterställning metoder fungerar om mer än en klient har distribuerats. Med en klient som distribueras är alla metoder som anges giltiga.  

En multipurpose DR-installationen är där enheten HANA stora instans på DR-plats körs en icke-produktion arbetsbelastning. Montera replikerad lagring (ytterligare) volym anger händelse av katastrof, stänga av icke-produktion-systemet och starta produktion HANA-instans. De flesta kunder som använder funktionen HANA stora instans disaster recovery använder den här konfigurationen. 


Du hittar mer information om hög tillgänglighet för SAP HANA i SAP följande artiklar: 

- [Vitboken för SAP HANA hög tillgänglighet](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA Administrationsguide](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy videon på SAP HANA System-replikering](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP stöd Obs #1999880 – vanliga frågor och svar på SAP HANA System-replikering](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP stöd Obs #2165547 – SAP HANA tillbaka in och återställa inom SAP HANA-systemmiljön replikering](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP stöd Obs #1984882 – med SAP HANA System replikering för maskinvara Exchange med minsta/noll avbrottstid](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Nätverksöverväganden för katastrofåterställning med HANA stora instanser

Om du vill dra nytta av disaster recovery-funktionerna i HANA stora instanser måste du utforma nätverksanslutning till två Azure-regioner. Du behöver en Azure ExpressRoute-krets anslutning från lokala i dina viktigaste Azure-region och en annan krets anslutning från lokalt till din disaster recovery-region. Det här måttet beskriver en situation där det finns ett problem i en Azure-region, inklusive en Microsoft Enterprise Edge Router (MSEE) plats.

Du kan ansluta alla virtuella Azure-nätverk som ansluter till SAP HANA i Azure (stora instanser) i en region till en ExpressRoute-krets som ansluter HANA stora instanser i andra region som en andra åtgärd. Med den här *mellan ansluta*, tjänster som körs på en Azure virtuellt nätverk i regionen 1 kan ansluta till HANA stora instans enheter i Region 2 och tvärtom. Det här måttet adresser fall där endast en av platserna som MSEE som ansluter till din lokala plats med Azure försätts offline.

Följande bild illustrerar en flexibel konfiguration för disaster recovery fall:

![Bästa konfigurationen för katastrofåterställning](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Andra krav med HANA stora instanser storage-replikering för katastrofåterställning

Utöver de föregående kraven för disaster recovery-installationen med HANA stora instanser måste du:

- Order SAP HANA på Azure (stora instanser) SKU: er av samma storlek som din produktion SKU: er och distribuerar dem i disaster recovery region. I de aktuella kunddistributioner för dessa instanser att köra icke-produktion HANA instanser. De här konfigurationerna kallas *multipurpose DR inställningar*.   
- Ordna ytterligare lagringsutrymme på DR-plats för varje din SAP HANA på Azure (stora instanser) SKU: er som du vill återställa i disaster recovery-platsen. Köpa ytterligare lagringsutrymme kan du allokera lagringsvolymer. Du kan allokera volymer som är mål för storage-replikering från produktionen Azure-region i Azure-region för katastrofåterställning.

 

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

En av de viktigaste aspekterna att operativsystemet databaser är att skydda dem från katastrofer. Orsaken till dessa händelser kan vara allt från naturkatastrof enkel fel.

Säkerhetskopiera en databas med möjlighet att återställa den till valfri punkt i tiden (exempelvis innan någon bort kritiska data), gör återställningen till ett tillstånd som så nära som möjligt med det som finns innan avbrott.

Två typer av säkerhetskopior måste utföras för bästa resultat:

- Säkerhetskopiorna: fullständiga, inkrementella eller differentiella säkerhetskopieringar
- Säkerhetskopieringar av transaktionsloggen

Förutom fullständig databasen säkerhetskopiering utförs på en nivå för programmet, kan du utföra säkerhetskopieringar med lagring ögonblicksbilder. Lagring ögonblicksbilder ersätter inte säkerhetskopieringar av transaktionsloggen. Säkerhetskopieringar av transaktionsloggen är viktigt att återställa databasen till en viss tidpunkt eller tömma loggar från redan genomförda transaktioner. Lagring ögonblicksbilder kan påskynda återställning genom att snabbt tillhandahålla en sammanslagning vanlig bild av databasen. 

SAP HANA i Azure (stora instanser) erbjuder två alternativ för säkerhetskopiering och återställning:

- Själv (DIY). När du beräknar för att säkerställa att det finns tillräckligt med ledigt diskutrymme, utföra fullständig databasen och säkerhetskopior av med någon av följande metoder för disk-säkerhetskopiering. Du kan säkerhetskopiera antingen direkt på volymer som är kopplad till HANA stora instans enheter eller till filen resurser NFS (Network) som har ställts in på en Azure virtuell dator (VM). I det senare fallet kunder konfigurera en Linux-VM i Azure, koppla Azure Storage till den virtuella datorn och dela lagring via en konfigurerad NFS-server i den virtuella datorn. Om du säkerhetskopierar mot volymer som kopplas direkt till HANA stora instans enheter, måste du kopiera säkerhetskopieringar till Azure storage-konto (när du har skapat en virtuell Azure-dator som exporterar NFS-resurser som är baserade på Azure Storage). Du kan också använda en Azure-säkerhetskopieringsvalvet eller Azure kall lagring. 

   Ett annat alternativ är att använda ett verktyg för skydd av data från tredje part för lagring av säkerhetskopior när de har kopierats till ett Azure storage-konto. Alternativet själv säkerhetskopiering kan också krävas för data som du behöver lagra under längre perioder för regelefterlevnad och granskning. I samtliga fall kopieras säkerhetskopiorna till NFS-resurser visas via en virtuell dator och Azure Storage.

- Infrastruktur för säkerhetskopiering och återställning av funktioner. Du kan också säkerhetskopiera och återställa funktioner som innehåller den underliggande infrastrukturen för SAP HANA i Azure (stora instanser). Det här alternativet uppfyller behovet av säkerhetskopieringar och snabb återställning. Resten av det här avsnittet adresser säkerhetskopiering och återställning av funktionerna som erbjuds med HANA stora instanser. Det här avsnittet beskriver också relationen säkerhetskopiering och återställning måste disaster recovery funktionerna som erbjuds av HANA stora instanser.

>   [!NOTE]
>   Snapshot-teknik som används av HANA stora instanser underliggande infrastruktur har ett beroende på SAP HANA ögonblicksbilder. Nu fungerar SAP HANA ögonblicksbilder inte tillsammans med flera innehavare av behållare för SAP HANA multitenant-databasen. Om bara en klient har distribuerats, SAP HANA ögonblicksbilder fungerar och den här metoden kan användas.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Med hjälp av ögonblicksbilder för lagring av SAP HANA i Azure (stora instanser)

Lagringsinfrastruktur som underliggande SAP HANA i Azure (stora instanser) stöder lagring ögonblicksbilder av volymer. Både säkerhetskopiering och återställning av volymer stöds, med följande:

- I stället för fullständiga databassäkerhetskopieringar tas ögonblicksbilder av lagring regelbundet.
- När utlösa en ögonblicksbild över /hana/data och /hana/shared (inklusive /usr/sap) initierar en SAP HANA ögonblicksbild innan körs lagring ögonblicksbilden volymer, teknik för ögonblicksbilder. SAP HANA ögonblicksbilden är installationen avseende eventuell loggen återställningar efter återställningen av ögonblicksbilden lagring.
- När lagring ögonblicksbilden har körts utan problem, SAP HANA ögonblicksbilden har tagits bort.
- Säkerhetskopieringar av transaktionsloggen tas ofta och lagras i /hana/logbackups volymen eller i Azure. Du kan utlösa /hana/logbackups volymen som innehåller säkerhetskopieringarna av transaktionsloggen för att ta en ögonblicksbild separat. I så fall behöver du inte köra en HANA ögonblicksbild.
- Om du måste återställa en databas till en viss punkt i tiden, begär den Microsoft Azure-supporten (för ett avbrott i produktion) eller en SAP HANA på Azure-tjänsthantering återställning till en viss lagring ögonblicksbild. Ett exempel är en planerad återställning av ett system med begränsat till det ursprungliga tillståndet.
- SAP HANA-ögonblicksbilden som ingår i ögonblicksbilden lagring är en offset punkt för säkerhetskopieringar av transaktionsloggen som har körts och lagrade när lagring ögonblicksbilden togs.
- Dessa säkerhetskopieringar av transaktionsloggen vidtas för att återställa databasen till en viss punkt i tiden.

Du kan utföra lagring ögonblicksbilder målobjekt för tre klasser av volymer:

- En kombinerad ögonblicksbild över hana-data och /hana/shared (inklusive/usr/sap). Den här ögonblicksbilden kräver en SAP HANA-ögonblicksbild skapas som förberedelse för lagring ögonblicksbilden. SAP HANA-ögonblicksbild ser till att databasen är i ett konsekvent tillstånd från en lagring synvinkel. Och att för återställningen bearbeta som är en plats för att ange upp på.
- En separat ögonblicksbild över/hana/logbackups.
- En partition av operativsystemet.


### <a name="storage-snapshot-considerations"></a>Överväganden för ögonblicksbild av lagring

>[!NOTE]
>Lagring ögonblicksbilder använda lagringsutrymme som har allokerats till HANA stora instans-enheter. Du behöver tänka på följande aspekter av schemaläggning av lagring ögonblicksbilder och hur många ögonblicksbilder som lagring ska lagras. 

Specifika säkerhetsnivån lagring ögonblicksbilder för SAP HANA i Azure (stora instanser) inkluderar:

- En ögonblicksbild av en specifik lagring (vid tidpunkten när den tas) förbrukar lite lagringsutrymme.
- Ögonblicksbilden behöver lagra det ursprungliga innehållet i block, samt ändringarna som dataändringar innehåll och innehållet i filer som ändras på lagringsvolymen SAP HANA-data.
- Därför ökar lagring ögonblicksbilden i storlek. Ju längre ögonblicksbilden finns, desto större blir lagring ögonblicksbilden.
- Flera ändringar som görs till volymen som SAP HANA-databas över livslängden för lagring ögonblicksbild, desto större förbrukningen av diskutrymme för lagring ögonblicksbild.

SAP HANA i Azure (stora instanser) levereras med fast Volymstorlekar för SAP HANA data och loggfilen volymer. Utför ögonblicksbilder av volymerna eats i utrymmet på volymen. Du måste avgöra när du ska planera lagring ögonblicksbilder. Du måste också övervaka förbrukningen av diskutrymme för lagring volymerna, samt hantera antalet ögonblicksbilder som du lagrar. Du kan inaktivera lagring-ögonblicksbilder när du importerar stora mängder data eller utföra andra viktiga ändringar i HANA-databasen. 


Följande avsnitt innehåller information för att utföra dessa ögonblicksbilder, inklusive allmänna rekommendationer:

- Även om maskinvaran kan klara 255 ögonblicksbilder per volym, som du vill hålla betydligt lägre än detta antal.
- Innan du utför lagring av ögonblicksbilder, övervaka och hålla reda på ledigt utrymme.
- Minska antalet lagring ögonblicksbilderna baserat på ledigt utrymme. Du kan sänka antal ögonblicksbilder som du behåller eller du kan utöka volymer. Du kan ordna ytterligare lagringsutrymme i enheter som 1 TB.
- Inaktivera lagring ögonblicksbilder på volymen /hana/data under aktiviteter, till exempel data flyttas till SAP HANA med Migreringsverktyg för SAP-plattformen (R3load) eller återställa SAP HANA-databaser från säkerhetskopior. 
- Under större omorganisering av SAP HANA tabeller bör lagring ögonblicksbilder undvikas, om möjligt.
- Lagring ögonblicksbilder är en förutsättning för att dra nytta av haveriet återställningsfunktioner för SAP HANA i Azure (stora instanser).

### <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Krav för att använda ögonblicksbilder för självbetjäning lagring

Kontrollera att Perl är installerad på Linux-operativsystem på servern HANA stora instanser för att säkerställa att ögonblicksbild skriptet körs utan problem. Perl finns förinstallerat på HANA stora instans-enhet. Om du vill kontrollera versionen Perl, använder du följande kommando:

`perl -v`

![Den offentliga nyckeln kopieras genom att köra det här kommandot](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="set-up-storage-snapshots"></a>Konfigurera lagring ögonblicksbilder

Följ dessa steg om du vill konfigurera lagring ögonblicksbilder med HANA stora instanser:
1. Kontrollera att Perl är installerad på Linux-operativsystem på servern HANA stora instanser.
2. Ändra/etc/ssh/ssh\_config att lägga till raden _Macintosh hmac-sha1_.
3. Skapa en SAP HANA säkerhetskopiering användarkonto på huvudnoden för varje SAP HANA-instans som du kör, om tillämpligt.
4. Installera HDB för SAP HANA-klienten på alla stora instanser för SAP HANA-servrar.
5. Skapa en offentlig nyckel för att komma åt det underliggande lagringsinfrastruktur som styr ögonblicksbilder skapas på den första servern SAP HANA stora instanser av varje region.
6. Kopiera skript och konfigurationsfilen från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) till platsen för **hdbsql** i SAP HANA-installationen.
7. Ändra den *HANABackupDetails.txt* fil som krävs för önskad kund-specifikationer.

### <a name="consideration-for-mcod-scenarios"></a>För MCOD scenarier
Om du kör en [MCOD scenariot](https://launchpad.support.sap.com/#/notes/1681092) med flera SAP HANA-instanser på en HANA stora instans enhet, har du separat lagringsvolymer etableras för varje SAP HANA-instanser. Du kan inte initiera separat ögonblicksbilder på varje system för HANA instans-ID (SID) i den aktuella versionen av självbetjäning ögonblicksbild automation. Funktionen ger kontroller för SAP HANA registrerade instanserna av servern i konfigurationsfilen (se nedan) och kör en samtidig ögonblicksbild av alla instanser som är registrerad på enheten volymer.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Steg 1: Installera klienten för SAP HANA HDB

Linux-operativsystem som är installerade på SAP HANA i Azure (stora instanser) innehåller mappar och skript som krävs för att köra ögonblicksbilder för SAP HANA-lagring för säkerhetskopiering och katastrofåterställning återställning. Sök efter nyare versioner i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Den senaste versionen av skript är 3.x. Olika skript kan ha olika utgåvor av mindre inom samma huvudversion.

>[!IMPORTANT]
>När du flyttar från version 2.1 till version 3.0 av skript, Observera att konfigurationsfilen och vissa syntax struktur har ändrats. Se bildtexter i de olika avsnitten. 

Det är ditt ansvar att installera klienten för SAP HANA HDB på HANA stora instans-enheter när du installerar SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Steg 2: Ändra/etc/ssh/ssh\_config

Ändra `/etc/ssh/ssh_config` genom att lägga till den _Macintosh hmac-sha1_ rad som visas här:
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

Om du vill aktivera åtkomst till lagring ögonblicksbild gränssnitt för din klient HANA stort instans, måste du upprätta en procedur inloggning via en offentlig nyckel. På den första SAP HANA på Azure (stora instanser)-server i din klientorganisation, skapar du en offentlig nyckel som används för att komma åt lagringsinfrastrukturen. Den offentliga nyckeln garanterar att ett lösenord inte krävs att logga in på lagring ögonblicksbild gränssnitt. Skapa en offentlig nyckel innebär också att du inte behöver underhålla lösenordsinformation. Kör följande kommando för att generera den offentliga nyckeln i Linux på stora instanser för SAP HANA-servern:
```
  ssh-keygen –t dsa –b 1024
```
Den nya platsen är **_/root/.ssh/id\_dsa.pub**. Ange inte en verklig lösenord, eller så måste du ange lösenordet varje gång du loggar in. I stället väljer **RETUR** två gånger för att ta bort kravet ”ange lösenord” för att logga in.

Se till att den offentliga nyckeln korrigerades som förväntat genom att ändra mappar som ska **/root/.ssh/** och sedan köra den `ls` kommando. Om nyckeln finns kan du kopiera den genom att köra följande kommando:

![Den offentliga nyckeln kopieras genom att köra det här kommandot](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

Nu kontakta SAP HANA på Azure-tjänsthantering och ger dem den offentliga nyckeln. I representant använder den offentliga nyckeln för att registrera det i den underliggande lagringsinfrastruktur som är högg för din klient HANA stora instans.

### <a name="step-4-create-an-sap-hana-user-account"></a>Steg 4: Skapa ett användarkonto för SAP HANA

Du måste skapa ett användarkonto i SAP HANA som lagring ögonblicksbild skript kan använda för att initiera skapandet av ögonblicksbilder för SAP HANA. Skapa ett användarkonto för SAP HANA SAP HANA Studio för detta ändamål. Användaren måste skapas under SYSTEMDB och inte under SID-databasen. Det här kontot måste ha följande behörigheter: **säkerhetskopiering Admin** och **katalog viktigt**. I det här exemplet är användarnamnet **SCADMIN**. Det användarkonto som har skapats i HANA Studio är skiftlägeskänslig. Se till att välja **nr** för att användaren måste ändra lösenordet på deras nästa inloggning.

![Skapa en användare i HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

Om du använder MCOD distributioner med flera SAP HANA-instanser på en enhet, måste du upprepa proceduren för varje SAP HANA-instans.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Steg 5: Ge SAP HANA-användarkonto

I det här steget auktorisera SAP HANA-användarkonto som du har skapat, så att skripten inte behöver skicka lösenord vid körning. Kommandot SAP HANA `hdbuserstore` kan skapa en SAP HANA användarnyckel, vilken lagras på en eller flera SAP HANA-noder. Nyckeln för användare kan användaråtkomst SAP HANA utan att behöva hantera lösenord från inom scripting processen. Scripting processen beskrivs senare i den här artikeln.

>[!IMPORTANT]
>Kör följande kommando som `root`. Annars fungerar skriptet inte korrekt.

Ange den `hdbuserstore` kommandot på följande sätt:

**För icke - MDC HANA installationen**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**För MDC HANA installationen**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

I följande exempel användaren är **SCADMIN01**, värdnamnet är **lhanad01**, och numret för instansen är **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Om du använder en HANA MCOD distribution med flera SAP HANA-instanser på en enhet, måste du Upprepa steget för varje SAP HANA-instans och associerade säkerhetskopiering användare på enheten.

Om du har en SAP HANA skalbar konfiguration som du behöver hantera alla skript från en enskild server. I det här exemplet nyckeln för SAP HANA **SCADMIN01** måste ändras för varje värd på ett sätt som visar vilka värden som är relaterad till nyckeln. Ändra kontot SAP HANA säkerhetskopiering med instans antal HANA-databas. Nyckeln måste ha administratörsbehörighet på värden som tilldelas den och skalbar konfigurationer säkerhetskopiering användaren måste ha åtkomsträttigheter till SAP HANA-instanser. Under förutsättning att de tre skalbar noderna har namn **lhanad01**, **lhanad02**, och **lhanad03**, de kommandon som ser ut så här:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Steg 6: Hämta ögonblicksbild skript, konfigurera ögonblicksbilderna och testa konfiguration och anslutningen

Hämta den senaste versionen av skript från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Kopiera de nedladdade skript och textfilen till arbetskatalog för **hdbsql**. För den aktuella HANA installationer katalogen är i följande format: /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

När du hanterar Perl-skript: 

- Ändra aldrig skript om du uppmanas av Microsoft Operations.
- När du tillfrågas om du vill ändra skriptet eller en parameterfil, Använd alltid Linux textredigerare, till exempel ”vi” och inte en Windows-redigerare som Anteckningar. Formatet kan skadas om du använder en Windows-redigeraren.
- Använd alltid senaste skript. Du kan hämta den senaste versionen från GitHub.
- Använd samma version av skripten i liggande.
- Testa skripten och lär du dig med de parametrar som krävs och utdata från skriptet innan du använder dem direkt i produktionssystemet.
- Ändra inte monteringspunktsnamn på den server som etablerats av Microsoft Operations. Dessa skript är beroende av dessa standard monteringspunkter ska vara tillgängliga för att kunna köras.


Syftet med olika skript och filer är följande:

- **Azure\_hana\_backup.pl**: det här skriptet kommer med verktyget Linux Cron schemaläggning att köra lagring ögonblicksbilder på HANA data och delade volymer, / hana/logbackups volymen eller operativsystemet.
- **Azure\_hana\_replikering\_status.pl**: det här skriptet innehåller grundläggande information kring replikeringsstatus från produktionsplatsen till disaster recovery-plats. Skript-Övervakare för att säkerställa att replikeringen sker och den visar storleken på objekt som replikeras. Det ger också information om en replikering tar för lång eller om länken inte är tillgänglig.
- **Azure\_hana\_ögonblicksbild\_details.pl**: det här skriptet innehåller en lista över grundläggande information om alla ögonblicksbilder, per volym, som finns i din miljö. Det här skriptet kan köras på den primära servern eller på en server-enhet på disaster recovery-plats. Skriptet innehåller följande information, fördelat på varje volym som innehåller ögonblicksbilder:
   * Storleken på totala antalet ögonblicksbilder på en volym
   * Följande information i varje ögonblicksbild i volymen: 
      - Namnet på ögonblicksbilder 
      - Tid för skapande 
      - Storleken på ögonblicksbilden
      - Frekvens av ögonblicksbilden
      - HANA Backup-ID som är associerade med den ögonblicksbilden, om relevant
- **Azure\_hana\_ögonblicksbild\_delete.pl**: det här skriptet tar bort en ögonblicksbild för lagring eller en uppsättning ögonblicksbilder. Du kan använda SAP HANA-ID som hittades i HANA Studio för säkerhetskopiering eller den ögonblicksbild namnet. Backup-ID är för närvarande endast kopplad till ögonblicksbilder som skapats för HANA data/log/klusterdelade volymer. Annars, om ögonblicksbilds-ID anges den syftar alla ögonblicksbilder som matchar det angivna snapshot-ID.  
- **testHANAConnection.pl**: det här skriptet testar anslutningen till SAP HANA-instans och krävs för att konfigurera lagring ögonblicksbilder.
- **testStorageSnapshotConnection.pl**: det här skriptet har två syften. Först ser till att HANA stora instans-enhet som kör skripten har åtkomst till den tilldelade lagring virtuella datorn och till lagringsgränssnittet för ögonblicksbild av dina stora HANA-instanser. Andra syftet är att skapa en tillfällig ögonblicksbild för HANA-instans som du vill testa. Det här skriptet ska köras för varje HANA-instans på en server för att säkerställa att säkerhetskopiering skript fungerar som förväntat.
- **removeTestStorageSnapshot.pl**: det här skriptet tar bort test-ögonblicksbilden som skapades med skriptet **testStorageSnapshotConnection.pl**.
- **Azure\_hana\_dr\_failover.pl**: det här skriptet initierar en DR-växling till en annan region. Skriptet måste köras på enheten HANA stora instans i DR region eller på enheten du vill växla över till. Det här skriptet stoppar storage-replikering från den primära sidan till sekundär sida, återställer den senaste ögonblicksbilden på DR-volymer och ger monteringspunkter för DR volymer.
- **Azure\_hana\_testa\_dr\_failover.pl**: det här skriptet utför ett redundanstest i DR-plats. Till skillnad från skriptet azure_hana_dr_failover.pl avbryter denna inte storage-replikering från primär till sekundär. I stället kloner replikerad lagring volymer skapas på DR-sida och monteringspunkter klonade volymer tillhandahålls. 
- **HANABackupCustomerDetails.txt**: den här filen är en ändringsbar konfigurationsfil som du behöver ändra för att anpassa sig till SAP HANA-konfiguration. Den *HANABackupCustomerDetails.txt* filen är den kontrollen och konfiguration för det skript som körs storage snapshots. Justera filen för ditt syfte och inställningar. Du får den **namn på säkerhetskopia** och **lagring IP-adress** från SAP HANA på Azure-tjänsthantering när distribuerar dina instanser. Du kan inte ändra sekvensen beställning eller avstånd för någon av variablerna i den här filen. Om du gör skript körs inte korrekt. Dessutom får du IP-adressen för noden skala upp eller huvudnoden (om skalbar) från SAP HANA på Azure-tjänsthantering. Du kan också känna HANA-instansnummer som du får under installationen av SAP HANA. Nu, måste du lägga till ett namn i konfigurationsfilen.

För en skala upp eller skalbara distribution ser konfigurationsfilen som i följande exempel när du fylla i servernamnet för HANA stora instans-enhet och serverns IP-adress. Om du använder SAP HANA System Replication använda HANA System replikeringskonfiguration virtuella IP-adress. Fyll i alla fält som behövs för varje SAP HANA-SID som du vill säkerhetskopiera eller återställa.

Du kan också kommentera ut rader förekomster av som du inte vill säkerhetskopiera för en viss tidsperiod genom att lägga till en ”#” framför ett obligatoriskt fält. Du behöver också att ange alla SAP HANA-instanser som finns på en server om det ingen behövs att säkerhetskopiera eller återställa den särskilda instansen. Formatet måste hållas för alla fält, eller alla skript genereras ett felmeddelande och skriptet avslutas. Du kan ta bort ytterligare krävs rader för någon SID information information som du inte använder efter den senaste SAP HANA-instansen används. Alla rader måste vara antingen ifylld, bortkommenterad eller tagits bort.

>[!IMPORTANT]
>Strukturen i filen ändras med övergången från version 2.1 till version 3.0. Om du vill använda skripten 3.0-versionen måste du anpassa konfigurationen filstruktur. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

För varje instans som du konfigurerar på HANA stora instans-enhet, eller för konfigurationen av skalbara, måste du definiera data enligt följande:

    
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
Upprepa den här konfigurationen på varje nod för skalbar och konfigurationer som HANA System replikering. Den här åtgärden säkerställer att i fel fall säkerhetskopior och eventuell lagringsreplikering fortsätter att fungera.   

När du placerar alla konfigurationsdata i den *HANABackupCustomerDetails.txt* fil, kontrollera om konfigurationerna som är korrekta för HANA instansdata. Använd skriptet `testHANAConnection.pl`, som är oberoende av en SAP HANA skala upp eller skalbara konfiguration.

```
testHANAConnection.pl
```

Om du har en SAP HANA skalbar konfiguration kan du kontrollera att master HANA-instansen har åtkomst till alla nödvändiga HANA servrar och instanser. Det finns inga parametrar till testskript, men du måste lägga till dina data till den *HANABackupCustomerDetails.txt* konfigurationsfil för att skriptet ska köras. Endast shell-kommando felkoder returneras så att det inte är möjligt för skriptet till fel kontrollera varje instans. Skriptet ger även så vissa kommentarer att kontrollera.

Om du vill köra skriptet anger du följande kommando:
```
 ./testHANAConnection.pl
```
Om skriptet erhåller har status för HANA instansen, visas ett meddelande som att HANA-anslutning har upprättats.


I nästa steg är att kontrollera anslutningen till lagring baseras på de data du lägga till i den *HANABackupCustomerDetails.txt* configuration filen och sedan köra en test-ögonblicksbild. Innan du kan köra den `azure_hana_backup.pl` skript, måste du köra det här testet. Om en volym innehåller inga ögonblicksbilder, går det inte att fastställa om volymen är tom, eller om det finns ett SSH-fel för att hämta information för ögonblicksbild. Skriptet körs därför två steg:

- Verifierar att klientens lagring virtuell dator och gränssnitt som är tillgängliga för skript att köra ögonblicksbilder.
- En test- eller dummy, ögonblicksbild för varje volym skapas av HANA instans.

Därför ingår HANA-instans som ett argument. Om körningen misslyckas, går det inte att felsökning för anslutning till lagringsplatsen. Även om det finns några fel kontrollerar, innehåller skriptet användbara tips.

1. Kör de kommandon för att utföra det här testet:

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   Både användarnamn för lagring och lagring IP-adress har angetts till dig vid övergång av HANA stora instans-enhet.

2. Kör testskriptet:
   ```
    ./testStorageSnapshotConnection.pl <HANA SID>
   ```

Skriptet försöker logga in till lagring med hjälp av den offentliga nyckeln som anges i föregående steg i installationen och med de data som konfigurerats i den *HANABackupCustomerDetails.txt* fil. Om inloggningen lyckas visas följande innehåll:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Om det uppstår i anslutning till lagring konsolen utdata ser ut så här:

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

Efter en lyckad inloggning till lagring virtuella gränssnitt, skriptet fortsätter med fas 2 och skapar en test-ögonblicksbild. Utdata visas här för att en skalbar konfigurera tre noder SAP HANA:

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

Om testet ögonblicksbilden har körts har med skript, kan du fortsätta med att konfigurera faktiska storage snapshots. Om du inte lyckas kan du undersöka problem innan du går vidare. Testa ögonblicksbilden ska vara runt tills de första verkliga ögonblicksbilderna är klar.


### <a name="step-7-perform-snapshots"></a>Steg 7: Utföra ögonblicksbilder

När de förberedande stegen är klar kan du börja konfigurera faktiska ögonblicksbild lagringskonfiguration. Skriptet ska schemaläggas fungerar med SAP HANA skala upp och skala ut konfigurationer. Periodiska och regelbundna körningen av skriptet säkerhetskopiering, schemalägga i skriptet med hjälp av verktyget cron. 

Du kan skapa tre olika typer av säkerhetskopior av ögonblicksbilder:
- **HANA**: en kombinerad ögonblicksbildsäkerhetskopia där volymer som innehåller/hana/data och hana/delade (som innehåller /usr/sap samt) omfattas av samordnade ögonblicksbilden. Det går en enda filåterställning från ögonblicksbilden.
- **Loggar**: en ögonblicksbildsäkerhetskopia av/hana/logbackups volymen. Ingen ögonblicksbild av HANA utlöses för att köra den här ögonblicksbilden för lagring. Den här lagringsvolymen är avsedd att innehålla loggsäkerhetskopior för SAP HANA-transaktion. Dessa utförs oftare för att begränsa loggen tillväxt och förhindra dataförlust. Det går en enda filåterställning från ögonblicksbilden. Inte lägre frekvensen under 3 minuter.
- **Start**: en ögonblicksbild av volymen som innehåller start logiskt enhetsnummer (LUN) för den stora HANA-instansen. Den här säkerhetskopian kan endast med typ I SKU: er av HANA stora instanser. Du kan inte utföra en fil återställer från en ögonblicksbild av volymen som innehåller start LUN.


>[!NOTE]
> Anropssyntaxen för dessa tre typer av ögonblicksbilder ändras med övergången till version 3.0 skript, vilket stöder MCOD distributioner. Det finns inget behov av att ange en instans HANA SID längre. Du måste se till att SAP HANA-instanser av en enhet konfigureras i konfigurationsfilen *HANABackupCustomerDetails.txt*.

>[!NOTE]
> När du kör skriptet för första gången, kan den visar flera oväntade fel i miljön med multi-SID. Köra skriptet åtgärdar problemet.



Nya anropssyntaxen för att köra ögonblicksbilder för lagring med skriptet *azure_hana_backup.pl* ser ut så här:

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

Information om parametrarna är följande: 

- Den första parametern karaktäriserar ögonblicksbild säkerhetskopieringstyp. Godkända värden är **hana**, **loggar**, och **Start**. 
- Parametern **<HANA Large Instance Type>** krävs för start volymen säkerhetskopieringar. Det finns två giltiga värden med ”TypeI” eller ”TypeII” beroende HANA stora instans enhet. Ta reda på vilken typ din enhet är, se [SAP HANA (stora instanser) översikt och arkitektur för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- Parametern **< snapshot_prefix >** är en ögonblicksbild eller en etikett för säkerhetskopia för typ av ögonblicksbild. Den har två syften: en är att ge det ett namn så att du vet vad de här ögonblicksbilderna om. Andra syftet är att skriptet *azure\_hana\_backup.pl* att fastställa antalet ögonblicksbilder för lagring som bevaras under den särskilda etiketten. Om du schemalägger två säkerhetskopior av lagring ögonblicksbilder av samma typ (t.ex. **hana**), med två olika etiketter och definiera att 30 ögonblicksbilder bör hållas för varje kan du få 60 lagring ögonblicksbilder av volymerna som påverkas. 
- Parametern **< snapshot_frequency >** är reserverad för framtida utveckling och inte har någon effekt. Ange det till ”3min” vid körning av säkerhetskopior av typen **loggen**, och ”15 min” när du kör andra typer.
- Parametern **<number of snapshots retained>** definierar lagring av ögonblicksbilder indirekt genom att definiera antalet ögonblicksbilder med samma ögonblicksbild prefix (etikett). Den här parametern är viktigt för schemalagda körningar via cron. Om antalet ögonblicksbilder med samma snapshot_prefix överskrider det antal som anges av den här parametern, raderas den äldsta ögonblicksbilden innan du kör en ny ögonblicksbild för lagring.

För en skalbar utför skriptet ytterligare kontroller för att säkerställa att du har åtkomst till alla HANA-servrar. Skriptet kontrollerar också att alla instanser av HANA returnera rätt status för instanserna innan du skapar den en SAP HANA-ögonblicksbild. SAP HANA ögonblicksbilden följs av en ögonblicksbild för lagring.

Körningen av skriptet `azure_hana_backup.pl` skapar lagringen ögonblicksbild i följande tre faser:

1. Kör en SAP HANA-ögonblicksbild
2. Kör en ögonblicksbild för lagring
3. Tar bort den SAP HANA-ögonblicksbilden som skapades innan körningen av lagring ögonblicksbild

Anropa den för att köra skriptet från HDB körbara mappen som den kopierades. 

Kvarhållningsperioden administreras med antalet ögonblicksbilder som skickas som en parameter när du kör skriptet. Hur lång tid som täcks av ögonblicksbilder för lagring är en funktion av körningen och antalet ögonblicksbilder som har skickats som en parameter när du kör skriptet. Om antalet ögonblicksbilder som hålls överskrider det antal som namnges som en parameter i anropet av skriptet, raderas den äldsta ögonblicksbilden för lagring av samma etikett innan en ny ögonblicksbild körs. Det tal som du ger den sista parametern för anropet är det tal du kan använda för att styra hur många ögonblicksbilder som hålls. Med det här numret kan du också styra, indirekt diskutrymmet som används för ögonblicksbilder. 

> [!NOTE]
>När du ändrar etiketten startas inventeringen igen. Du måste vara strikt i etiketter, så att din ögonblicksbilder inte tas bort av misstag.

### <a name="snapshot-strategies"></a>Strategier för ögonblicksbild
Frekvensen för ögonblicksbilder för olika typer av beror på om du använder funktionen HANA stora instans disaster recovery. Den här funktionen är beroende av lagring ögonblicksbilder som kan kräva särskilda rekommendationer för frekvens och körningen perioder lagring ögonblicksbilder. 

I överväganden och rekommendationer som följer kan antas att du gör *inte* använder funktionen disaster recovery som erbjuder HANA stora instanser. I stället använder du lagring ögonblicksbilder att ha säkerhetskopior och kunna ange punkten i tidsåterställningen för de senaste 30 dagarna. Begränsningar för antalet ögonblicksbilder och utrymme får anses kunder följande krav:

- Tiden för återställning för point-in-time-återställning.
- Hur mycket diskutrymme.
- Återställningspunkt och målen för potentiella återställning från en katastrof.
- Eventuell körningen av HANA full-databassäkerhetskopieringar mot diskar. När en fullständig databassäkerhetskopia mot diskar eller **backint** gränssnittet utförs misslyckas körningen av ögonblicksbilder för lagring. Om du planerar att köra fullständiga databassäkerhetskopieringar ovanpå lagring ögonblicksbilder, kontrollera att har körningen av lagring ögonblicksbilder inaktiverats under denna tid.
- Antalet ögonblicksbilder per volym (begränsat till 255).


För kunder som inte använder disaster recovery-funktionerna i HANA stora instanser är ögonblicksbild perioden mer sällan. I sådana fall kan kunder utföra kombinerade ögonblicksbilder på /hana/data och /hana/shared (inklusive /usr/sap) under perioder som 12- eller 24 timmar, och de hålla ögonblicksbilder för en månad. Detsamma gäller ögonblicksbilder av säkerhetskopiering loggvolym. Körningen av SAP HANA säkerhetskopieringar av transaktionsloggen mot loggvolym för säkerhetskopiering sker dock i 5 minuter till 15 minuter perioder.

Schemalagda lagring ögonblicksbilder utförs bäst med hjälp av cron. Använda samma skript för alla säkerhetskopiering och återställning krasch och att du ändrar skriptet indata för att matcha olika begäras säkerhetskopieringstiden. Dessa ögonblicksbilder är alla schemalagda annorlunda i cron beroende på deras körningstid: varje timme, 12 timmar, varje dag eller varje vecka. 

Följande är ett exempel på ett cron-schema i/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
I exemplet ovan är en ögonblicksbild av varje timme kombinerade som täcker de volymer som innehåller data/hana/och /hana/shared (inklusive/usr/sap) platser. Använd den här typen av ögonblicksbilder för en snabbare återställning i tidpunkt under de senaste två dagarna. Det finns också en ögonblicksbild på dessa volymer. Alltså ha två dagars täckning av varje timme ögonblicksbilder, samt fyra veckor efter täckning av dagliga ögonblicksbilder. Dessutom säkerhetskopieras säkerhetskopiering transaktionsvolymer loggen varje dag. Dessa säkerhetskopior hålls samt 4 veckor. Som du ser i den tredje raden i crontab har säkerhetskopia av transaktionsloggen HANA schemalagts för körning var femte minut. Olika cron-jobb som kör lagring ögonblicksbilder starttider ut, så att dessa ögonblicksbilder inte körs på samma gång vid en viss punkt i tiden. 

I följande exempel utför du en kombinerad ögonblicksbild som täcker de volymer som innehåller/hana/data och hana/delade (inklusive/usr/sap) platser på en timme. Du kan behålla dessa ögonblicksbilder för två dagar. Ögonblicksbilder av loggen säkerhetskopiering transaktionsvolymer utförs på grundval av 5 minuter och hålls 4 timmar. Som har tidigare, säkerhetskopiering av HANA transaktionsloggfilen schemalagts för körning var femte minut. Ögonblicksbilden av säkerhetskopian transaktionsvolymer loggen har utförts med en fördröjning på 2 minuter efter loggsäkerhetskopiering transaktion har startats. Loggsäkerhetskopiering för SAP HANA transaktionen ska slutföras under normala omständigheter inom de två minuterna. Som innan, volymen som innehåller startavbildningen LUN backas upp en gång per dag av en ögonblicksbild för lagring och bevaras i fyra veckor.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

Följande bild illustrerar sekvenser av det tidigare exemplet, exklusive start LUN:

![Förhållandet mellan säkerhetskopieringar och ögonblicksbilder](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA utför regelbundna skrivningar till /hana/log volymen för att dokumentera utförts ändringar till databasen. Med jämna mellanrum skriver SAP HANA en lagringspunkt till /hana/data volymen. Som anges i crontab körs en säkerhetskopia av SAP HANA-transaktionslogg var femte minut. Du kan också se en SAP HANA-ögonblicksbild körs varje timme på grund av utlösa en ögonblicksbild av en kombinerad lagring över /hana/data och /hana/shared volymer. När ögonblicksbilden HANA lyckas, utförs ögonblicksbilden av kombinerade lagring. Som finns beskrivet i crontab körs lagring ögonblicksbilden på volymen /hana/logbackup var 5 minuter, cirka 2 minuter efter säkerhetskopieringen i HANA transaktionslogg.

> [!NOTE]
>Om du schemalägger lagring säkerhetskopior av ögonblicksbilder på två noder i ett HANA System Replication-installationen måste se till att körningar av säkerhetskopior av ögonblicksbilder mellan två noder inte överlappar varandra. SAP HANA har en begränsning för att hantera en HANA ögonblicksbild på bara en gång. Eftersom en ögonblicksbild av HANA är en grundläggande komponent i en lyckad lagring ögonblicksbild för säkerhetskopiering, måste du kontrollera att lagring ögonblicksbilden på den primära och sekundära noden och en eventuell tredje nod är tidsgränsen från varandra.


>[!IMPORTANT]
> Användningen av lagringsutrymme ögonblicksbilder för SAP HANA säkerhetskopior är värdefullt bara när ögonblicksbilder utförs tillsammans med SAP HANA säkerhetskopieringar av transaktionsloggen. Dessa säkerhetskopieringar av transaktionsloggen måste omfatta tidsperioder mellan storage snapshots. 

Om du har angett ett åtagande för användare av en punkt-in-time-återställning av 30 dagar, måste du:

- Komma åt en kombinerad lagring ögonblicksbild över /hana/data och /hana/shared som är 30 dagar i extrema fall.
- Har säkerhetskopior av sammanhängande transaktionsloggen som beskriver tiden mellan alla kombinerade lagring ögonblicksbilder. Därför måste äldsta ögonblicksbilden av säkerhetskopian transaktionsvolymer loggen vara 30 dagar gamla. Detta är inte om du kopierar säkerhetskopieringarna av transaktionsloggen till en annan NFS-resurs som finns på Azure-lagring. I så fall kan du dra gamla säkerhetskopior av transaktionsloggen från den NFS-resursen.

Om du vill dra nytta av ögonblicksbilder för lagring och eventuell storage-replikering för säkerhetskopiering av transaktionsloggar, måste du ändra sökvägen till vilken SAP HANA skriver säkerhetskopieringarna av transaktionsloggen. Du kan göra den här ändringen i HANA Studio. Även om SAP HANA säkerhetskopierar automatiskt fullständig logg segment, bör du ange ett intervall för logg-säkerhetskopiering för att vara entydig. Detta gäller särskilt när du använder alternativet disaster recovery eftersom du vanligtvis vill köra säkerhetskopior med en deterministisk punkt. I följande fall ställs 15 minuter som loggen säkerhetskopieringsintervallet.

![Schemalägga SAP HANA säkerhetskopieringsloggar i SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Du kan också välja säkerhetskopior som är oftare än var 15: e minut. En mer frekventa inställning används ofta tillsammans med disaster recovery funktionaliteten hos HANA stora instanser. Vissa kunder utföra säkerhetskopieringar av transaktionsloggen var femte minut.  

Om databasen inte har säkerhetskopierats, är det sista steget att säkerhetskopiera filbaserad databas om du vill skapa en enkel säkerhetskopiering posten som måste finnas i katalogen för säkerhetskopieringen. Annars kan inte SAP HANA initiera de angivna säkerhetskopiorna.

![Säkerhetskopiera filbaserade att skapa en enda post för säkerhetskopiering](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Efter de första lyckade lagring ögonblicksbilderna har utförts, du kan ta bort ögonblicksbilden test som har utförts i steg 6. Det gör du genom att köra skriptet `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl <hana instance>
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


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Övervakning av antal och storlek ögonblicksbilder på volymen

Du kan övervaka antalet ögonblicksbilder och lagringförbrukningen av dessa ögonblicksbilder på en specifik lagringsvolym. Den `ls` kommandot visas inte ögonblicksbild katalogen eller filer. Dock Linux OS-kommandot `du` innehåller information om dessa ögonblicksbilder för lagring, eftersom de lagras på samma volymer. Kommandot kan användas med följande alternativ:

- `du –sh .snapshot`: Det här alternativet innehåller alla ögonblicksbilder i katalogen ögonblicksbild totalt.
- `du –sh --max-depth=1`: Det här alternativet visas alla ögonblicksbilder som sparats i den **.snapshot** mapp och storleken på varje ögonblicksbild.
- `du –hc`: Det här alternativet får den totala storleken som används av alla ögonblicksbilder.

Använd dessa kommandon för att se till att ögonblicksbilder som tas och lagras inte som förbrukar all lagring på volymer.

>[!NOTE]
>Ögonblicksbilder av startavbildningen LUN visas inte med de tidigare kommandona.

### <a name="getting-details-of-snapshots"></a>Hämta information om ögonblicksbilder
Om du vill ha mer information om ögonblicksbilder, du kan också använda skriptet `azure_hana_snapshot_details.pl`. Det här skriptet kan köras på någon av platserna om det finns en aktiv server på disaster recovery-plats. Skriptet innehåller följande utdata, fördelat på varje volym som innehåller ögonblicksbilder: 
   * Storleken på totala antalet ögonblicksbilder på en volym
   * Följande information i varje ögonblicksbild i volymen: 
      - Namnet på ögonblicksbilder 
      - Tid för skapande 
      - Storleken på ögonblicksbilden
      - Frekvens av ögonblicksbilden
      - HANA Backup-ID som är associerade med den ögonblicksbilden, om relevant

Följande är ett exempel på skript körning syntax:

```
./azure_hana_snapshot_details.pl 
```

Eftersom skriptet försöker hämta HANA säkerhetskopiering ID: T måste den ansluta till SAP HANA-instans. Den här anslutningen kräver konfigurationsfilen *HANABackupCustomerDetails.txt* anges korrekt. Utdata för två ögonblicksbilder på en volym kan se ut ungefär så här:

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


### <a name="file-level-restore-from-a-storage-snapshot"></a>Filnivå återställning från en ögonblicksbild för lagring
För typer ögonblicksbild **hana** och **loggar**, du kan få åtkomst till ögonblicksbilder direkt på volymer i den **.snapshot** directory. Det finns en underkatalog för varje ögonblicksbilder. Du kan kopiera varje fil i tillståndet den var i vid ögonblicksbilden från underkatalogen i den faktiska katalogstrukturen.

>[!NOTE]
>En fil fungerar inte återställningen för ögonblicksbilder av startavbildningen LUN som är oberoende av typ av HANA stora instans-enheter. Den **.snapshot** directory exponeras inte i filen Boot.ini LUN. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Minska antalet ögonblicksbilder på en server

Du kan minska antalet vissa etiketter ögonblicksbilder som du lagrar som beskrivits tidigare. De två sista parametrarna för kommandot för att initiera en ögonblicksbild är etiketten och antalet ögonblicksbilder som du vill behålla.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

I det förra exemplet ögonblicksbild etiketten är **dailyhana** och antalet ögonblicksbilder med den här etiketten ska behållas är **28**. När du har besvarat förbrukningen av diskutrymme kan du vill minska antalet lagrade ögonblicksbilder. Enkelt sätt att minska antalet ögonblicksbilder till 15, till exempel är att köra skriptet med den sista parametern som angetts till **15**:

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

Om du kör skriptet med den här inställningen är antalet ögonblicksbilder, inklusive den nya lagring ögonblicksbilden 15. De senaste 15 ögonblicksbilderna hålls och 15 äldre ögonblicksbilderna tas bort.

 >[!NOTE]
 > Det här skriptet minskar antalet ögonblicksbilder bara om det finns fler än en timme gammal ögonblicksbilder. Skriptet tar inte bort ögonblicksbilderna som är mindre än 1 timme gamla. Dessa begränsningar är relaterade till de valfria disaster recovery-funktionerna som erbjuds.

Om du inte längre vill hantera en uppsättning ögonblicksbilder med säkerhetskopiering etiketten **hanadaily** i syntaxexemplen, kan du köra skriptet med **0** som kvarhållning tal. Alla ögonblicksbilder matchar etiketten tas sedan bort. Ta bort alla ögonblicksbilder kan dock påverka funktionerna i HANA stora instanser disaster recovery-funktioner.

Ett andra alternativ för att ta bort specifika ögonblicksbilder är att använda skriptet `azure_hana_snapshot_delete.pl`. Det här skriptet är utformat för att ta bort en ögonblicksbild eller en uppsättning ögonblicksbilder antingen genom att använda HANA säkerhetskopiering ID som hittades i HANA Studio eller via namnet i sig ögonblicksbild. Säkerhetskopiering ID är för närvarande endast knuten till ögonblicksbilder som skapats för den **hana** ögonblicksbilder. Ögonblicksbilder av säkerhetskopior av typen **loggar** och **Start** inte utföra en SAP HANA-ögonblicksbild och så det finns ingen säkerhetskopiering ID för dessa ögonblicksbilder. Om ögonblicksbilder namnet anges efter alla ögonblicksbilder i olika volymer som matchar namnet på angivna ögonblicksbilder. 

Anropa skriptet måste du ange SID HANA-instans med hjälp av anropssyntaxen av skriptet:

```
./azure_hana_snapshot_delete.pl <SID>

```

Kör skriptet som användare **rot**.

Om du väljer en ögonblicksbild, kan du ta bort varje ögonblicksbild individuellt. Du först ange volymen som innehåller ögonblicksbilden och ange sedan namnet på ögonblicksbilder. Om ögonblicksbilden finns på volymen och är mer än en timme gammal, tas bort. Du hittar det volymnamn på och ögonblicksbild genom att köra den `azure_hana_snapshot_details` skript. 

>[!IMPORTANT]
>Om det finns data som endast finns på ögonblicksbilden tas som bort när ögonblicksbilden har tagits bort, att data går förlorade permanent.

   

### <a name="recover-to-the-most-recent-hana-snapshot"></a>Återställ till den senaste HANA ögonblicksbilden

I ett scenario för produktion och ned, kan du initiera processen för att återställa från en ögonblicksbild av lagring som en kundincident med Microsoft Azure-supporten. Det är en hög angelägenhetsgrad fråga om data har tagits bort i en produktionssystem och det enda sättet att hämta det är att återställa produktionsdatabasen.

I annat fall punkten i tidsåterställningen kanske låg angelägenhetsgrad och planerade dagar i förväg. Du kan planera återställningen med SAP HANA på Azure-tjänsthantering i stället för att höja flaggan hög prioritet. Du kan till exempel planerar att uppgradera SAP-program genom att använda ett nytt paket förbättring. Du måste sedan återgår till en ögonblicksbild som representerar tillstånd innan uppgraderingen förbättring av paketet.

Innan du skickar begäran, måste du förbereda. SAP HANA på Azure Service Management-teamet kan sedan hantera begäran och ge de återställda volymerna. Därefter kan återställa du HANA-databas utifrån ögonblicksbilderna. 

Nedan visas hur du förbereder för begäran:

>[!NOTE]
>Användargränssnittet kan skilja sig från följande skärmbilderna, beroende på den SAP HANA-versionen som du använder.

1. Bestäm vilken ögonblicksbild för att återställa. Endast hana/datavolym återställs om du instruera annars. 

2. Stäng HANA-instans.

 ![Stäng HANA-instans](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Avmontera datavolymerna på varje nod för HANA-databas. Om datavolymerna är fortfarande monterade för operativsystemet, misslyckas återställningen av ögonblicksbilden.
 ![Avmontera datavolymerna på varje nod för HANA-databas](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Öppna en Azure-supporten om begäran och innehåller instruktioner för återställning av en specifik ögonblicksbild.

 - Under återställningen: SAP HANA på Azure-tjänsthantering kanske du ombeds att delta i ett konferenssamtal så samordning, verifiering och bekräfta att rätt lagring ögonblicksbild har återställts. 

 - Efter återställningen: SAP HANA på Azure-tjänsthantering meddelar dig när lagring ögonblicksbild har återställts.

5. Montera alla datavolymer när återställningsprocessen har slutförts.

 ![Montera alla datavolymer](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Välj återställningsalternativ SAP HANA Studio, om de inte automatiskt kommer när du återansluter till HANA DB via SAP HANA Studio. I följande exempel visas en återställning till den senaste HANA ögonblicksbilden. En ögonblicksbild av lagring bäddar in en HANA ögonblicksbild. Om du återställer till den senaste ögonblicksbilden av lagring, ska den senaste HANA ögonblicksbilden. (Om du återställer till en tidigare ögonblicksbild av lagring, du måste leta upp HANA ögonblicksbilden baserat på tiden lagring ögonblicksbilden togs.)

 ![Välj alternativ för Återställ SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Välj **återställa databasen till en specifik säkerhetskopierings- eller ögonblicksbild**.

 ![Fönstret Välj återställningstyp](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Välj **ange säkerhetskopiering utan katalog**.

 ![Fönstret Ange plats för säkerhetskopia](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. I den **måltypen** väljer **ögonblicksbild**.

 ![Ange att säkerhetskopiera till Återställ fönstret](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Välj **Slutför** att starta återställningsprocessen.

 ![Välj ”Slutför” om du vill starta återställningen](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA-databas är återställas och återställas till HANA ögonblicksbild som ingår i lagring ögonblicksbilden.

 ![HANA-databas är återställas och återställas till HANA ögonblicksbild](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>Återställ till den senaste status

Följande process återställer HANA ögonblicksbild som ingår i lagring ögonblicksbilden. Den sedan återställer säkerhetskopieringarna av transaktionsloggen senaste tillståndet för databasen innan du återställer lagring ögonblicksbilden.

>[!IMPORTANT]
>Innan du fortsätter, kontrollera att du har en fullständig och sammanhängande kedja av säkerhetskopieringar av transaktionsloggen. Utan dessa säkerhetskopior kan du återställa det aktuella tillståndet för databasen.

1. Slutför steg 1 – 6 i [återställa till den senaste HANA ögonblicksbilden](#recovering-to-the-most-recent-hana-snapshot).

2. Välj **återställa databasen till det senaste tillståndet**.

 ![Välj ”återställa databasen till det senaste tillståndet”](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Ange platsen för de senaste HANA säkerhetskopiorna. Platsen måste innehålla alla de HANA säkerhetskopieringarna av transaktionsloggen från HANA ögonblicksbild till det senaste tillståndet.

 ![Ange platsen för de senaste HANA säkerhetskopiorna](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Välj en säkerhetskopia som en bas som du vill återställa databasen. I det här exemplet är HANA ögonblicksbild i skärmbilden HANA ögonblicksbild som ingick i lagring ögonblicksbilden. 

 ![Välj en säkerhetskopia som en bas som du vill återställa databasen](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Avmarkera den **Använd Delta säkerhetskopieringar** kryssrutan om går inte finns mellan HANA ögonblicksbilder och det senaste tillståndet.

 ![Avmarkera kryssrutan ”Använd Delta säkerhetskopieringar” om det finns inga går](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. På sidan Sammanfattning **Slutför** att starta återställningen.

 ![Klicka på ”Slutför” på fönstret Sammanfattning](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>Återställa till en annan punkt i tiden
Om du vill återställa till en punkt i tiden mellan HANA ögonblicksbilden (ingår i ögonblicksbilden av lagring) och ett som är senare än HANA ögonblicksbild point-in-time-återställning, utför du följande steg:

1. Kontrollera att du har alla de säkerhetskopieringar av transaktionsloggen från HANA ögonblicksbilden för den tid som du vill återställa till.
2. Börjar under [återställa till det senaste tillståndet](#recovering-to-the-most-recent-state).
3. I steg 2 i proceduren i det **ange återställningstyp** väljer **återställa databasen till följande punkt i tiden**, och sedan ange punkten i tid. 
4. Slutför steg 3-6.

### <a name="monitor-the-execution-of-snapshots"></a>Övervaka körning av ögonblicksbilder

När du använder lagring ögonblicksbilder av HANA stora instanser, måste du också att övervaka körning av dessa ögonblicksbilder. Skriptet som kör en ögonblicksbild av lagring skriver utdata till en fil och sparar den på samma plats som Perl-skript. En separat fil skrivs för varje ögonblicksbild för lagring. Utdata från varje fil visar de olika faserna att ögonblicksbild skriptet körs:

1. Söker efter de volymer som behöver skapa en ögonblicksbild.
2. Söker efter ögonblicksbilderna tas från dessa volymer.
3. Tar bort eventuella befintliga ögonblicksbilder för att matcha antalet ögonblicksbilder som du angav.
4. Skapar en SAP HANA-ögonblicksbild.
5. Skapar ögonblicksbilden lagring över volymer.
6. Tar bort ögonblicksbilden för SAP HANA.
7. Byter namn på den senaste ögonblicksbilden på **.0**.

Den viktigaste delen av skriptet cab identifieras är den här delen:
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
Från det här exemplet kan du se hur skriptet poster skapandet av HANA ögonblicksbilden. I fallet med skalbara måste initieras den här processen på huvudnoden. Huvudnoden initierar synkron skapandet av SAP HANA-ögonblicksbilder på varje worker-nod. Därefter tas ögonblicksbilden av lagring. När du har körts lagring ögonblicksbilder HANA ögonblicksbilden har tagits bort. Borttagningen av ögonblicksbilden HANA initieras från huvudnoden.


## <a name="disaster-recovery-principles"></a>Disaster recovery-principer
HANA stora instanser erbjuder en disaster recovery-funktionalitet mellan HANA stora instans tidsstämplar i olika Azure-regioner. Om du distribuerar HANA stora instans enheter i regionen oss Väst Azure kan du till exempel använda HANA stora instans-enheter i region oss Öst som disaster recovery-enheter. Som tidigare nämnts har katastrofåterställning inte konfigurerats automatiskt, eftersom den kräver att betala för en annan HANA stora instans-enhet i DR-region. Installationsprogrammet för disaster recovery fungerar för skala upp samt skalbar inställningar. 

I scenarier som hittills distribueras använda kunder enhet i DR-region för att köra icke-produktion system som använder en installerade HANA-instans. HANA stora instans enheten måste vara av samma SKU: N som SKU: N används i produktionen. Följande bild visar vilka diskkonfigurationen mellan server-enhet i Azure produktionsregion och disaster recovery region ser ut som:

![Konfiguration för DR installation av disk](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

I översikt över bilden visas sedan måste du sortera en annan uppsättning av diskvolymer. Diskvolymer mål har samma storlek som volymerna produktion för produktion instansen i disaster recovery-enheter. Dessa volymer är kopplade till HANA stora instans server-enhet i disaster recovery-platsen. Följande volymer replikeras från produktionsregionen till DR-plats:

- /hana/data
- / hana/logbackups 
- /Hana/Shared (inklusive/usr/sap)

/Hana/log volymen inte har replikerats eftersom transaktionsloggen SAP HANA inte behövs på samma sätt som återställningen från dessa volymer är klar. 

Bas för disaster recovery-funktionerna som erbjuds erbjuds lagringsfunktioner för replikering av HANA stora instans-infrastruktur. Funktionen som används på sidan för lagring är inte en konstant ström av ändringar som replikeras i ett asynkront sätt när ändringar sker till lagringsvolymen. Det är en mekanism som förlitar sig på det faktum att skapa ögonblicksbilder av volymerna regelbundet. Delta mellan en redan replikerade ögonblicksbild och en ny ögonblicksbild som ännu inte har replikerats överförs sedan till webbplatsen disaster recovery till volymer på diskar.  De här ögonblicksbilderna lagras på volymer och när det gäller en disaster recovery-redundans, måste återställas på dessa volymer.  

Den första överföringen av av alla data på volymen ska innan mängden data blir mindre än går mellan ögonblicksbilder. Därför innehåller volymer i DR-plats för ögonblicksbilder som utförs i produktionsplatsen. Slutligen kan du använda DR systemet för att komma till en tidigare status att återställa förlorade data utan att återställa produktionssystemet.

När det gäller MCOD distributioner med flera oberoende SAP HANA-instanser på en enhet för HANA stora instans förväntas att alla SAP HANA-instanser får lagring som replikeras till den DR-sidan.

I fall där du använder HANA System Replication när hög tillgänglighet-funktioner på din plats för produktion, replikeras endast volymer på nivå 2 (eller replik)-instans. Den här konfigurationen kan uppstå en fördröjning i storage-replikering till DR-plats om du behålla eller ta bort den sekundära repliken (nivå 2) server a eller SAP HANA-instans i den här enheten. 


>[!IMPORTANT]
>Precis som med flera skikt HANA System Replication blockerar en avstängning för nivå 2 HANA instans eller server replikering till disaster recovery platsen när du använder funktionen HANA stora instans disaster recovery.


>[!NOTE]
>HANA stora instans replikering lagringsfunktioner spegling och replikering av ögonblicksbilder för lagring. Om du inte utföra lagring ögonblicksbilder som introducerades i den [säkerhetskopiering och återställning](#backup-and-restore) avsnitt i den här artikeln får inte förekomma att replikering ska disaster recovery-platsen. Ögonblicksbild lagringskörning krävs för att storage-replikering till disaster recovery-plats.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Förberedelse av katastrofåterställning
I det här scenariot har du ett produktionssystem som körs på HANA stora instanser i produktion Azure-region. För de steg som följer vi antar att systemet HANA SID är ”PRD” och att du har ett icke-produktion system som kör på HANA stora instanser i DR Azure-region. I det senare vi antar att dess SID är ”TST”. Följande bild visar den här konfigurationen:

![Start av DR-installationen](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Om server-instansen inte har redan beställts med ytterligare lagringsutrymme volymen, SAP HANA på Azure-tjänsthantering bifogar ytterligare uppsättning volymer som mål för produktion repliken till HANA stora instans enhet där du kör TST HANA-instans. Du måste ange SID för din produktion HANA-instans för detta ändamål. När SAP HANA på Azure-tjänsthantering bekräftar av dessa volymer, måste du montera volymerna till HANA stora instans-enhet.

![Nästa steg för DR-installationen](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Nästa steg är att installera andra SAP HANA-instans på HANA stora instans-enhet i DR Azure-region där du kör TST HANA-instans. Den nyligen installerade SAP HANA-instansen måste ha samma SID. Användare som skapas måste ha samma UID och grupp-ID instansens produktion. Om installationen har slutförts, måste du:

- Köra steg 2 av förberedelserna inför ögonblicksbild lagring som beskrivs tidigare i den här artikeln.
- Skapa en offentlig nyckel för DR-enhet för HANA stora instans enhet om du inte ännu har gjort. Se steg 3 i förberedelserna inför ögonblicksbild lagring som beskrivs tidigare i den här artikeln.
- Underhåll av *HANABackupCustomerDetails.txt* med nya HANA instansen och testa om anslutning till lagring fungerar korrekt.  
- Stoppa den nyligen installerade SAP HANA-instansen på HANA stora instans-enhet i DR Azure-region.
- Avmontera volymerna PRD och kontakta SAP HANA på Azure-tjänsthantering. Volymerna kan inte upprätthålla monterade till enheten, eftersom de inte kan nås när fungerar som lagring replikeringsmål.  

![Steget av DR-installationen innan du upprättar replikering](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Driftteamet upprättar replikeringsrelationen mellan PRD volymerna i produktion Azure-region och PRD volymer i DR Azure-region.

>[!IMPORTANT]
>Volymen /hana/log replikeras inte eftersom det inte är nödvändigt att återställa den replikerade SAP HANA-databasen till ett konsekvent tillstånd på disaster recovery-webbplatsen.

Därefter konfigurera eller justera lagring ögonblicksbild Säkerhetskopieringsschemat att komma till din RTO och Återställningspunktmål i fallet för katastrofåterställning. För att minimera återställningspunktmålet, anger du följande replikeringsintervall i tjänsten HANA stora instans:
- För volymer som omfattas av den kombinerade ögonblicksbilden (ögonblicksbilder **hana**), uppsättning att replikera var 15 minuter att motsvarande lagring volym mål i disaster recovery-plats.
- För säkerhetskopiering transaktionsvolymer logg (ögonblicksbilder **loggar**), ange att replikera var 3: e minut på volym-mål för motsvarande lagring på webbplatsen disaster recovery.

För att minimera återställningspunktmålet, ställer du in följande:
- Utföra en **hana** typen lagring ögonblicksbild (finns i ”steg 7: utföra ögonblicksbilder”) var 30: e minut 1 timme.
- Utföra säkerhetskopieringar av transaktionsloggen SAP HANA var femte minut.
- Utföra en **loggar** skriver lagringsutrymme som ögonblicksbilden var 5-15: e minut. Med det här intervallet perioden kan du få ett Återställningpunktsmål cirka 25 15 minuter.

Med den här installationen, ordningen på säkerhetskopieringar av transaktionsloggen ögonblicksbilder för lagring och replikering av transaktionen HANA logga säkerhetskopierade data på volymen och hana- och /hana/shared (inklusive/usr/sap) kan se ut de data som visas i bilden:

 ![Förhållandet mellan en ögonblicksbild av loggen transaktion och en snapin-spegling på en tidsaxeln](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

För att uppnå en ännu bättre Återställningspunktmål disaster recovery om Kopiera du säkerhetskopieringarna av transaktionsloggen HANA från SAP HANA i Azure (stora instanser) till andra Azure-regionen. Utför följande steg för att uppnå detta ytterligare Återställningspunktmål minskning:

1. Logga in så ofta som möjligt /hana/logbackups säkerhetskopiera HANA transaktionen.
2. Använd rsync för att kopiera säkerhetskopieringarna av transaktionsloggen till NFS resursen finns Azure virtuella datorer. De virtuella datorerna är i virtuella Azure-nätverk i Azure produktionsregion och i DR-regioner. Du måste ansluta båda virtuella Azure-nätverk till kretsen ansluter produktion HANA stora instanser till Azure. Se bilder i den [nätverk överväganden för katastrofåterställning med HANA stora instanser](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) avsnitt. 
3. Behåll säkerhetskopieringarna av transaktionsloggen i region i den virtuella datorn är ansluten till NFS exporteras lagring.
4. Komplettera de säkerhetskopieringar av transaktionsloggen du hitta på /hana/logbackups volymen med mer nyligen tagits säkerhetskopieringar av transaktionsloggen på NFS dela på disaster recovery-webbplatsen i fall katastrofåterställning växling vid fel. 
5. Starta en säkerhetskopia av transaktionsloggen att återställa till den senaste säkerhetskopian som kan sparas över till DR-region.

När HANA stora instans operations bekräfta replikering relationen installationen och körningen lagring ögonblicksbild säkerhetskopieringar startas, börjar datareplikeringen.

![Steget av DR-installationen innan du upprättar replikering](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Eftersom replikering pågår kan ögonblicksbilder på PRD volymer i DR Azure-regioner inte återställs. Endast lagras. Om volymerna är monterade i ett sådant tillstånd representerar de tillstånd som demontera volymerna när PRD SAP HANA-instansen har installerats i server-enhet i DR Azure-region. De kan också vara lagring säkerhetskopieringar som inte har återställts.

Vid redundans kan du välja att återställa till en äldre lagring ögonblicksbild i stället för den senaste ögonblicksbilden för lagring.

## <a name="disaster-recovery-failover-procedure"></a>Redundans för katastrofåterställning
Det finns två fall att tänka på när du redundansväxlar till DR-plats:

- Du måste SAP HANA-databas om du vill gå tillbaka till den senaste statusen för data. Det finns i det här fallet självbetjäning skript som du kan utföra redundans utan att behöva kontakta Microsoft. Men för återställning efter fel måste du arbeta med Microsoft.
- Vill du återställa till en ögonblicksbild av lagring som inte är senaste replikerade ögonblicksbild. I det här fallet behöver arbeta med Microsoft. 

>[!NOTE]
>Följande steg måste utföras på enheten HANA stora instansen, som representerar DR-enhet. 
 
Om du vill återställa till de senaste replikerad lagring ögonblicksbilderna, utför du följande steg: 

1. Stäng av den icke-produktion instansen av HANA på disaster recovery-enheten för stora HANA-instanser som du kör. Det beror på att det finns en vilande HANA produktion instans förinstallerat.
2. Se till att inga SAP HANA-processer körs. Använd följande kommando för den här kontrollen: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Utdata ska visa du den **hdbdaemon** processer i ett stoppat tillstånd och inga andra HANA processer körs eller startats.
3. Kör skriptet på DR plats HANA stora instans enheten *azure_hana_dr_failover.pl*. Skriptet frågar efter en SAP HANA SID som ska återställas. När det behövs, anger i en eller den enda SAP HANA SID som har replikerats och underhålls i den *HANABackupCustomerDetails.txt* fil på HANA stora instans-enhet i DR-plats. 

      Om du vill ha flera SAP HANA-instanser som växlar över måste du köra skriptet flera gånger. När det behövs, Skriv i SAP HANA SID du vill ha redundans och återställning. Skriptet visar en lista över monteringspunkter på de volymer som har lagts till i enheten HANA stora instans är klar kan. Den här listan innehåller de återställda DR-volymerna samt.

4. Montera återställda disaster recovery volymer med Linux operativsystemkommandon till enheten HANA stora instans i disaster recovery-platsen. 
6. Starta inaktiva produktion-instans för SAP HANA.
7. Om du väljer att kopiera loggen säkerhetskopiering transaktionsloggar för att minska tiden för Återställningspunktmål måste du slå samman dessa säkerhetskopieringar av transaktionsloggen till nyligen monterade DR/hana/logbackups katalogen. Skriv inte över befintliga säkerhetskopior. Kopiera nyare säkerhetskopieringar som inte har replikerats med den senaste replikeringen för en ögonblicksbild för lagring.
8. Du kan även återställa enskilda filer utanför ögonblicksbilder som har replikerats till volymen som /hana/shared/PRD i DR Azure-region. 

Du kan testa redundans för Katastrofåterställning samt utan att påverka faktiska replikeringens relation. Följ föregående steg 1 och 2 för att genomföra ett redundanstest, och fortsätt sedan med följande steg 3.

>[!IMPORTANT]
>Gör *inte* köra alla transaktioner i produktions på den instans som du skapade i DR-plats för **testa redundans** med skriptet som introducerades i steg 3. Detta kommando skapar en uppsättning volymer som har ingen relation till den primära platsen. Därför synkroniseringen tillbaka till den primära platsen är *inte* möjligt. 

Steg 3 för växling vid fel:

Kör skriptet på DR plats HANA stora instans enheten **azure_hana_test_dr_failover.pl**. Det här skriptet är *inte* stoppa replikeringsrelationen mellan den primära platsen och DR-plats. I stället är det här skriptet kloning lagringsvolymer DR. När kloningsprocessen lyckas, återställs till tillståndet för den senaste ögonblicksbilden klonade volymerna och sedan montera till DR-enhet. Skriptet frågar efter en SAP HANA SID som ska återställas. Typen i en eller den enda SAP HANA SID som har replikerats och underhålls i den *HANABackupCustomerDetails.txt* fil på HANA stora instans-enhet i DR-plats. 

Om du vill ha flera SAP HANA-instanser för att testa måste du köra skriptet flera gånger. När det behövs, Skriv i SAP HANA SID för den instans som du vill testa redundans. Skriptet visar en lista över monteringspunkter på de volymer som har lagts till i HANA stora instans-enhet när åtgärden har slutförts. Den här listan innehåller de klonade DR-volymerna samt.

Fortsätt till steg 4.

   >[!NOTE]
   >Om du behöver växlas över till DR-plats att rädda vissa data som har tagits bort timmar sedan och måste anges till en tidigare ögonblicksbild DR volymerna procedur denna. 

4. Stäng av den icke-produktion instansen av HANA på disaster recovery-enheten för stora HANA-instanser som du kör. Det beror på att det finns en vilande HANA produktion instans förinstallerat.
5. Se till att inga SAP HANA-processer körs. Använd följande kommando för den här kontrollen: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Utdata ska visa du den **hdbdaemon** processer i ett stoppat tillstånd och inga andra HANA processer körs eller startats.
6. Avgör som namnet på ögonblicksbilder eller säkerhetskopiering SAP HANA-ID som du vill återställa disaster recovery platsen. Den här ögonblicksbilden är vanligtvis senaste ögonblicksbilden i verkliga disaster recovery fall. Om du behöver återställa data väljer du en tidigare ögonblicksbild.
7. Kontakta Azure Support via en supportförfrågan med hög prioritet. Fråga efter återställning av den ögonblicksbilden (med namn och datum för ögonblicksbilden) eller HANA säkerhetskopiering ID för DR-plats. Standardvärdet är att operations sida återställer/hana/datavolym endast. Om du vill ha/hana/logbackups volymerna samt måste du särskilt tillstånd som. *Återställ inte /hana/shared volymen.* I stället bör du välja specifika filer som global.ini av den **.snapshot** katalogen och dess underkataloger när du återansluta/hana/delad klustervolym för PRD. 

   På sidan åtgärder utförs följande steg:

   a. Replikering av ögonblicksbilder från produktionsvolymen till disaster recovery volymer har stoppats. Den här avbrott har redan hänt om ett avbrott på produktionsplatsen är därför du måste utföra katastrofåterställning.
   
   b. Lagringen ögonblicksbild namn eller en ögonblicksbild med säkerhetskopiering ID som du har valt har återställts på disaster recovery-volymer.
   
   c. Efter återställningen, disaster recovery-volymer som är tillgängliga för monteras HANA stora instans enheterna i disaster recovery region.
      
8. Montera disaster recovery-volymer till HANA stora instans-enhet i disaster recovery-plats. 
9. Starta inaktiva produktion-instans för SAP HANA.
10. Om du väljer att kopiera loggen säkerhetskopiering transaktionsloggar för att minska tiden för Återställningspunktmål måste du slå samman dessa säkerhetskopieringar av transaktionsloggen till nyligen monterade DR/hana/logbackups katalogen. Skriv inte över befintliga säkerhetskopior. Kopiera nyare säkerhetskopieringar som inte har replikerats med den senaste replikeringen för en ögonblicksbild för lagring.
11. Du kan även återställa enskilda filer utanför ögonblicksbilder som har replikerats till volymen som /hana/shared/PRD i DR Azure-region.

Nästa ordningsföljden omfattar återställer SAP HANA produktion instansen baserat på den återställda lagring ögonblicksbilden och säkerhetskopior av de transaktion som är tillgängliga:

1. Ändra platsen för säkerhetskopian till **/hana/logbackups** med hjälp av SAP HANA Studio.
   ![Ändra platsen för säkerhetskopian för DR-återställning](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA skannar hela säkerhetskopian platser och ger förslag på den senaste säkerhetskopia transaktionslogg för att återställa till. Sökningen kan ta några minuter tills en skärm som visas i följande: ![lista över säkerhetskopieringar av transaktionsloggen för DR-återställning](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. Justera några standardinställningar:

      - Rensa **använda Delta säkerhetskopior**.
      - Välj **initiera loggen området**.

   ![Ange det initiera logg området](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. Välj **Slutför**.

   ![Slut DR-återställning](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Ett fönster för pågår, som den här, visas. Tänk på att exemplet är av en disaster recovery-återställning av en tre noder skalbar SAP HANA-konfiguration.

![Återställning](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Om återställningen verkar låser sig i den **Slutför** skärmen och matchar inte visa förlopp skärmen, kontrollera att alla SAP HANA-instanser på arbetsnoderna körs. Starta SAP HANA-instanser manuellt vid behov.


### <a name="failback-from-a-dr-to-a-production-site"></a>Återställning från en Blobb till en plats för produktion
Du kan växla tillbaka från en Blobb till en plats för produktion. Nu ska vi titta på ett scenario för växling vid fel till disaster recovery plats orsakades av problem i produktionen Azure-region och inte av behovet av att återställa förlorade data. Du har körts SAP produktion arbetsbelastningen på ett tag på disaster recovery-webbplatsen. När problemen vid produktionsplatsen löses som du vill växla tillbaka till din produktionsplatsen. Eftersom du kan förlora data omfattar steg tillbaka till produktionsplatsen flera steg och nära samarbete med SAP HANA på åtgärder i Azure-teamet. Det är upp till dig att utlösa operations-teamet om du vill starta synkroniseringen tillbaka till produktionsplatsen när problemen är lösta.

Det här är en sekvens med åtgärder:

1. SAP HANA på åtgärder i Azure-teamet hämtar utlösaren att synkronisera lagringsvolymer produktion från disaster recovery lagringsvolymer, som nu representerar tillståndet för produktion. I det här tillståndet stängs HANA stora instans-enhet i produktionsplatsen.
2. SAP HANA på åtgärder i Azure-teamet övervakar replikering och ser till att den är uppdateringar innan du informerar dig.
3. Du kan stänga av de program som använder produktion HANA instans i disaster recovery-platsen. Sedan gör du en säkerhetskopia av HANA transaktionslogg. Därefter kan du stoppa HANA-instans som körs på enheterna HANA stora instans i disaster recovery-platsen.
4. När HANA-instans som körs i HANA stora instans-enhet i disaster recovery-platsen är avstängd, synkroniserar driftteamet manuellt diskvolymer igen.
5. SAP HANA på åtgärder i Azure-teamet startar enheten HANA stora instans i produktionsplatsen igen och lämnar det till dig. Du se till att SAP HANA-instansen har en avstängning vid starten av stora HANA-instans-enhet.
6. Du kan utföra samma steg för återställning av databasen som du gjorde när tidigare växling till återställningsplatsen för katastrofåterställning.

### <a name="monitor-disaster-recovery-replication"></a>Övervaka disaster recovery replikering

Du kan övervaka statusen för din lagring replikering pågår genom att köra skriptet `azure_hana_replication_status.pl`. Det här skriptet måste köras från en enhet som körs i disaster recovery-platsen ska fungera som förväntat. Skriptet fungerar oavsett om replikering är aktiv. Skriptet kan köras för varje enhet HANA stora instans för din klient på disaster recovery-plats. Den kan inte användas för att få information om startvolymen.

Anropa skriptet med det här kommandot:
```
./replication_status.pl <HANA SID>
```

Utdata är fördelade på, volymen i följande avsnitt:  

- Länkstatus
- Replikering för närvarande är aktiv
- Senaste ögonblicksbilden replikeras 
- Storleken på den senaste ögonblicksbilden
- Aktuella fördröjningen mellan ögonblicksbilder (mellan den senaste replikeringen som slutförda ögonblicksbild och nu)

Länkens status visas som **Active** om länken mellan platser är nere eller om det finns en pågående redundansväxling. Aktiviteten replikering adresser om data är för närvarande håller på att replikeras och är inaktiv, eller om andra aktiviteter som för närvarande pågår till länken. Den senaste ögonblicksbilden replikeras endast ska visas som `snapmirror…`. Storleken på den senaste ögonblicksbilden visas sedan. Slutligen visas fördröjning. Fördröjning representerar tiden från schemalagda replikering till när replikeringen är klar. En fördröjning kan vara större än en timme för replikering, särskilt i den inledande replikeringen, även om replikering har startats. Fördröjning fortsätter att öka tills den pågående replikering har slutförts.

Följande är ett exempel på utdata:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












