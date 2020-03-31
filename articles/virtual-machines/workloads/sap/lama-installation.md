---
title: SAP LaMa-anslutning för Azure | Microsoft-dokument
description: Hantera SAP-system på Azure med SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: fda62ff0af29c7cf681d9438b02420d299535701
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293952"
---
# <a name="sap-lama-connector-for-azure"></a>SAP LaMa-anslutning för Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Allmän supportpolicy: Öppna alltid en incident med SAP på komponenten BC-VCM-LVM-HYPERV om du behöver stöd för SAP LaMa eller Azure-anslutningen.

SAP LaMa används av många kunder för att driva och övervaka deras SAP-landskap. Eftersom SAP LaMa 3.0 SP05 levereras den med en anslutning till Azure som standard. Du kan använda den här kopplingen för att frigöra och starta virtuella datorer, kopiera och flytta hanterade diskar och ta bort hanterade diskar. Med dessa grundläggande åtgärder kan du flytta, kopiera, klona och uppdatera SAP-system med SAP LaMa.

Den här guiden beskriver hur du konfigurerar Azure-anslutningen för SAP LaMa, skapar virtuella datorer som kan användas för att installera adaptiva SAP-system och hur du konfigurerar dem.

> [!NOTE]
> Anslutningen är endast tillgänglig i SAP LaMa Enterprise Edition

## <a name="resources"></a>Resurser

Följande SAP-anteckningar är relaterade till ämnet SAP LaMa på Azure:

| Antecknar numrerar | Titel |
| --- | --- |
| [2343511] |Microsoft Azure-anslutning för SAP Landscape Management (LaMa) |
| [2350235] |SAP Landscape Management 3.0 - Enterprise edition |

