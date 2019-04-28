---
title: SAP LaMa connector för Azure | Microsoft Docs
description: Hantera SAP-system på Azure med SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/17/2018
ms.author: sedusch
ms.openlocfilehash: f09f66e81ec4878aedebfee9be4c0c67b75c8ad6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463029"
---
# <a name="sap-lama-connector-for-azure"></a>SAP LaMa-anslutning för Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Allmänna Supportmeddelande: Alltid öppna en incident med SAP på komponenten BC-VCM-LVM-Hyper-v om du behöver stöd för SAP LaMa eller Azure-anslutningen.

SAP LaMa används av många kunder att driva och övervaka sina SAP-landskap. Sedan SAP LaMa 3.0 SP05 levereras det med en anslutning till Azure som standard. Du kan använda den här anslutningen för att frigöra och starta virtuella datorer, kopiera och flytta hanterade diskar och ta bort hanterade diskar. Med dessa grundläggande åtgärder, kan du flytta, kopiera, klona och uppdatera SAP-system med hjälp av SAP LaMa.

Den här guiden beskriver hur du ställer in Azure connector för SAP-LaMa, skapa virtuella datorer som kan användas för att installera anpassningsbar SAP-system och hur de konfigureras.

> [!NOTE]
> Anslutningen är endast tillgänglig i SAP LaMa Enterprise Edition

## <a name="resources"></a>Resurser

Följande SAP-information är relaterade till ämnet för SAP LaMa på Azure:

| Nummer | Rubrik |
| --- | --- |
| [2343511] |Microsoft Azure connector för SAP-landskap Management (LaMa) |
| [2350235] |SAP-landskap Management 3.0 – Enterprise edition |

Läs även den [SAP hjälpa Portal för SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Allmänna anmärkningar

* Se till att aktivera *automatisk generering av monteringspunkt* i Inställningar -> Inställningar -> motor  
  Om SAP LaMa montering av volymer med SAP anpassningsbar tillägg på en virtuell dator, måste det finnas monteringspunkten om den här inställningen inte är aktiverad.

* Använd separata undernät och använda dynamisk IP-adresser att förhindra IP-inte ”stjäla” när du distribuerar nya virtuella datorer och SAP-instanser är oförberedd  
  Om du använder dynamisk IP-adressallokering i undernätet, som också används av SAP LaMa, misslyckas förbereder ett SAP-system med SAP LaMa. Om ett SAP-system är förberedd är inte reserverade IP-adresser och kan hämta tilldelad till andra virtuella datorer.

* Om du loggar in på hanterade värdar, se till att inte blockera filsystem från att demontera  
  Om du loggar in på en Linux-datorer och ändra arbetskatalogen till en katalog i en monteringspunkt, till exempel /usr/sap/AH1/ASCS00/exe, volymen går inte att demontera och flytta eller unprepare misslyckas.

## <a name="set-up-azure-connector-for-sap-lama"></a>Konfigurera Azure-anslutningsapp för SAP LaMa

Azure connector levereras från och med SAP LaMa 3.0 SP05. Vi rekommenderar att du alltid installera senaste supportpaket och korrigering för SAP LaMa 3.0. Azure anslutningen används ett huvudnamn för tjänsten för att auktorisera mot Microsoft Azure. Följ dessa steg om du vill skapa ett huvudnamn för tjänsten för hantering av SAP-landskap (LaMa).

1. Gå till https://portal.azure.com
1. Öppna bladet Azure Active Directory
1. Klicka på App-registreringar
1. Klicka på Lägg till
1. Ange ett namn, väljer typen ”Web app/API”, ange en inloggnings-URL (till exempel http:\//localhost) och klicka på Skapa
1. Inloggnings-URL: en används inte och kan vara vilken giltig URL
1. Välj den nya appen och klicka på nycklar på fliken Inställningar
1. Ange en beskrivning för en ny nyckel, Välj ”upphör aldrig att gälla” och klickar på Spara
1. Anteckna värdet. Den används som lösenord för tjänstens huvudnamn
1. Anteckna programmets ID. Den används som användarnamn för tjänstens huvudnamn

Tjänstens huvudnamn har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge tjänstens huvudnamn behörighet att komma åt dem.

1. Gå till https://portal.azure.com
1. Öppna resursbladet för grupper
1. Välj den resursgrupp som du vill använda
1. Klicka på åtkomstkontroll (IAM)
1. Klicka på Lägg till rolltilldelning
1. Välj rollen deltagare
1. Ange namnet på programmet som du skapade ovan
1. Klicka på Spara
1. Upprepa steg 3 till 8 för alla resursgrupper som du vill använda i SAP LaMa

Öppna webbplatsen SAP LaMa och gå till infrastrukturen. Gå till fliken molnet chefer och klicka på Lägg till. Välj Microsoft Azure Cloud Adapter och klicka på Nästa. Ange följande information:

* Etikett: Välj ett namn för anslutningsinstansen
* Användarnamn: Program-ID för tjänstens huvudkonto
* Lösenord: Tjänstens huvudnamn nyckel/lösenord
* URL: Behåll standardvärdet https://management.azure.com/
* Övervakning av intervall (sekunder): Bör vara minst 300
* Prenumerations-ID: Azure prenumerations-ID
* Azure Active Directory klient-ID: ID för Active Directory-klient
* Proxyvärd: Värdnamnet för proxyservern om SAP LaMa behöver en proxyserver för att ansluta till internet
* Proxyport: TCP-porten för proxyservern

Klicka på Test-konfiguration för att verifiera dina indata. Du bör se

Anslutningen lyckades: Anslutning till Microsoft-molnet har upprättats. 7 resursgrupper hittades (endast 10 grupper begärt)

längst ned på webbplatsen.

## <a name="provision-a-new-adaptive-sap-system"></a>Etablera ett nytt anpassningsbar SAP-system

Du kan manuellt distribuera en ny virtuell dator eller Använd en av Azure-mallar i den [snabbstartsmallarna på lagringsplatsen](https://github.com/Azure/azure-quickstart-templates). Den innehåller mallar för [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [SAP NetWeaver-programservrar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps), och [databasen](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Du kan också använda dessa mallar för att etablera nya värdarna som en del av en system kopia/klon osv.

Vi rekommenderar att du använder ett separat undernät för alla virtuella datorer som du vill hantera med SAP LaMa och Använd inte dynamiska IP-adresser för att förhindra IP-adress ”stjäla” när du distribuerar nya virtuella datorer och SAP-instanser är oförberedd.

> [!NOTE]
> Om möjligt ta bort alla tillägg för virtuell dator eftersom de kan orsaka långa körningar för att frånkoppla diskar från en virtuell dator.

Kontrollera att användaren \<hanasid > adm, \<sapsid > adm och gruppen sapsys finnas på måldatorn med samma ID och gid eller Använd LDAP. Aktivera och starta NFS-servern på de virtuella datorerna som ska användas för att köra SAP NetWeaver (A) SCS.

### <a name="manual-deployment"></a>Manuell distribution

SAP LaMa kommunicerar med den virtuella datorn med Värdagenten SAP. Om du distribuerar de virtuella datorerna manuellt eller inte med Azure Resource Manager-mallen från Snabbstart-databasen, se till att installera den senaste agenten för SAP-värden och anpassningsbar SAP-tilläggen. Mer information om nödvändiga korrigeringsnivåer för Azure finns i SAP-kommentar [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Manuell distribution av en Linux-dator

Skapa en ny virtuell dator med en av de stöd system som anges i SAP-kommentar [2343511]. Lägga till ytterligare IP-konfigurationer för SAP-instanser. Varje instans måste ha minst på IP-adress och måste installeras via ett virtuellt värdnamn.

SAP NetWeaver ASCS-instans behöver diskar för /sapmnt/\<SAPSID >, / usr/sap/\<SAPSID >, /usr/sap/trans ochusr/sap/\<sapsid > adm. SAP NetWeaver-programservrar behöver inte ytterligare diskar. Allt som rör SAP-instansen måste lagras på ASCS och exporteras via NFS. I annat fall går det för närvarande inte att lägga till ytterligare servrar med hjälp av SAP LaMa.

![ASCS för SAP NetWeaver på Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Manuell distribution för SAP HANA

Skapa en ny virtuell dator med en av system som stöd för SAP HANA som anges i SAP-kommentar [2343511]. Lägg till en ytterligare IP-konfiguration för SAP HANA och en per HANA-klient.

SAP HANA behöver diskar för /hana/shared, /hana/backup, /hana/data och /hana/log

![SAP HANA på Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Manuell distribution för Oracle-databas på Linux

Skapa en ny virtuell dator med ett operativsystem som stöds för Oracle-databaser som anges i SAP-kommentar [2343511]. Lägg till en ytterligare IP-konfiguration för Oracle-databasen.

Oracle-databasen behöver diskar för /oracle och /home/oraod1 /home/oracle

![Oracle-databas på Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Manuell distribution för Microsoft SQL Server

Skapa en ny virtuell dator med en av system som stöd för Microsoft SQL Server som anges i SAP-kommentar [2343511]. Lägg till en ytterligare IP-konfiguration för SQL Server-instansen.

SQL Server-databasserver måste diskar för databasdata och loggfilerna och diskar för c:\usr\sap.

![Oracle-databas på Linux](media/lama/sap-lama-db-sql.png)

Se till att installera en stöds Microsoft ODBC-drivrutin för SQL Server på en virtuell dator som du vill använda för att flytta en programserver för SAP NetWeaver till eller som ett mål för kopiera/kloning av system.

SAP LaMa kan inte flytta SQL-servern så att en virtuell dator som du vill använda för att flytta en databasinstans till eller som ett system kopia/klona mål måste SQL Server förinstallerat.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Distribuera virtuell dator med en Azure-mall

Hämta följande senaste tillgängliga arkiverar från den [SAP Software Marketplace](https://support.sap.com/swdc) för operativsystemet på de virtuella datorerna:

1. SAPCAR 7.21
1. SAP-VÄRDAGENTEN 7.21
1. SAP ANPASSNINGSBAR TILLÄGG 1.0 EXT

Hämta även följande komponenter från den [Microsoft Download Center](https://www.microsoft.com/download)

1. Microsoft Visual C++ 2010 Redistributable Package (x64) (endast Windows)
1. Microsoft ODBC-drivrutin för SQLServer (endast SQLServer)

Komponenterna som krävs för att distribuera mallen. Det enklaste sättet att göra dem tillgängliga för mallen är att överföra dem till ett Azure storage-konto och skapa en signatur för delad åtkomst (SAS).

Mallarna har följande parametrar:

* sapSystemId: SAP system-ID. Används för att skapa disklayouten (till exempelusr/sap/\<sapsid >).

* Datornamn: Namnet på den nya virtuella datorn. Den här parametern används också av SAP LaMa. När du använder den här mallen för att etablera en ny virtuell dator som en del av en system-kopia, väntar SAP LaMa tills värden med detta datornamn kan nås.

* osType: Typ av operativsystem som du vill distribuera.

* DbType: Typ av databasen. Den här parametern används för att avgöra hur många ytterligare IP-konfigurationer måste läggas till och hur disklayouten bör se ut.

* sapSystemSize: Storleken på SAP-System som du vill distribuera. Den används för att fastställa den virtuella datorn Instanstypen och storleken.

* adminUsername: Användarnamnet för den virtuella datorn.

* adminPassword: Lösenordet för den virtuella datorn. Du kan också ange en offentlig nyckel för SSH.

* sshKeyData: Offentlig SSH-nyckel för virtuella datorer. Stöds endast för Linux-operativsystem.

* subnetId: ID för det undernät som du vill använda.

* deployEmptyTarget: Du kan distribuera ett tomt mål om du vill använda den virtuella datorn som mål för en instans flytta eller liknande. I det här fallet kopplas inga ytterligare diskar eller IP-konfigurationer.

* sapcarLocation: Plats för sapcar programmet som matchar det operativsystem som du distribuerar. sapcar används för att extrahera arkivet som du anger i andra parametrar.

* sapHostAgentArchiveLocation: Platsen för Värdagenten för SAP-arkivet. SAP Värdagenten distribueras som en del av den här för malldistributionen.

* sapacExtLocation: Platsen för de anpassningsbara SAP-tillägg. SAP-kommentar [2343511] visar den lägsta korrigeringsnivån som krävs för Azure.

* vcRedistLocation: Platsen för den VC Runtime som krävs för att installera tillägget för SAP-anpassningsbar. Den här parametern är endast krävs för Windows.

* odbcDriverLocation: Platsen för ODBC-drivrutin som du vill installera. Endast Microsoft ODBC-drivrutin för SQL Server stöds.

* sapadmPassword: Lösenordet för användaren sapadm.

* sapadmId: Det användar-ID för Linux för sapadm användaren. Krävs inte för Windows.

* sapsysGid: Linux grupp-ID i gruppen sapsys. Krävs inte för Windows.

* _artifactsLocation: Bas-URI, där artefakter som krävs av den här mallen finns. När mallen distribueras med hjälp av de medföljande skript, används en privat plats i prenumerationen och det här värdet ska skapas automatiskt. Behövs bara om du inte distribuerar mallen från GitHub.

* _artifactsLocationSasToken: SasToken som krävs för att komma åt _artifactsLocation. När mallen distribueras med hjälp av de medföljande skript, ska en sasToken skapas automatiskt. Behövs bara om du inte distribuerar mallen från GitHub.

### <a name="sap-hana"></a>SAP HANA

I exemplen nedan förutsätter vi att du installerar SAP HANA med system-ID: T HN1 och SAP NetWeaver-system med system-ID AH1. Virtuella värdnamn är hn1-db för HANA-instansen ah1-db för HANA-klienten som används av SAP NetWeaver-systemet, ah1-ascs för SAP NetWeaver ASCS och ah1 di 0 för den första SAP NetWeaver-programservern.

#### <a name="install-sap-netweaver-ascs-for-sap-hana"></a>Installera SAP NetWeaver ASCS för SAP HANA

Innan du börjar SAP Software etablering Manager (SWPM) måste behöva du montera virtuella värdnamnet för ASCS IP-adress. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext kan du se till att montera om IP-adressen efter en omstart.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Kör SWPM och Använd *ah1 ascs* för den *ASCS instans värdnamn*.

![Linux][Logo_Linux] Linux  
Lägg till följande profil-parameter till SAP Värdagenten profilen, som finns i /usr/sap/hostctrl/exe/host_profile. Mer information finns i SAP-kommentar [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-hana"></a>Installera SAP HANA

Om du installerar SAP HANA med kommandorad verktyget hdblcm, kan du använda parametern--värdnamn för att tillhandahålla ett virtuellt värdnamn. Du måste lägga till IP-adressen för det virtuella värdnamnet för databasen till ett nätverksgränssnitt. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext kan du se till att montera om IP-adressen efter en omstart.

Lägg till en annan virtuell värdnamn och IP-adress för det namn som används av programmet för att ansluta till HANA-klient.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Kör instans databasinstallation av SWPM på den virtuella datorn på programmet, inte på den virtuella datorn för HANA. Använd *ah1 db* för den *databasen värden* i dialogrutan *databas för SAP-System*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Installera SAP NetWeaver-programserver för SAP HANA

Innan du börjar SAP Software etablering Manager (SWPM) måste behöva du montera IP-adressen för virtuella värdnamn på programservern. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext kan du se till att montera om IP-adressen efter en omstart.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Det rekommenderas att använda SAP NetWeaver profil parametern dbs/hdb/hdb_use_ident för att ange identiteten som används för att hitta nyckeln i HDB userstore. Du kan lägga till den här parametern manuellt efter installationen för databas-instans med SWPM eller köra SWPM med

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Om du ställer in det manuellt, måste du också skapa nya HDB userstore poster.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Använd *ah1-di-0* för den *PROVIDERADRESSER instans värdnamn* i dialogrutan *primära Application Server-instansen*.

#### <a name="post-installation-steps-for-sap-hana"></a>Anvisningarna efter Installation av för SAP HANA

Se till att säkerhetskopiera SYSTEMDB och alla klientdatabaser innan du försöker göra en kopia av klientorganisation, klient flytta eller skapa en systemreplikering.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

I exemplen nedan förutsätter vi att du installerar SAP NetWeaver-system med system-ID AS1. Virtuella värdnamn är as1-db för SQL Server-instansen som används av SAP NetWeaver-systemet, as1-ascs för SAP NetWeaver ASCS och as1 di 0 för den första SAP NetWeaver-programservern.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Installera SAP NetWeaver ASCS för SQLServer

Innan du börjar SAP Software etablering Manager (SWPM) måste behöva du montera virtuella värdnamnet för ASCS IP-adress. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext kan du se till att montera om IP-adressen efter en omstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Kör SWPM och Använd *as1 ascs* för den *ASCS instans värdnamn*.

#### <a name="install-sql-server"></a>Installera SQLServer

Du måste lägga till IP-adressen för det virtuella värdnamnet för databasen till ett nätverksgränssnitt. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext kan du se till att montera om IP-adressen efter en omstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Kör instans databasinstallation av SWPM på SQL server-dator. Använd SAPINST_USE_HOSTNAME =*as1 db* att åsidosätta det värdnamn som används för att ansluta till SQL Server. Om du har distribuerat den virtuella datorn med hjälp av Azure Resource Manager-mall, se till att ange katalogen som används för data databasfiler till *C:\sql\data* och databasloggfilen till *C:\sql\log*.

Se till att användaren *NT AUTHORITY\SYSTEM* har åtkomst till SQL Server och har rollen *sysadmin*. Mer information finns i SAP-kommentar [1877727] och [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Installera SAP NetWeaver-programserver

Innan du börjar SAP Software etablering Manager (SWPM) måste behöva du montera IP-adressen för virtuella värdnamn på programservern. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext kan du se till att montera om IP-adressen efter en omstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Använd *as1-di-0* för den *PROVIDERADRESSER instans värdnamn* i dialogrutan *primära Application Server-instansen*.

## <a name="troubleshooting"></a>Felsökning

### <a name="errors-and-warnings-during-discover"></a>Fel och varningar under identifiera

* SELECT-behörighet nekas
  * [Microsoft] [ODBC SQL Server-drivrutinen] [SQLServer] Välj behörighet nekas för objekt log_shipping_primary_databases, systemdatabasen msdb, schemat dbo. [SOAPFaultException]  
  Välj behörighet nekas för objekt log_shipping_primary_databases, systemdatabasen msdb, schemat dbo.
  * Lösning  
    Se till att *NT AUTHORITY\SYSTEM* har åtkomst till SQL-servern. Se SAP-kommentar [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Fel och varningar för instans verifiering

* Ett undantag genererades i valideringen av HDB userstore  
  * finns i Loggvisaren  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: Undantag i verifieraren med ID 'RuntimeHDBConnectionValidator' (verifiering: 'VALIDATION_HDB_USERSTORE'): Det gick inte att hämta hdbuserstore  
    HANA userstore är inte på rätt plats
  * Lösning  
    Kontrollera att den /usr/sap/AH1/hdbclient/install/installation.ini stämmer

### <a name="errors-and-warnings-during-a-system-copy"></a>Fel och varningar under en System-kopia

* Ett fel uppstod vid validering av systemet etablering steg
  * På grund av: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException anropa ”/ usr/sap/hostctrl/exe/sapacext - a ShowHanaBackups -m HN1 -f 50 - h hn1-db - o nivå = 0\;status = 5\;port = 35013 pf = / usr/sap/hostctrl / exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook - r' | /usr/SAP/hostctrl/exe/sapacext – en ShowHanaBackups -m HN1 -f 50 - h hn1-db - o nivå = 0\;status = 5\;port = 35013 pf = / usr/sap/hostctrl/exe/host_profile - R -T dev_lvminfo -u SYSTEM -p hook - r
  * Lösning  
    Säkerhetskopiera alla databaser i källan HANA-system

* System Kopiera steg *starta* på databasinstans
  * Värd-Agentåtgärden '000D3A282BC91EE8A1D76CF1F92E2944 ”misslyckades (OperationException. FaultCode: '127', meddelande: ' Kommandokörning misslyckades. : [Microsoft] [ODBC-drivrutin SQL Server] [SQL Server] användaren har inte behörighet att ändra databasen ”AS2-databasen finns inte eller databasen inte är i ett tillstånd som tillåter åtkomstkontroller”.)
  * Lösning  
    Se till att *NT AUTHORITY\SYSTEM* har åtkomst till SQL-servern. Se SAP-kommentar [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Fel och varningar under en System-klon

* Fel uppstod vid försök att registrera agenten instans i steg *Tvingad registrera och starta instans Agent* för application server eller ASCS
  * Fel uppstod vid försök att registrera instans-agenten. (RemoteException: ”Det gick inte att läsa in instansdata från profilen”\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0 ”:  Det går inte att få åtkomst till profilen ”\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0”: Det finns ingen sådan fil eller katalog ”.)
  * Lösning  
   Se till att resursen sapmnt på ASCS/SCS har fullständig åtkomst för SAP_AS1_GlobalAdmin

* Fel i steget *Start Aktivera skydd för kloning*
  * Det gick inte att öppna filen ”\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0” orsak: Det finns ingen sådan fil eller katalog
  * Lösning  
    Programservern datorkonto måste ha skrivbehörighet för profilen

### <a name="errors-and-warnings-during-create-system-replication"></a>Fel och varningar under Skapa Systemreplikering

* Undantag när du klickar på Skapa System Replication
  * På grund av: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException anropa ”/ usr/sap/hostctrl/exe/sapacext - a ShowHanaBackups -m HN1 -f 50 - h hn1-db - o nivå = 0\;status = 5\;port = 35013 pf = / usr/sap/hostctrl / exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook - r' | /usr/SAP/hostctrl/exe/sapacext – en ShowHanaBackups -m HN1 -f 50 - h hn1-db - o nivå = 0\;status = 5\;port = 35013 pf = / usr/sap/hostctrl/exe/host_profile - R -T dev_lvminfo -u SYSTEM -p hook - r
  * Lösning  
    Testa om sapacext kan utföras eftersom `<hanasid`> adm

* Ett fel inträffade när fullständig kopia inte är aktiverat i Storage steg
  * Ett fel uppstod när ett meddelande för attributet av kontext för sökvägen IStorageCopyData.storageVolumeCopyList:1 och fältet targetStorageSystemId
  * Lösning  
    Ignorera varningar i steg och försök igen. Det här problemet korrigeras i en ny support paket/patch LaMa av SAP.

### <a name="errors-and-warnings-during-relocate"></a>Fel och varningar under flytta

* Sökvägen ”/ usr/sap/AH1” är inte tillåtet för nfs reexports.
  * Kontrollera SAP-kommentar [2628497] information.
  * Lösning  
    Lägg till ASCS exporterar till ASCS HostAgent profil. Se SAP-kommentar [2628497]

* Funktionen har inte implementerats när hur du flyttar ASCS
  * Kommandoutdata: exportfs: värden: / usr/sap/AX1: Funktionen har inte implementerats
  * Lösning  
    Kontrollera att servertjänsten för NFS är aktiverat på den virtuella måldatorn flytta

### <a name="errors-and-warnings-during-application-server-installation"></a>Fel och varningar under installationen av programmet Server

* Fel vid körning av SAPinst steg: getProfileDir
  * FEL: (Senaste fel som rapporterats av steget: Fångats ESAPinstException i modulen anrop: Verifieraren steg ' | NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | ind | getSid | 0 | NW_readProfileDir | ind | ind | ind | ind | readProfile | 0 | getProfileDir ”rapporterade ett fel: Noden \\\as1-ascs\sapmnt\AS1\SYS\profile finns inte. Starta SAPinst i interaktivt läge för att lösa detta problem)
  * Lösning  
    Se till att SWPM körs med en användare som har åtkomst till profilen. Den här användaren kan konfigureras i guiden Application Server-Installation

* Fel vid körning av SAPinst steg: askUnicode
  * FEL: (Senaste fel som rapporterats av steget: Fångats ESAPinstException i modulen anrop: Verifieraren steg ' | NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | ind | getSid | 0 | NW_getUnicode | ind | ind | ind | ind | unicode | 0 | askUnicode ”rapporterade ett fel: Starta SAPinst i interaktivt läge för att lösa detta problem)
  * Lösning  
    Om du använder en nyare kernel SAP avgöra SWPM inte om systemet är en unicode-systemet längre med ASCS-meddelandeservern. Se SAP-kommentar [2445033] för mer information.  
    Det här problemet korrigeras i en ny support paket/patch LaMa av SAP.  
    Ange profilparametern OS_UNICODE = uc i standardprofilen för SAP-system för att lösa problemet.

* Fel vid körning av SAPinst steg: dCheckGivenServer
  * Fel vid körning av SAPinst steg: dCheckGivenServer ”version =” 1.0 ”fel: (Senaste fel som rapporterats av steget: \<p > installationen avbröts av användaren. \</p>
  * Lösning  
    Se till att SWPM körs med en användare som har åtkomst till profilen. Den här användaren kan konfigureras i guiden Application Server-Installation

* Fel vid körning av SAPinst steg: checkClient
  * Fel vid körning av SAPinst steg: checkClient ”version =” 1.0 ”fel: (Senaste fel som rapporterats av steget: \<p > installationen avbröts av användaren. \</p>)
  * Lösning  
    Se till att Microsoft ODBC-drivrutinen för SQL Server är installerat på den virtuella datorn där du vill installera application server

* Fel vid körning av SAPinst steg: copyScripts
  * Senaste fel som rapporterats av steget: Systemanropet misslyckades. INFORMATION: Fel 13 (0x0000000d) (behörighet nekad) i körningen av system anropa fopenU om du med parametern (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), rad (494) i filen (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib / filesystem/syxxcfstrm2.cpp), Stackspårning:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction (iastring konstanta & namn, args_t konst och argument)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream (args_t konst och _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl (konstanta CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Lösning  
    Se till att SWPM körs med en användare som har åtkomst till profilen. Den här användaren kan konfigureras i guiden Application Server-Installation

* Fel vid körning av SAPinst steg: askPasswords
  * Senaste fel som rapporterats av steget: Systemanropet misslyckades. INFORMATION: Fel 5 (0x00000005) (åtkomst nekas.) i körningen av systemanrop 'NetValidatePasswordPolicy' med parametern (...), rad (359) i filen (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), Stackspårning:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction (iastring konstanta & namn, args_t konst och argument)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy (args_t konst och _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,***) konst)
  * Lösning  
    Se till att lägga till en värdregel i steget *isolering* att tillåta kommunikation från den virtuella datorn till domänkontrollanten

## <a name="next-steps"></a>Nästa steg
* [SAP HANA på Azure handboken][hana-ops-guide]
* [Azure virtuella datorer, planering och implementering av SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