Läs även [SAP Hjälpportal för SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Allmänna anmärkningar

* Se till att aktivera *Automatisk monteringspunktsgenerering* i installationsprogrammet -> inställningar -> Motor  
  Om SAP LaMa monterar volymer med SAP Adaptive Extensions på en virtuell dator måste monteringspunkten finnas om den här inställningen inte är aktiverad.

* Använd separat undernät och använd inte dynamiska IP-adresser för att förhindra att IP-adress "stjäl" när nya virtuella datorer och SAP-instanser inte är förberedda  
  Om du använder dynamisk IP-adressallokering i undernätet, som också används av SAP LaMa, kan det hända att förbereda ett SAP-system med SAP LaMa. Om ett SAP-system inte är oförberett är IP-adresserna inte reserverade och kan allokeras till andra virtuella datorer.

* Om du loggar in på hanterade värdar ska du se till att inte blockera filsystem från att avmonteras  
  Om du loggar in på en virtuell Linux-datorer och ändrar arbetskatalogen till en katalog i en monteringspunkt, till exempel /usr/sap/AH1/ASCS00/exe, kan volymen inte avmonteras och en flytta eller unprepare misslyckas.

* Se till att inaktivera CLOUD_NETCONFIG_MANAGE på SUSE SLES Linux virtuella datorer. Mer information finns i [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Konfigurera Azure-anslutning för SAP LaMa

Azure-anslutningen levereras från och med SAP LaMa 3.0 SP05. Vi rekommenderar att du alltid installerar det senaste supportpaketet och -korrigeringsfilen för SAP LaMa 3.0.

Azure-anslutningen använder Azure Resource Manager-API:et för att hantera dina Azure-resurser. SAP LaMa kan använda en tjänsthuvudnamn eller en hanterad identitet för att autentisera mot det här API:et. Om din SAP LaMa körs på en virtuell Azure-dator rekommenderar vi att du använder en hanterad identitet enligt beskrivningen i kapitlet [Använd en hanterad identitet för att få åtkomst till Azure API](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d). Om du vill använda en tjänsthuvudnamn följer du stegen i kapitlet [Använd en tjänsthuvudnamn för att få åtkomst till Azure API](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e).

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Använda en tjänsthuvudnamn för att få åtkomst till Azure API

Azure-anslutningen kan använda en tjänsthuvudnamn för att auktorisera mot Microsoft Azure. Följ dessa steg för att skapa en tjänsthuvudnamn för SAP Landscape Management (LaMa).

1. Gå till https://portal.azure.com
1. Öppna Azure Active Directory-bladet
1. Klicka på Appregistreringar
1. Klicka på Ny registrering
1. Ange ett namn och klicka på Registrera
1. Välj den nya appen och klicka på Certifikat & hemligheter på fliken Inställningar
1. Skapa en ny klienthemlighet, ange en beskrivning för en ny nyckel, välj när hemligheten ska upphöra och klicka på Spara
1. Skriv ned värdet. Den används som lösenord för servicehuvudmannen
1. Skriv ner program-ID: t. Den används som användarnamn för servicehuvudmannen

Tjänsthuvudgivaren har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge tjänstens huvudnamn behörighet att komma åt dem.

1. Gå till https://portal.azure.com
1. Öppna bladet Resursgrupper
1. Markera den resursgrupp som du vill använda
1. Klicka på Åtkomstkontroll (IAM)
1. Klicka på Lägg till rolltilldelning
1. Välj rolldeltagare
1. Ange namnet på programmet som du skapade ovan
1. Klicka på Spara
1. Upprepa steg 3 till 8 för alla resursgrupper som du vill använda i SAP LaMa

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Använda en hanterad identitet för att få åtkomst till Azure-API:et

För att kunna använda en hanterad identitet måste din SAP LaMa-instans köras på en Azure-virtuell dator som har en system- eller användartilldelad identitet. Mer information om hanterade identiteter finns i Vad [Configure managed identities for Azure resources on a VM using the Azure portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) [är hanterade identiteter för Azure-resurser?](../../../active-directory/managed-identities-azure-resources/overview.md)

Den hanterade identiteten har inte behörighet att komma åt dina Azure-resurser som standard. Du måste ge den behörighet att komma åt dem.

1. Gå till https://portal.azure.com
1. Öppna bladet Resursgrupper
1. Markera den resursgrupp som du vill använda
1. Klicka på Åtkomstkontroll (IAM)
1. Klicka på Lägg till -> Lägg till rolltilldelning
1. Välj rolldeltagare
1. Välj "Virtuell dator" för "Tilldela åtkomst till"
1. Välj den virtuella datorn där DIN SAP LaMa-instans körs på
1. Klicka på Spara
1. Upprepa stegen för alla resursgrupper som du vill använda i SAP LaMa

I din SAP LaMa Azure-anslutningskonfiguration väljer du Använd hanterad identitet för att aktivera användningen av den hanterade identiteten. Om du vill använda en systemtilldelad identitet måste du lämna fältet Användarnamn tomt. Om du vill använda en användartilldelad identitet anger du det användartilldelade identitets-ID:t i fältet Användarnamn.

### <a name="create-a-new-connector-in-sap-lama"></a>Skapa en ny koppling i SAP LaMa

Öppna SAP LaMa-webbplatsen och navigera till infrastruktur. Gå till fliken Molnhanterare och klicka på Lägg till. Välj Microsoft Azure Cloud Adapter och klicka på Nästa. Ange följande information:

* Etikett: Välj ett namn för kopplingsinstansen
* Användarnamn: Tjänsthuvudnamns-ID eller ID för den användare som tilldelats identiteten för den virtuella datorn. Mer information finns i [Använda en system- eller användartilldelad identitet]
* Lösenord: Huvudnyckel/lösenord för tjänsten. Du kan lämna det här fältet tomt om du använder en system- eller användarbesiterad identitet.
* URL: Behåll standard`https://management.azure.com/`
* Övervakningsintervall (sekunder): Bör vara minst 300
* Använd hanterad identitet: SAP LaMa kan använda ett system eller en användartilldelad identitet för att autentisera mot Azure-API:et. Se kapitel [Använd en hanterad identitet för att få åtkomst till Azure-API:et](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) i den här guiden.
* Prenumerations-ID: Azure-prenumerations-ID
* Klient-ID för Azure Active Directory: ID för Active Directory-klienten
* Proxy värd: Värdnamn för proxy om SAP LaMa behöver en proxy för att ansluta till Internet
* Proxyport: TCP-port för proxyn
* Ändra lagringstyp för att spara kostnader: Aktivera den här inställningen om Azure-kortet ska ändra lagringstypen för hanterade diskar för att spara kostnader när diskarna inte används. För datadiskar som refereras i en SAP-instanskonfiguration ändrar kortet disktypen till Standardlagring under en instans som inte är infasad och tillbaka till den ursprungliga lagringstypen under en instansförberedning. Om du stoppar en virtuell dator i SAP LaMa ändrar kortet lagringstypen för alla anslutna diskar, inklusive OS-disken till Standard Storage. Om du startar en virtuell dator i SAP LaMa ändrar kortet lagringstypen tillbaka till den ursprungliga lagringstypen.

Klicka på Testkonfiguration för att validera indata. Du bör se

Anslutningen lyckades: Anslutningen till Microsoft-molnet lyckades. 7 resursgrupper hittades (endast 10 begärda grupper)

längst ner på webbplatsen.

## <a name="provision-a-new-adaptive-sap-system"></a>Etablera ett nytt adaptivt SAP-system

Du kan distribuera en ny virtuell dator manuellt eller använda en av Azure-mallarna i [snabbstartsdatabasen](https://github.com/Azure/azure-quickstart-templates). Den innehåller mallar för [SAP NetWeaver ASCS,](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs) [SAP NetWeaver-programservrar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)och [databasen](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Du kan också använda dessa mallar för att etablera nya värdar som en del av en systemkopia / klon etc.

Vi rekommenderar att du använder ett separat undernät för alla virtuella datorer som du vill hantera med SAP LaMa och inte använder dynamiska IP-adresser för att förhindra att IP-adress "stjäl" när du distribuerar nya virtuella datorer och SAP-instanser är oförberedda.

> [!NOTE]
> Om möjligt tar du bort alla tillägg för virtuella datorer eftersom de kan orsaka långa körningstider för att koppla bort diskar från en virtuell dator.

Se till \<att användaren hanasid>\<adm, sapsid>adm och grupp sapsys finns på måldatorn med samma ID och gid eller använda LDAP. Aktivera och starta NFS-servern på de virtuella datorer som ska användas för att köra SAP NetWeaver (A)SCS.

### <a name="manual-deployment"></a>Manuell distribution

SAP LaMa kommunicerar med den virtuella datorn med hjälp av SAP-värdagenten. Om du distribuerar de virtuella datorerna manuellt eller inte använder Azure Resource Manager-mallen från snabbstartsdatabasen, se till att installera den senaste SAP-värdagenten och SAP Adaptive Extensions. Mer information om de korrigeringsnivåer som krävs för Azure finns i SAP Note [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Manuell distribution av en virtuell Linux-dator

Skapa en ny virtuell dator med ett av de operativsystem som stöds i SAP Note [2343511]. Lägg till ytterligare IP-konfigurationer för SAP-instanserna. Varje instans behöver minst på IP-adress och måste installeras med ett virtuellt värdnamn.

SAP NetWeaver ASCS-instansen behöver diskar\<för /sapmnt/ SAPSID>,\</usr/sap/ SAPSID>, /usr/sap/trans\<och /usr/sap/ sapsid>adm. PROGRAMservrarna SAP NetWeaver behöver inga ytterligare diskar. Allt som har med SAP-instansen att göra måste lagras på ASCS och exporteras via NFS. Annars är det för närvarande inte möjligt att lägga till ytterligare programservrar med SAP LaMa.

![SAP NetWeaver ASCS på Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Manuell distribution för SAP HANA

Skapa en ny virtuell dator med ett av de operativsystem som stöds för SAP HANA som anges i SAP Note [2343511]. Lägg till ytterligare en IP-konfiguration för SAP HANA och en per HANA-klient.

SAP HANA behöver diskar för /hana/shared, /hana/backup, /hana/data och /hana/log

![SAP HANA på Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Manuell distribution för Oracle-databas på Linux

Skapa en ny virtuell dator med ett av de operativsystem som stöds för Oracle-databaser som anges i SAP Note [2343511]. Lägg till ytterligare en IP-konfiguration för Oracle-databasen.

Oracle-databasen behöver diskar för /oracle, /home/oraod1 och /home/oracle

![Oracle-databas på Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Manuell distribution för Microsoft SQL Server

Skapa en ny virtuell dator med ett av de operativsystem som stöds för Microsoft SQL Server som anges i SAP Note [2343511]. Lägg till ytterligare en IP-konfiguration för SQL Server-instansen.

SQL Server-databasservern behöver diskar för databasdata och loggfiler och diskar för c:\usr\sap.

![Oracle-databas på Linux](media/lama/sap-lama-db-sql.png)

Se till att installera en Microsoft ODBC-drivrutin som stöds för SQL Server på en virtuell dator som du vill använda för att flytta en SAP NetWeaver-programserver till eller som ett mål för systemkopiering/klon.

SAP LaMa kan inte flytta SQL Server själv så en virtuell dator som du vill använda för att flytta en databasinstans till eller som ett mål för systemkopiering/klon behöver SQL Server förinstallerat.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Distribuera virtuell dator med en Azure-mall

Ladda ner följande senaste tillgängliga arkiv från [SAP Software Marketplace](https://support.sap.com/swdc) för operativsystemet för de virtuella datorerna:

1. SAPCAR 7,21
1. SAP VÄRD AGENT 7,21
1. SAP ADAPTIV FÖRLÄNGNING 1,0 EXT

Hämta även följande komponenter från [Microsoft Download Center](https://www.microsoft.com/download)

1. Microsoft Visual C++ 2010 Redistributable Package (x64) (endast Windows)
1. Microsoft ODBC-drivrutin för SQL Server (endast SQL Server)

Komponenterna krävs för att distribuera mallen. Det enklaste sättet att göra dem tillgängliga för mallen är att ladda upp dem till ett Azure-lagringskonto och skapa en SAS (Shared Access Signature).

Mallarna har följande parametrar:

* sapSystemId: SAP-system-ID. Den används för att skapa disklayouten (till\<exempel /usr/sap/ sap/ sapsid>).

* datornamn: Datornamnet på den nya virtuella datorn. Den här parametern används också av SAP LaMa. När du använder den här mallen för att etablera en ny virtuell dator som en del av en systemkopia väntar SAP LaMa tills värden med det här datornamnet kan nås.

* osType: Den typ av operativsystem som du vill distribuera.

* dbtype: Typ av databas. Den här parametern används för att avgöra hur många ytterligare IP-konfigurationer som behöver läggas till och hur disklayouten ska se ut.

* sapSystemSize: Storleken på det SAP-system som du vill distribuera. Den används för att bestämma typ och storlek för instans av den virtuella datorn.

* adminUsername: Användarnamn för den virtuella datorn.

* adminPassword: Lösenord för den virtuella datorn. Du kan också ange en offentlig nyckel för SSH.

* sshKeyData: Offentlig SSH-nyckel för de virtuella datorerna. Stöds endast för Linux-operativsystem.

* subnetId: ID:t för det undernät som du vill använda.

* deployEmptyTarget: Du kan distribuera ett tomt mål om du vill använda den virtuella datorn som ett mål för en instans flytta eller liknande. I det här fallet är inga ytterligare diskar eller IP-konfigurationer anslutna.

* sapcarLocation: Platsen för sapcar-programmet som matchar operativsystemet du distribuerar. sapcar används för att extrahera de arkiv du tillhandahåller i andra parametrar.

* sapHostAgentArchiveLocation: Platsen för SAP-värdagentarkivet. SAP Host Agent distribueras som en del av den här malldistributionen.

* sapacExtLocation: Platsen för SAP Adaptive Extensions. SAP Note [2343511] listar den lägsta korrigeringsnivån som krävs för Azure.

* vcRedistLocation: Platsen för VC Runtime som krävs för att installera SAP Adaptive Extensions. Den här parametern krävs endast för Windows.

* odbcDriverLocation: Platsen för ODBC-drivrutinen som du vill installera. Endast Microsoft ODBC-drivrutin för SQL Server stöds.

* sapadmPassword: Lösenordet för sapadm användaren.

* sapadmId: Linux-användar-ID för sapadm användaren. Krävs inte för Windows.

* sapsysGid: Linux-grupp-ID för sapsys-gruppen. Krävs inte för Windows.

* _artifactsLocation: Bas-URI, där artefakter som krävs enligt den här mallen finns. När mallen distribueras med hjälp av de medföljande skripten används en privat plats i prenumerationen och det här värdet genereras automatiskt. Behövs bara om du inte distribuerar mallen från GitHub.

* _artifactsLocationSasToken: SasToken krävs för att komma åt _artifactsLocation. När mallen distribueras med hjälp av de medföljande skripten genereras en sasToken automatiskt. Behövs bara om du inte distribuerar mallen från GitHub.

### <a name="sap-hana"></a>SAP HANA

I exemplen nedan förutsätter vi att du installerar SAP HANA med system-ID HN1 och SAP NetWeaver-systemet med system-ID AH1. Den virtuella värdnamn är hn1-db för HANA instans, ah1-db för HANA klienten som används av SAP NetWeaver systemet, ah1-ascs för SAP NetWeaver ASCS och ah1-di-0 för den första SAP NetWeaver programserver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Installera SAP NetWeaver ASCS för SAP HANA med Azure Managed Disks

Innan du startar SWPM (SAP Software Provisioning Manager) måste du montera IP-adressen för det virtuella värdnamnet för ASCS. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext, se till att återmontera IP-adressen efter en omstart.

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

Kör SWPM och använd *ah1-ascs* för *ASCS-instansens värdnamn*.

![Linux][Logo_Linux] Linux  
Lägg till följande profilparameter i PROFILEN SAP Host Agent, som finns på /usr/sap/hostctrl/exe/host_profile. Mer information finns i SAP Note [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Installera SAP NetWeaver ASCS för SAP HANA på AZURE NetAppFiles (ANF) BETA

> [!NOTE]
> Den här funktionen är inte heller GA ännu. Mer information finns i SAP Note [2815988] (endast synlig för förhandsgranskningskunder).
Öppna en SAP-incident på komponenten BC-VCM-LVM-HYPERV och begäran om att ansluta till LaMa-lagringskortet för förhandsversionen av Azure NetApp Files

ANF tillhandahåller NFS för Azure. Inom ramen för SAP LaMa förenklar detta skapandet av ASCS-instanserna (ABAP Central Services) och den efterföljande installationen av applikationsservrar. Tidigare ascs-instansen måste fungera som NFS-server också och parametern acosprep/nfs_paths måste läggas till host_profile av SAP-värdagenten.

#### <a name="anf-is-currently-available-in-these-regions"></a>ANF är för närvarande tillgängligt i dessa regioner:

Östra Australien, Centrala USA, Östra USA, Östra USA 2, Norra Europa, Södra centrala USA, Västeuropa och Västra USA 2.

#### <a name="network-requirements"></a>Nätverkskrav

ANF kräver ett delegerat undernät som måste vara en del av samma virtuella nätverk som SAP-servrarna. Här är ett exempel för en sådan konfiguration.
Den här skärmen visar skapandet av VNET och det första undernätet:

![SAP LaMa skapa virtuellt nätverk för Azure ANF ](media/lama/sap-lama-createvn-50.png)

I nästa steg skapas det delegerade undernätet för Microsoft.NetApp/volymer.

![SAP LaMa lägga till delegerat undernät ](media/lama/sap-lama-addsubnet-50.png)

![SAP LaMa lista över undernät ](media/lama/sap-lama-subnets.png)

Nu måste ett NetApp-konto skapas i Azure-portalen:

![SAP LaMa skapa NetApp-konto ](media/lama/sap-lama-create-netappaccount-50.png)

![SAP LaMa NetApp-konto skapat ](media/lama/sap-lama-netappaccount.png)

Inom NetApp-kontot anger kapacitetspoolen storleken och typen av diskar för varje pool:

![SAP LaMa skapa NetApp kapacitet pool ](media/lama/sap-lama-capacitypool-50.png)

![SAP LaMa NetApp kapacitet pool skapas ](media/lama/sap-lama-capacitypool-list.png)

NFS-volymerna kan nu definieras. Eftersom det kommer att finnas volymer för flera system i en pool bör ett självutnäklarande namngivningsschema väljas. Genom att lägga till SID kan du gruppera relaterade volymer tillsammans. För ASCS- och AS-instansen behövs följande fästen: */sapmnt/\<SID\>*, */usr/sap/\<SID\>* och */home/\<sid\>adm*. Alternativt behövs */usr/sap/trans* för den centrala transportkatalogen, som åtminstone används av alla system i ett landskap.

> [!NOTE]
> Under BETA-fasen måste namnet på volymerna vara unikt inom prenumerationen.

![SAP LaMa skapa en volym 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa skapa en volym 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa skapa en volym 3 ](media/lama/sap-lama-createvolume3-80.png)

Dessa steg måste upprepas även för de andra volymerna.

![SAP LaMa lista över skapade volymer ](media/lama/sap-lama-volumes.png)

Nu måste dessa volymer monteras på de system där den första installationen med SAP SWPM kommer att utföras.

Först måste monteringspunkterna skapas. I det här fallet är SID AN1 så följande kommandon måste köras:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Därefter monteras ANF-volymerna med följande kommandon:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Monteringskommandona kan också härledas från portalen. De lokala monteringspunkterna måste justeras.

Använd kommandot df -h för att verifiera.

![SAP LaMa montera punkter OS-nivå ](media/lama/sap-lama-mounts.png)

Nu måste installationen med SWPM utföras.

Samma steg måste utföras för minst en AS-instans.

Efter den lyckade installationen måste systemet identifieras inom SAP LaMa.

Monteringspunkterna ska se ut så här för ASCS- och AS-instansen:

![SAP LaMa montera punkter ](media/lama/sap-lama-ascs.png) i LaMa (Detta är ett exempel. IP-adresserna och exportsökvägen skiljer sig från de som användes tidigare)


#### <a name="install-sap-hana"></a>Installera SAP HANA

Om du installerar SAP HANA med kommandoradsverktyget hdblcm använder du parametern --hostname för att tillhandahålla ett virtuellt värdnamn. Du måste lägga till IP-adressen för databasens virtuella värdnamn i ett nätverksgränssnitt. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext, se till att återmontera IP-adressen efter en omstart.

Lägg till ett annat virtuellt värdnamn och IP-adress för det namn som används av programservrarna för att ansluta till HANA-klienten.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Kör databasinstansinstallationen av SWPM på den virtuella datorn för programservern, inte på den virtuella HANA-datorn. Använd *ah1-db* för *databasvärden* i dialogrutan *Databas för SAP-system*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Installera SAP NetWeaver Application Server för SAP HANA

Innan du startar SWPM (SAP Software Provisioning Manager) måste du montera IP-adressen för det virtuella värdnamnet för programservern. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext, se till att återmontera IP-adressen efter en omstart.

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

Det rekommenderas att använda SAP NetWeaver profil parameter dbs/hdb/hdb_use_ident för att ställa in den identitet som används för att hitta nyckeln i HDB userstore. Du kan lägga till den här parametern manuellt efter databasinstansinstallationen med SWPM eller köra SWPM med

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Om du ställer in den manuellt måste du också skapa nya HDB-userstore-poster.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Använd *ah1-di-0* för *PAS-instansens värdnamn* i dialogrutan *Primär programserverinstans*.

#### <a name="post-installation-steps-for-sap-hana"></a>Steg efter installation för SAP HANA

Se till att säkerhetskopiera SYSTEMDB och alla klientdatabaser innan du försöker göra en klientkopia, klientflytta eller skapa en systemreplikering.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

I exemplen nedan antar vi att du installerar SAP NetWeaver-systemet med system-ID AS1. De virtuella värdnamnen är as1-db för SQL Server-instansen som används av SAP NetWeaver-systemet, as1-ascs för SAP NetWeaver ASCS och as1-di-0 för den första SAP NetWeaver-programservern.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Installera SAP NetWeaver ASCS för SQL Server

Innan du startar SWPM (SAP Software Provisioning Manager) måste du montera IP-adressen för det virtuella värdnamnet för ASCS. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext, se till att återmontera IP-adressen efter en omstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Kör SWPM och använd *as1-ascs* för *ASCS-instansens värdnamn*.

#### <a name="install-sql-server"></a>Installera SQL Server

Du måste lägga till IP-adressen för databasens virtuella värdnamn i ett nätverksgränssnitt. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext, se till att återmontera IP-adressen efter en omstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Kör databasinstansinstallationen av SWPM på den virtuella SQL-servern-datorn. Använd SAPINST_USE_HOSTNAME=*as1-db* för att åsidosätta värdnamnet som används för att ansluta till SQL Server. Om du har distribuerat den virtuella datorn med azure Resource Manager-mallen kontrollerar du att katalogen som används för databasdatafilerna ska anges till *C:\sql\data* och databasloggfilen till *C:\sql\log*.

Kontrollera att användaren *NT AUTHORITY\SYSTEM* har åtkomst till SQL Server och har serverrollen *sysadmin*. Mer information finns i SAP Note [1877727] och [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Installera SAP NetWeaver Application Server

Innan du startar SWPM (SAP Software Provisioning Manager) måste du montera IP-adressen för det virtuella värdnamnet för programservern. Det rekommenderade sättet är att använda sapacext. Om du monterar IP-adressen med sapacext, se till att återmontera IP-adressen efter en omstart.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Använd *som1-di-0* för *PAS-instansens värdnamn* i dialogrutan *Primär programserverinstans*.

## <a name="troubleshooting"></a>Felsökning

### <a name="errors-and-warnings-during-discover"></a>Fel och varningar under Upptäck

* SELECT-behörigheten nekades
  * -Jag vet inte vad du är på. [ODBC SQL Server Driver] [SQL Server] Select-behörigheten nekades på objektet "log_shipping_primary_databases", databasen "msdb", schema 'dbo'. [SOAPFaultException]  
  Select-behörigheten nekades på objektet "log_shipping_primary_databases", databasen "msdb", schema 'dbo'.
  * Lösning  
    Kontrollera att *NT AUTHORITY\SYSTEM* kan komma åt SQL Server. Se SAP Note [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Fel och varningar för instansvalidering

* Ett undantag togs upp i valideringen av HDB-userstore  
  * se Loggvisaren  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: Undantag i valideraren med ID 'RuntimeHDBConnectionValidator' (Validering: 'VALIDATION_HDB_USERSTORE'): Kunde inte hämta hdbuserstore  
    HANA-användarbutiken är inte på rätt plats
  * Lösning  
    Kontrollera att /usr/sap/AH1/hdbclient/install/installation.ini är korrekt

### <a name="errors-and-warnings-during-a-system-copy"></a>Fel och varningar under en systemkopia

* Ett fel uppstod när systemetableringssteget skulle valideras
  * Orsakas av: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 050\;-h hn1-db -o level=0 status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Lösning  
    Ta backup av alla databaser i källa HANA-system

* *Stegvisa systemkopieringssteg* för databasinstans
  * Värdagentåtgärden '000D3A282BC91EE8A1D76CF1F92E2944' misslyckades (OperationException. FaultCode: '127', Meddelande: "Kommandokörning misslyckades. : [Microsoft][ODBC SQL Server Driver][SQL Server]Användaren har inte behörighet att ändra databasen 'AS2', databasen finns inte eller databasen är inte i ett tillstånd som tillåter åtkomstkontroller.')
  * Lösning  
    Kontrollera att *NT AUTHORITY\SYSTEM* kan komma åt SQL Server. Se SAP Note [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Fel och varningar under en systemklon

* Ett fel uppstod när instansagenten skulle registreras i steg *Tvångsregister och Start-instansagent för* programserver eller ASCS
  * Ett fel uppstod när instansagenten skulle registreras. (RemoteException: 'Det gick inte att\\läsa in instansdata från profilen ' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Det går inte att komma åt profilen as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0':\\Ingen sådan fil eller katalog.')
  * Lösning  
   Kontrollera att sapmnt-resursen på ASCS/SCS har full åtkomst för SAP_AS1_GlobalAdmin

* Fel i steg *Aktivera startskydd för klon*
  * Det gick inte\\att öppna filen ' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Orsak: Ingen sådan fil eller katalog
  * Lösning  
    Programserverns datorkonto behöver skrivåtkomst till profilen

### <a name="errors-and-warnings-during-create-system-replication"></a>Fel och varningar under Skapa systemreplikering

* Undantag när du klickar på Skapa systemreplikering
  * Orsakas av: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 050\;-h hn1-db -o level=0 status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * Lösning  
    Testa om sapacext kan `<hanasid` utföras som>adm

* Fel när fullständig kopia inte är aktiverat i Lagringssteg
  * Ett fel uppstod när ett kontextattributmeddelande för sökvägen IStorageCopyData.storageVolumeCopyList:1 och fält targetStorageSystemId
  * Lösning  
    Ignorera varningar i steg och försök igen. Det här problemet kommer att åtgärdas i ett nytt supportpaket/patch av SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Fel och varningar under flytta

* Sökväg '/usr/sap/AH1' är inte tillåtet för nfs reexports.
  * Mer information finns i SAP Note [2628497.]
  * Lösning  
    Lägg till ASCS-exporter till ASCS HostAgent-profil. Se SAP Note [2628497]

* Funktionen har inte implementerats vid flytt av ASCS
  * Kommandoutdata: exportf:/usr/sap/AX1: Funktionen har inte implementerats
  * Lösning  
    Kontrollera att NFS-servertjänsten är aktiverad på den virtuella enheten för att flytta målet

### <a name="errors-and-warnings-during-application-server-installation"></a>Fel och varningar under installationen av programservern

* Fel vid körning av SAPinst-steget: getProfileDir
  * FEL: (Senaste fel som rapporterats av steget: Fångade ESAPinstException i modulanrop: Validerare av steg '| NW_DI|ind|ind|ind|ind|0|0| NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0| NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' rapporterade ett fel: Det \\finns inget fel: Det finns inget fel: Det finns inget fel: Det finns inget fel: Det finns inget fel: Det finns ingen nod \ascs\sapmnt\AS1\SYS\profile. Starta SAPinst i interaktivt läge för att lösa detta problem)
  * Lösning  
    Kontrollera att SWPM körs med en användare som har åtkomst till profilen. Den här användaren kan konfigureras i guiden Installation av programserver

* Fel vid körning av SAPinst-steget: frågaUnicode
  * FEL: (Senaste fel som rapporterats av steget: Fångade ESAPinstException i modulanrop: Validerare av steg '| NW_DI|ind|ind|ind|ind|0|0| NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0| NW_getUnicode|ind|ind|ind|ind|unicode|0|askUnicode" rapporterade ett fel: Starta SAPinst i interaktivt läge för att lösa problemet)
  * Lösning  
    Om du använder en nyligen genomförd SAP-kärna kan SWPM inte avgöra om systemet är ett unicode-system längre med hjälp av meddelandeservern för ASCS. Mer information finns i SAP Note [2445033.]  
    Det här problemet kommer att åtgärdas i ett nytt supportpaket/patch av SAP LaMa.  
    Ange profilparametern OS_UNICODE=uc i SAP-systemets standardprofil för att lösa problemet.

* Fel vid körning av SAPinst-steg: dCheckGivenServer
  * Fel vid körning av SAPinst-steg: dCheckGivenServer" version="1.0" FEL: (Senaste fel som rapporterats av steget: \<p> Installationen avbröts av användaren. \</p>
  * Lösning  
    Kontrollera att SWPM körs med en användare som har åtkomst till profilen. Den här användaren kan konfigureras i guiden Installation av programserver

* Fel vid körning av SAPinst-steget: checkClient
  * Fel vid körning av SAPinst-steg: checkClient" version="1.0" FEL: (Senaste fel som rapporterats av steget: \<p> Installationen avbröts av användaren. \</p>)
  * Lösning  
    Kontrollera att Microsoft ODBC-drivrutinen för SQL Server är installerad på den virtuella datorn där du vill installera programservern

* Fel vid körning av SAPinst-steg: copyScripts
  * Senaste fel som rapporterades av steget: Systemanropet misslyckades. DETALJER: Fel 13 (0x0000000d) (Behörighet nekad) vid utförande av systemanropet "fopenU" med parameter (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd. w), linje (494) i fil (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), stapelspårning:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction(iastring const& namn, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath \\& \aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Lösning  
    Kontrollera att SWPM körs med en användare som har åtkomst till profilen. Den här användaren kan konfigureras i guiden Installation av programserver

* Fel vid körning av SAPinst-steget: askPasswords
  * Senaste fel som rapporterades av steget: Systemanropet misslyckades. DETALJER: Fel 5 (0x00000005) (Access nekas.) vid utförande av systemanropet "NetValidatePasswordPolicy" med parametern (...), rad (359) i filen (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), stackspårning:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledAvJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& namn, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * Lösning  
    Se till att lägga till en värdregel i steg *Isolering* för att tillåta kommunikation från den virtuella datorn till domänkontrollanten

## <a name="next-steps"></a>Nästa steg
* [Användarguide för SAP HANA på Azure][hana-ops-guide]
* [Planering och implementering av virtuella Azure-datorer för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
