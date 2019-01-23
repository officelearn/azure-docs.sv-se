---
title: Ange ny värd för en lokal Contoso-app genom att migrera till Azure virtuella datorer och Azure SQL Database Managed Instance | Microsoft Docs
description: Lär dig hur Contoso namnkonflikt en lokal app på Azure Virtual Machines och genom att använda Azure SQL Database Managed Instance.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 3564288c55716a9c64ae2460048cb255a60dcc02
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469584"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Contoso-migrering: Ange ny värd för en lokal app på en virtuell Azure-dator och SQL Database Managed Instance

I den här artikeln Contoso migrerar dess SmartHotel360 app klientdelens VM till en Azure virtuell dator med hjälp av Azure Site Recovery-tjänsten. Contoso migrerar också app-databas till Azure SQL Database Managed Instance.

> [!NOTE]
> Azure SQL Database Managed Instance finns för närvarande i förhandsversion.

Den här artikeln är i en serie av artiklar som dokumenterar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och ett antal scenarier som illustrerar hur du konfigurerar en infrastruktur för migreringen och kör olika typer av migreringar. Scenarier växer i komplexitet. Artiklar läggs till seriens över tid.


**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: Översikt över](contoso-migration-overview.md) | Översikt över Contosos migreringsstrategi, artikelserien och exempelappar som används i serien. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md) | Contoso kör en utvärdering av dess lokal tvålagers-SmartHotel-app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av den [Azure Migrate](migrate-overview.md) service. Contoso utvärderar app SQL Server-databas med hjälp av [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel app. Contoso migrerar appen klientdelens VM med hjälp av [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Den här artikeln
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel app virtuella datorer till virtuella Azure-datorer med hjälp av Site Recovery-tjänsten. | Tillgängligt
[Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Contoso migrerar SmartHotel appen. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen. Database Migration Service används för att migrera app-databas till SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Ange ny värd för en app för Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso Slutför en lift and shift-migrering av dess Linux osTicket app på virtuella Azure-datorer med Site Recovery. | Tillgängligt
[Artikel 8: Ange ny värd för en app för Linux på Azure virtuella datorer och Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar dess osTicket Linux-app till virtuella Azure-datorer med hjälp av Site Recovery. Den migrerar app-databas till Azure Database för MySQL med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app i en Azure webbapp och Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar dess SmartHotel-app till en Azure webbapp och migrerar app-databasen till en Azure SQL Server-instans. | Tillgängligt
[Artikel 10: Omstrukturera en Linux-app i en Azure-webbapp och en Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera platser. Webbappen är integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera Team Foundation Server på Azure DevOps-tjänsterna](contoso-migration-tfs-vsts.md) | Contoso migrerar sin lokala Team Foundation Server-distributionen genom att migrera den till Azure DevOps-tjänsterna i Azure. | Tillgängligt
[Artikel 12: Omforma en app i Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migrerar dess SmartHotel-app till Azure och sedan rearchitects appen. Contoso rearchitects webbnivån appen som en Windows-behållare och rearchitects app-databasen med hjälp av Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Contoso återskapas dess SmartHotel-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service, Azure Functions, Azure Cognitive Services och Azure Cosmos DB. | Tillgängligt
[Artikel 14: Skala en migrering till Azure](contoso-migration-scale.md) | När du testar migreringen kombinationer, förbereder Contoso att skala upp till en fullständig migrering till Azure. | Tillgängligt




Du kan hämta SmartHotel360-exempelappen som används i den här artikeln från [GitHub](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Affärsdrivande faktorer

Contosos IT-ledning har arbetat tillsammans med företagets affärspartners att förstå vad företaget vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer. Därför ökat trycket på företagets lokala system och infrastruktur.
- **Öka effektiviteten**: Contoso behöver du ta bort onödiga procedurer och att effektivisera processer för utvecklare och användare. Affärsbehov IT är fast och som inte spill tid eller pengar, så företaget kan leverera snabbare på kunders behov.
- **Förbättrad flexibilitet**:  Contoso IT måste vara mer responsiva gentemot behoven i verksamheten. Måste kunna reagera snabbare än de ändringar som sker i marketplace för företag för att utföra en global ekonomi. IT på Contoso måste inte vara i vägen eller bli en blockerare för företag.
- **Skala**: När företagets verksamhet växer har måste Contoso IT ange system som kan växa i samma takt.

## <a name="migration-goals"></a>Migreringsmål

Contoso cloud-teamet har identifierat mål för den här migreringen. Företaget använder migreringen mål för att avgöra den bästa migreringsmetoden.

- Efter migreringen bör app i Azure ha samma prestandafunktioner som appen har idag i Contosos lokala VMWare-miljön. Flytta till molnet innebär inte att prestanda är mindre viktigt.
- Contoso vill inte investera i appen. Appen är kritiska och viktiga för verksamheten, men Contoso helt enkelt vill flytta appen i sin nuvarande form till molnet.
- Databasadministrationsuppgifter värden ska minimeras, efter att appen har migrerats.
- Contoso vill inte använda en Azure SQL-databas för den här appen. Är ute efter alternativ.


## <a name="solution-design"></a>Lösningsdesign

Efter att fästa ned sina mål och krav, Contoso utformar går igenom en distributionslösning och identifierar migreringsprocessen, inklusive Azure-tjänster som ska användas för migreringen.

### <a name="current-architecture"></a>Aktuella arkitektur 

- Contoso har en huvuddatacentret (**contoso-datacenter**). Datacentret finns i den stad i New York i östra USA.
- Contoso har tre ytterligare lokala grenar i USA.
- Huvuddatacentret är ansluten till internet med en fiber appar i Metro-Ethernet-anslutning (500 Mbit/s).
- Varje gren är lokalt ansluten till internet med hjälp av företagsklass anslutningar med IPsec VPN-tunnlar till huvuddatacentret. Installationsprogrammet tillåter Contosos hela nätverket ska anslutas permanent och optimerar Internetanslutning.
- Huvuddatacentret är fullständigt virtualiserat med VMware. Contoso har två ESXi 6.5-virtualiseringsvärdar som hanteras av vCenter Server 6.5.
- Contoso använder Active Directory för Identitetshantering. Contoso använder DNS-servrar i det interna nätverket.
- Contoso har en lokal domänkontrollant (**contosodc1**).
- Domänkontrollanter som kör på virtuella VMware-datorer. Domänkontrollanter på lokala grenar som körs på fysiska servrar.
- SmartHotel360-appen är nivåindelad över två virtuella datorer (**WEBVM** och **SQLVM**) som finns på en version 6.5 VMware ESXi-värd (**contosohost1.contoso.com**). 
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**) som körs på en virtuell dator.

![Aktuell Contoso-arkitektur](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

### <a name="proposed-architecture"></a>Föreslagna arkitektur

I det här scenariot Contoso som vill migrera dess tvålagers-lokal reseapp på följande sätt:

- Migrera app-databasen (**SmartHotelDB**) till en Azure SQL Database Managed Instance.
- Migrera klientdelen **WebVM** till en Azure virtuell dator.
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.

![Scenariots arkitektur](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="database-considerations"></a>Överväganden för databasen

Som en del av designprocessen gjorde Contoso en funktionsjämförelse mellan Azure SQL Database och SQL Server-hanterad instans. Följande överväganden hjälpte dem att bestämma att gå med Managed Instance.

- Hanterade instans syftet är att leverera nästan 100% kompatibilitet med den senaste versionen av den lokala SQL Server. Microsoft rekommenderar att den hanterade instansen för kunder som kör SQL Server lokalt eller på IaaS VM som vill migrera sina appar till en helt hanterad tjänst med minimal designändringar.
- Contoso överväger att migrera ett stort antal appar från en lokal plats till IaaS. Många av dessa är ISV-tillhandahålls. Contoso inser att genom att använda Managed Instance kan kontrollera databaskompatibilitet för dessa appar i stället för att använda SQL-databas som inte stöds.
- Contoso kan bara göra en lift and shift-migrering till Managed Instance med helt automatiserad Data Migration Service (DMS). Med den här tjänsten på plats, kan Contoso återanvända den för framtida databasmigreringar.
- SQL-hanterad instans har stöd för SQL Server Agent som är en viktig fråga för SmartHotel360-appen. Contoso behöver denna kompatibilitet, annars får förändra underhållsplaner som krävs av appen.
- Contoso kan utbyta sina befintliga licenser för rabatterade priser på en SQL Database Managed Instance med Azure Hybrid-förmånen för SQL Server med Software Assurance. Det kan göra Contoso att spara upp till 30 procent på hanterad instans.
- Hanterad instans finns helt i det virtuella nätverket, så den ger en hög nivå av isolering och säkerhet för Contosos data. Contoso kan få fördelarna med det offentliga molnet, samtidigt som den miljö som är isolerade från det offentliga Internet.
- Hanterad instans stöder många säkerhetsfunktioner inklusive alltid krypterad, dynamisk datamaskning, säkerhet på radnivå och hotidentifiering.


### <a name="solution-review"></a>Granska lösningen

Contoso utvärderar den föreslagna designen genom att sätta ihop en lista med för- och nackdelar.

**Beräkningen** | **Detaljer**
--- | ---
**Experter** |  WEBVM kommer att flyttas till Azure utan ändringar, kan du förenkla migreringen.<br/><br/> SQL-hanterad instans stöder Contosos tekniska krav och mål.<br/><br/> Hanterade instansen får du 100% kompatibilitet med deras aktuella distribution, medan du flyttar dem från SQL Server 2008 R2.<br/><br/>  De kan använda sina investering i Software Assurance och använda Azure Hybrid-förmånen forSQL Server och Windows Server.<br/><br/> De kan återanvända Database Migration Service för ytterligare framtida migreringar.<br/><br/> SQL-hanterad instans har inbyggda feltolerans som Contoso inte behöver konfigurerar. Detta säkerställer att datanivå inte längre en enda åtkomstpunkt för redundans.
**Nackdelar** | WEBVM kör Windows Server 2008 R2.  Även om det här operativsystemet stöds av Azure kan den inte längre är plattform som stöds. [Läs mer](https://support.microsoft.com/en-us/help/956893).<br/><br/> Webbnivån förblir en enda åtkomstpunkt för redundans med endast WEBVM som tillhandahåller tjänster.<br/><br/> Contoso behöver att fortsätta stödja app-webbnivån som en virtuell dator i stället för att flytta till en hanterad tjänst, till exempel Azure App Service.<br/><br/> För datanivån kanske Managed Instance inte den bästa lösningen om Contoso vill anpassa operativsystemet eller databasservern, eller om de vill köra appar från tredje part tillsammans med SQL Server. Kör SQL Server på en IaaS-VM kan ange den här flexibiliteten. 

### <a name="migration-process"></a>Migreringsprocessen

Contoso migrera webb- och data nivåer av dess SmartHotel360-app till Azure genom att följa dessa steg:

1. Contoso har redan Azure-infrastruktur på plats, så den behöver bara lägga till några specifika Azure-komponenter för det här scenariot.
2. Datanivån kommer att migreras med hjälp av Data Migration Service. Data Migration Service ansluter till en lokal SQL Server-dator via en plats-till-plats VPN-anslutning mellan Contoso-datacenter och Azure. Data Migration Service migrerar sedan databasen.
3. Webbnivån kommer att migreras med hjälp av en lift and shift-migrering med hjälp av Site Recovery. Signeras förbereda lokala VMware-miljö, konfigurerar och aktiverar replikering och migrerar de virtuella datorerna genom att föra över dem till Azure.

     ![Migreringsarkitektur för](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

### <a name="azure-services"></a>Azure-tjänster

Tjänst | Beskrivning | Kostnad
--- | --- | ---
[Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Database Migration Service gör det möjligt för sömlös migrering från flera databaskällor till Azure-Dataplattformar med minimal avbrottstid. | Lär dig mer om [regioner som stöds](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) och [Database Migration Service priser](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance är en hanterad databastjänst som representerar en fullständigt hanterad SQL Server-instans i Azure-molnet. Den använder samma kod som den senaste versionen av SQL Server Database Engine och har de senaste funktionerna, prestandaförbättringar och säkerhetsuppdateringar. | Med hjälp av en SQL Database Managed Instance som körs i Azure debiteras avgifter baserat på kapacitet. Läs mer om [Managed Instance priser](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten Site Recovery dirigerar och hanterar migrering och haveriberedskap för virtuella datorer i Azure och lokala datorer och fysiska servrar.  | Azure Storage-avgifter tillkommer under replikering till Azure.  Virtuella Azure-datorer skapas och betalar avgifter vid redundans. Läs mer om [Site Recovery-avgifter och priser](https://azure.microsoft.com/pricing/details/site-recovery/).

 

## <a name="prerequisites"></a>Förutsättningar

Contoso och andra användare måste uppfylla följande krav för det här scenariot:

Krav | Information
--- | ---
**Registrera dig i förhandsversionen av Managed Instance** | Du måste vara registrerade i den begränsade offentliga förhandsversionen för SQL Database Managed Instance. Du behöver en Azure-prenumeration till [registrera](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Registreringen kan ta ett par dagar att slutföra, så se till att registrera dig innan du börjar distribuera det här scenariot.
**Azure-prenumeration** | Du bör redan har skapat en prenumeration när du utför utvärderingen i den första artikeln i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör för prenumerationen, måste du be administratören tilldela dig ägar-eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan se [använda rollbaserad åtkomstkontroll för att hantera Site Recovery åtkomst](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site Recovery (lokalt)** | Din lokala vCenter Server-instansen bör köra version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värden.<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Stöds [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [storage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguration.
**Database Migration Service** | För Database Migration Service, behöver du en [kompatibla lokala VPN-enheten](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Du måste kunna konfigurera den lokala VPN-enhet. Den måste ha en offentlig IPv4-adress för utåtriktade. Adressen får inte finnas bakom en NAT-enhet.<br/><br/> Kontrollera att du har åtkomst till din lokala SQL Server-databas.<br/><br/> Windows-brandväggen ska kunna komma åt käll-databasmotorn. Lär dig hur du [konfigurerar Windows-brandväggen för access Database Engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Om det finns en brandvägg framför din databasdator, lägger du till regler för att tillåta åtkomst till databasen och filer via SMB-port 445.<br/><br/> Autentiseringsuppgifterna som används för att ansluta till SQL Server-källinstansen och som mål Managed Instance måste vara medlemmar i rollen sysadmin.<br/><br/> Du behöver ett nätverk delar i din lokala databas som Database Migration Service kan använda för att säkerhetskopiera källdatabasen.<br/><br/> Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörigheter på nätverksresursen.<br/><br/> Anteckna en Windows-användare och lösenord som har fullständig behörighet på nätverksresursen. Database Migration Service personifierar dessa autentiseringsuppgifter för att ladda upp filer för säkerhetskopiering till Azure Storage-behållare.<br/><br/> SQL Server Express-installationen anger TCP/IP-protokollet till **inaktiverad** som standard. Kontrollera att den är aktiverad.

## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso planerar att konfigurera distributionen:

> [!div class="checklist"]
> * **Steg 1: Konfigurera en SQL Database Managed Instance**: Contoso behöver en förskapad hanterad instans som en lokal SQL Server-databasen ska migreras.
> * **Steg 2: Förbereda Database Migration Service**: Contoso måste registrera databasprovider för migrering, skapa en instans och sedan skapa ett projekt med Database Migration Service. Contoso måste ställa in en signatur för delad åtkomst (SAS) identifierare URI (Uniform Resource) för Database Migration Service. En SAS-URI ger delegerad åtkomst till resurser i Contosos storage-konto, så Contoso kan ge begränsade behörigheter till storage-objekt. Contoso ställer in en SAS-URI så Database Migration Service kan komma åt den lagringskontobehållare som tjänsten Överför säkerhetskopieringsfilerna för SQL Server.
> * **Steg 3: Förbereda Azure Site Recovery**: Contoso måste skapa ett lagringskonto för att lagra replikerade data för Site Recovery. Det måste också skapa ett Azure Recovery Services-valv.
> * **Steg 4: Förbereda lokala VMware för Site Recovery**: Contoso förbereder konton för VM-identifiering och agentinstallation installationen ska ansluta till virtuella Azure-datorer efter redundans.
> * **Steg 5: Replikera virtuella datorer**: Om du vill konfigurera replikering Contoso konfigurera Site Recovery-miljöer för källa och mål, ställer in en replikeringsprincip och börjar replikera datorer till Azure Storage.
> * **Steg 6: Migrera databasen med hjälp av Database Migration Service**: Contoso migrerar databasen.
> * **Steg 7: Migrera de virtuella datorerna med Site Recovery**: Contoso kör ett redundanstest för att kontrollera att allt fungerar. Contoso körs sedan en fullständig redundans för att migrera de virtuella datorerna till Azure.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>Steg 1: Förbereda en SQL Database Managed Instance

Om du vill konfigurera en Azure SQL Database Managed Instance behöver Contoso ett undernät som uppfyller följande krav:

- Undernätet måste vara reserverad. Det måste vara tomt och får inte innehålla någon annan molntjänst. Undernätet får inte vara ett gateway-undernät.
- När den hanterade instansen har skapats kan Contoso bör inte lägga till resurser i undernätet.
- Undernätet kan inte ha en nätverkssäkerhetsgrupp som är kopplade till den.
- Undernätet måste ha en användardefinierad routning (UDR) routningstabell. Den enda vägen tilldelade ska vara för nästa hopp internet 0.0.0.0/0. 
- Valfri anpassad DNS: Om anpassad DNS har angetts i Azure-nätverk, måste Azures rekursiva matchare IP-adress (till exempel 168.63.129.16) läggas till i listan. Lär dig hur du [konfigurera anpassad DNS för en hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Undernätet får inte ha en slutpunkt (lagring eller SQL) som är associerade med den. Tjänstslutpunkter bör inaktiveras i det virtuella nätverket.
- Undernätet måste ha minst 16 IP-adresser. Lär dig hur du [storlek Managed Instance undernätet](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances).
- I Contosos hybridmiljö krävs anpassade DNS-inställningar. Contoso konfigurerar DNS-inställningarna om du vill använda en eller flera av företagets Azure DNS-servrar. Läs mer om [DNS-anpassning](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Konfigurera ett virtuellt nätverk för den hanterade instansen

Contoso-administratörer som konfigurerar det virtuella nätverket så här: 

1. De skapar ett nytt virtuellt nätverk (**VNET-SQLMI-EU2**) i den primära regionen östra USA 2. Den lägger till det virtuella nätverket till den **ContosoNetworkingRG** resursgrupp.
2. De tilldela ett adressutrymme för 10.235.0.0/24. De säkerställer att de inte överlappar andra nätverk i sitt företag.
3. De Lägg till två undernät i nätverket:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Det här undernätet används för att koppla en katalog till den hanterade instansen.

      ![Hanterad instans - skapa virtuellt nätverk](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

4. När det virtuella nätverk och undernät har distribuerats, peer de-nätverk på följande sätt:

    - Peer-datorer **VNET-SQLMI-EUS2** med **VNET-HUB-EUS2** (det virtuella navnätverket för USA, östra 2).
    - Peer-datorer **VNET-SQLMI-EUS2** med **VNET-PROD-EUS2** (produktionsnätverket).

      ![Nätverks-peering](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

5. They set custom DNS settings. DNS pekar först Contosos Azure-domänkontrollanter. Azure DNS är sekundär. Contoso Azure-domänkontrollanter finns på följande sätt:

    - I den **PROD-DC-EUS2** undernät i östra USA 2 produktionsnätverket (**VNET-PROD-EUS2**)
    - **CONTOSODC3** adress: 10.245.42.4
    - **CONTOSODC4** adress: 10.245.42.5
    - Azure DNS-matchning: 168.63.129.16

      ![DNS-nätverksservrarna](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*Behöver du mer hjälp?*

- Få en översikt över [SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance).
- Lär dig hur du [skapa ett virtuellt nätverk för en SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances).
- Lär dig hur du [konfigurerar peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering).
- Lär dig hur du [uppdatera inställningarna för Azure Active Directory DNS](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

### <a name="set-up-routing"></a>Konfigurera routning

Den hanterade instansen placeras i ett privat virtuellt nätverk. Contoso behöver en routningstabell för det virtuella nätverket kommunicera med Azure Management-tjänsten. Om det virtuella nätverket inte kan kommunicera med den tjänsten som hanterar den, blir det virtuella nätverket otillgänglig.

Contoso överväger dessa faktorer:

- Routningstabellen innehåller en uppsättning regler (vägar) som anger hur paket som skickas från den hanterade instansen ska dirigeras i det virtuella nätverket.
- Routningstabellen är associerade med undernät där hanterade instanser har distribuerats. Varje paket som lämnar ett undernät hanteras baserat på den associera routningstabellen.
- Ett undernät kan associeras med endast en routningstabell.
- Det finns inga ytterligare avgifter för att skapa routningstabeller i Microsoft Azure.

 Om du vill konfigurera routning Contoso göra administratörer följande:

1. De skapar en UDR (route) tabell i den **ContosoNetworkingRG** resursgrupp.

    ![Routningstabell](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Att uppfylla krav för hanterad instans, efter routningstabellen (**MIRouteTable**) har distribuerats, de lägger till en väg med adressprefixet 0.0.0.0/0. Den **nästa hopptyp** alternativet är inställt på **Internet**.

    ![Väg Tabellprefix](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. de associera routningstabellen med den **SQLMI-DB-EUS2** undernät (i den **VNET-SQLMI-EUS2** nätverk). 

    ![Dirigera tabell undernät](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*Behöver du mer hjälp?*

Lär dig hur du [konfigurera vägar för en hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

### <a name="create-a-managed-instance"></a>Skapa en hanterad instans

Contoso-administratörer kan nu etablera en SQL Database Managed Instance:

1. Eftersom den hanterade instansen har en business-app, kan de distribuera den hanterade instansen i företagets primära regionen för östra USA 2. De lägger till den hanterade instansen till den **ContosoRG** resursgrupp.
2. De välja en prisnivå nivå, storlek beräkning och lagring för instansen. Läs mer om [Managed Instance priser](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Managed Instance](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. När den hanterade instansen har distribuerats, två nya resurser visas i den **ContosoRG** resursgrupp:

    - Ett virtuellt kluster i fallet Contoso har flera hanterade instanser.
    - SQL Server Database Managed Instance. 

      ![Managed Instance](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*Behöver du mer hjälp?*

Lär dig hur du [etablera en hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-migration-service"></a>Steg 2: Förbereda Database Migration Service

För att förbereda Database Migration Service, Contoso-administratörer som behöver göra några saker:

- Registrera Database Migration Service-providern i Azure.
- Ge Database Migration Service åtkomst till Azure Storage för att ladda upp de säkerhetskopiera filerna som används för att migrera en databas. För att ge åtkomst till Azure Storage kan de skapa en Azure Blob storage-behållare. De genererar en SAS-URI för Blob storage-behållare. 
- Skapa ett projekt med Database Migration Service.

De utför sedan följande steg:

1. De registrera migrering databasprovider under prenumerationen.
    ![Database Migration Service - Register](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Skapar de ett Blob storage-behållare. Contoso genererar en SAS-URI så att Database Migration Service kan komma åt den.

    ![Database Migration Service – Generera en SAS-URI](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. De skapar en instans av Database Migration Service. 

    ![Database Migration Service – skapa instans](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. De placera Database Migration Service-instans i den **PROD-DC-EUS2** undernät för den **VNET-PROD-DC-EUS2** virtuellt nätverk.
    - Database Migration Service placeras här eftersom tjänsten måste vara i ett virtuellt nätverk som har åtkomst till en lokal SQL Server-dator via en VPN-gateway.
    - Den **VNET-PROD-EUS2** är peer-kopplas till **VNET-HUB-EUS2** och får inte Använd fjärrgateway. Den **Använd fjärrgateway** alternativet säkerställer att Database Migration Service kan kommunicera efter behov.

        ![Database Migration Service – konfigurera nätverket](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*Behöver du mer hjälp?*

- Lär dig hur du [konfigurera Database Migration Service](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Lär dig hur du [skapar och använder SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Steg 3: Förbered Azure för Site Recovery-tjänsten

Flera Azure-element krävs för Contoso att konfigurera Site Recovery för migrering av dess webbnivå VM (**WEBMV**):

- Ett virtuellt nätverk där över resurserna finns.
- Ett lagringskonto för att lagra replikerade data. 
- Ett Recovery Services-valv i Azure.

Contoso-administratörer konfigurera Site Recovery på följande sätt:

1. Eftersom den virtuella datorn är en frontwebb till SmartHotel360-appen, Contoso redundansväxlar den virtuella datorn till dess befintliga produktionsnätverket (**VNET-PROD-EUS2**) och undernät (**PROD-FE-EUS2**). Nätverk och undernät finns i den primära regionen östra USA 2. Contoso konfigurerar du nätverket när det [distribuerade Azure-infrastrukturen](contoso-migration-infrastructure.md).
2. De skapar ett lagringskonto (**contosovmsacc20180528**). Contoso använder ett allmänt konto. Contoso väljer standardlagring och replikering av lokalt redundant lagring.

    ![Site Recovery – skapa storage-konto](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Med nätverks- och storage-kontot på plats, de skapar ett valv (**ContosoMigrationVault**). Contoso placerar valv i den **ContosoFailoverRG** resursgrupp i den primära regionen östra USA 2.

    ![Skapa Recovery Services - valv](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*Behöver du mer hjälp?*

Lär dig hur du [ställa in Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Steg 4: Förbereda lokala VMware för Site Recovery

För att förbereda VMware för Site Recovery, måste Contoso-administratörer utföra dessa uppgifter:

- Förbereda ett konto på vCenter Server-instans eller vSphere ESXi-värden. Konton som automatiserar VM-identifiering.
- Förbereda ett konto som tillåter automatisk installation av Mobilitetstjänsten på virtuella VMware-datorer som Contoso vill replikera.
- Förbered lokala virtuella datorer att ansluta till virtuella Azure-datorer när de skapas efter en redundansväxling.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Identifiera automatiskt virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Contoso behöver ett konto som kan köra åtgärder som att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso-administratörer som konfigurerar kontot genom att slutföra dessa uppgifter:

1. Skapar en roll på vCenter-nivå.
2. Tilldelar behörigheterna som krävs till rollen.

*Behöver du mer hjälp?*

Lär dig hur du [skapa och tilldela en roll för automatisk identifiering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av Mobilitetstjänsten

Mobilitetstjänsten måste installeras på den virtuella datorn som Contoso vill replikera. Contoso överväger faktorerna om Mobilitetstjänsten:

- Site Recovery kan göra en automatisk push-installation av den här komponenten när Contoso aktiverar replikering för den virtuella datorn.
- För automatisk push-installation måste Contoso förbereda ett konto som Site Recovery använder för att få åtkomst till den virtuella datorn.
- Det här kontot anges när replikering har konfigurerats i Azure-konsolen.
- Contoso måste ha en domän eller lokalt konto med behörighet att installera på den virtuella datorn.

*Behöver du mer hjälp*

Lär dig hur du [skapa ett konto för push-installation av Mobilitetstjänsten](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill kunna ansluta till de replikerade virtuella datorerna i Azure efter redundansväxlingen till Azure. Om du vill ansluta till de replikerade virtuella datorerna i Azure, måste Contoso-administratörer utföra några åtgärder på den lokala virtuella datorn före migreringen: 

1. För åtkomst via internet aktiverar de du RDP på den lokala virtuella datorn före redundans. De säkerställer att TCP och UDP-regler lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler.
2. För åtkomst via Contosos plats-till-plats-VPN aktiverar de du RDP på den lokala datorn. De tillåter RDP i **Windows-brandväggen** > **tillåtna appar och funktioner** för **domän och privat** nätverk.
3. De ange operativsystemets SAN-princip på den lokala virtuella datorn till **OnlineAll**.

Contoso-administratörer måste också kontrollera de här objekten när de kör en redundans:

- Det bör finnas inga Windows-uppdateringar som väntar på den virtuella datorn när en redundansväxling har utlösts. Om Windows-uppdateringar är väntande användare Contoso kan inte logga in till den virtuella datorn förrän uppdateringen är klar.
- Efter redundansväxlingen administratörer bör kontrollera **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om de inte kan visa startdiagnostiken kan de bör kontrollera att den virtuella datorn körs, och granska sedan [felsökningstips](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure"></a>Steg 5: Replikera lokala virtuella datorer till Azure

Innan du kör en migrering till Azure måste Contoso-administratörer att konfigurera och aktivera replikering för den lokala virtuella datorn.

### <a name="set-a-replication-goal"></a>Ange ett replikeringsmål

1. I valvet under valvnamnet (**ContosoVMVault**), de har ett replikeringsmål (**komma igång** > **Site Recovery**  >   **Förbereda infrastrukturen**).
2. De ange som finns lokalt på datorerna, att de är virtuella VMware-datorer, replikeras till Azure.

    ![Förbereda infrastrukturen - skyddsmål](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Om du vill fortsätta, bekräfta Contoso-administratörer att de har slutfört distributionsplanering. De väljer **Ja, jag har gjort det**. Contoso håller på att migrera endast en enskild virtuell dator, distributionsplanering behövs inte i den här distributionen.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Nu kan konfigurera Contoso-administratörer källmiljön. Om du vill konfigurera dess källmiljö de ladda ned en OVF-mall och använda den för att distribuera konfigurationsservern och dess associerade komponenter som en högtillgänglig, en lokal VMware VM. Komponenterna på servern inkluderar:

- Konfigurationsservern som samordnar kommunikationen mellan den lokala infrastrukturen och Azure. Konfigurationsservern hanterar datareplikering.
- Processervern som fungerar som en replikeringsgateway. Processerver:
    - Tar emot replikeringsdata.
    - Optimerar replikering datum genom att använda cachelagring, komprimering och kryptering.
    - Skickar datum för replikering till Azure Storage.
- Processervern installerar också Mobilitetstjänsten på de virtuella datorerna som ska replikeras. Processervern utför automatisk identifiering av lokala virtuella VMware-datorer.
- När du konfigurationsservern VM skapas och startas, registrerar Contoso servern i valvet.

Om du vill konfigurera källan gör Contoso miljöadministratörer du följande:

1. De ladda ner OVF-mall från Azure-portalen (**förbereda infrastrukturen** > **källa** > **konfigurationsservern**).
    
    ![Lägg till en konfigurationsserver](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. De importerar mallen till VMware för att skapa och distribuera den virtuella datorn.

    ![Distribuera OVF-mall](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  När de aktiverar den virtuella datorn för första gången, startar i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och anger ett administratörslösenord.
4. När installationen är klar, loggar de in på den virtuella datorn som administratör. Vid första gången inloggning körs Azure Site Recovery-konfigurationsverktyget automatiskt.
5. De kan ange ett namn du använder för att registrera konfigurationsservern i valvet i konfigurationsverktyget för Site Recovery.
6. Verktyget kontrollerar den Virtuella datorns anslutning till Azure. När anslutningen har upprättats kan de välja **logga in** att logga in på Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv som konfigurationsservern är registrerad. 

    ![Registrera konfigurationsservern](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn. De loggar in på datorn igen. I guiden Konfigurera serverhantering startar automatiskt.
8. I guiden Välj de nätverkskort för att ta emot replikeringstrafiken. Den här inställningen kan inte ändras när den har konfigurerats.
9. De Välj prenumeration, resursgrupp och Recovery Services-valv där du vill registrera konfigurationsservern.

    ![Välj Recovery Services-valv](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. De ladda ned och installerar MySQL-Server och VMWare PowerCLI. Sedan kan de verifierar serverinställningarna.
11. Efter valideringen kan ange de FQDN eller IP-adress för vCenter Server-instans eller vSphere-värden. De lämnar standardporten och ange ett namn för vCenter Server-instans i Azure.
12. De ange det konto som skapades tidigare så att Site Recovery kan automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. 
13. De ange autentiseringsuppgifter, så att Mobilitetstjänsten installeras automatiskt när replikering har aktiverats. Kontot måste ha lokal administratörsbehörighet på de virtuella datorerna för Windows-datorer. 

    ![Konfigurera vCenter Server](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. När registreringen är klar visas i Azure-portalen kan de Kontrollera igen att konfigurationsservern och VMware-servern visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
8. Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Nu kan konfigurera Contoso administratörer målmiljön för replikeringen:

1. I **Förbered infrastruktur** > **Target**, de selecs målinställningarna.
2. Site Recovery kontrollerar att det är ett lagringskonto och nätverk i det angivna målet.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

När källan och målet har ställts in, Contoso administratörer skapa en replikeringsprincip och associerar principen med konfigurationsservern:

1. I **Förbered infrastruktur** > **replikeringsinställningar** > **replikeringsprincip** >  **skapa och Associera**, de skapar den **ContosoMigrationPolicy** princip.
2. De använder standardinställningarna:
    - **Tröskelvärde för Replikeringspunktmål**: Standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållning av återställningspunkt**: Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperioden är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Appkonsekvent ögonblicksbildsfrekvens**: Standardvärdet 1 timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.
 
    ![Replikeringsprincip – skapa](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. Principen associeras automatiskt med konfigurationsservern. 

    ![Replikeringsprincip – koppla](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*Behöver du mer hjälp?*

- Du kan läsa en genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurera replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Aktivera replikering

Contoso-administratörer kan nu börja replikera WebVM.

1. I **replikera program** > **källa** > **replikera**, de Välj inställningar för datakälla.
2. De anger att de vill aktivera virtuella datorer, Välj vCenter Server-instansen och ange konfigurationsservern.

    ![Aktivera replikering - källa](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. De ange Målinställningar, inklusive resursgruppen och nätverk där den virtuella Azure-datorn kommer att finnas efter redundansväxling. De ange konto för replikerade data ska lagras.

    ![Aktivera replikering - mål](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. De väljer **WebVM** för replikering. Site Recovery installerar Mobilitetstjänsten på varje virtuell dator när replikering har aktiverats. 

    ![Aktivera replikering – Välj den virtuella datorn](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. De kontrollera att rätt replikeringsprincip har valts och aktivera replikering för **WEBVM**. De tracs Replikeringsförlopp på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.
6. I **Essentials** i Azure-portalen kan de se status för de virtuella datorer som replikeras till Azure:

    ![Infrastruktur-vy](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*Behöver du mer hjälp?*

Du kan läsa en genomgång av de här stegen i [Aktivera replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database"></a>Steg 6: Migrera databasen 

Contoso-administratörer måste du skapa ett projekt med Database Migration Service och migrera sedan databasen.

### <a name="create-a-database-migration-service-project"></a>Skapa ett projekt med Database Migration Service

1. Skapar de ett Database Migration Service-projekt. De väljer den **SQL Server** , typ av källserver och **Azure SQL Database Managed Instance** som mål.

     ![Database Migration Service – nytt migreringsprojekt](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Guiden öppnas.

### <a name="migrate-the-database"></a>Migrera databasen 

1. I guiden anger de den Virtuella där den lokala databasen finns. De kommer in autentiseringsuppgifter för att få åtkomst till databasen.

    ![Database Migration Service - källinformation](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. de väljer att migrera databasen (**SmartHotel.Registration**):

    ![Database Migration Service – Välj källdatabaser](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. De kommer in namnet på den hanterade instansen i Azure och autentiseringsuppgifterna för målet.

    ![Database Migration Service - målinformation](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. I **ny aktivitet** > **kör migreringen**, de anger inställningar för att köra migreringen:
    - Autentiseringsuppgifter för källa och mål.
    - Databasen som ska migreras.
    - Nätverksresurs på en lokal virtuell dator. Database Migration Service kommer källa säkerhetskopior till den här resursen. 
        - Kontot som kör SQL Server-källinstansen måste ha skrivbehörighet på den här resursen.
        - FQDN-sökvägen till resursen måste användas.
    - SAS-URI som ger tillgång till den lagringskontobehållare som tjänsten Överför säkerhetskopieringsfilerna för migrering Database Migration Service.

        ![Database Migration Service – konfigurera migreringsinställningar för](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. De spara migreringsinställningarna för och kör sedan migreringen.
6. I **översikt**, de monitos Migreringsstatus för.

    ![Database Migration Service - Övervakare](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. De kontrollerar att måldatabaserna finns på den hanterade instansen när migreringen är klar.

    ![Database Migration Service – verifiera Databasmigrering](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm"></a>Steg 7: Migrera den virtuella datorn

Contoso-administratörer som kör ett snabbt redundanstest och sedan migrera den virtuella datorn.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Innan du migrerar WEBVM, ett redundanstest hjälper till att säkerställa att allt fungerar som förväntat. Administratörer utför följande steg:

1. Köra ett redundanstest till den senaste tillgängliga tidpunkt (**senaste bearbetade**).
2. De väljer **Stäng datorn innan du påbörjar redundans**. Med det här alternativet kan försöker Site Recovery stänga av den Virtuella källdatorn innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 
    1. En kravkontroll körs för att se till att alla villkor som krävs för migrering är uppfyllda.
    2. Redundansen bearbetar data så att du kan skapa en virtuell Azure-dator. Om den senaste återställningspunkten är vald, skapas en återställningspunkt från data.
    3.  Azure VM skapas med hjälp av de data som behandlas i föregående steg.
3. När redundansväxlingen är klar visas repliken virtuell Azure-dator i Azure-portalen. de kontrollerar att allt fungerar korrekt: den virtuella datorn har rätt storlek, den är ansluten till rätt nätverk och den körs. 
4. När du har verifierat redundanstestningen de Rensa växling vid fel och registrera eventuella observationer. 

### <a name="migrate-the-vm"></a>Migrera den virtuella datorn

1. Efter att ha kontrollerat att redundanstestningen fungerar som förväntat, Contoso administratörer skapa en återställningsplan för migrering och WEBVM läggas till i planen:

     ![Skapa en återställningsplan - Välj objekt](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. De kör en redundansväxling med det alternativet är att välja den senaste återställningspunkten. De anger att Site Recovery bör försöker stänga av den lokala virtuella datorn innan du utlöser redundansväxlingen.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Efter redundansen kan kontrollera de att den virtuella Azure-datorn visas som förväntat i Azure-portalen.

   ![Information om återställning av plan](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. När du har verifierat slutföra de migreringen du slutför migreringsprocessen, stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-faktureringen för den virtuella datorn.

    ![Redundans - fullständig migrering](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Uppdatera anslutningssträngen

Som det sista steget i migreringsprocessen uppdatera Contoso administratörer anslutningssträngen för programmet som ska peka på den migrerade databasen som körs på Contosos Managed Instance.

1. I Azure-portalen kan de hitta anslutningssträngen genom att välja **inställningar** > **anslutningssträngar**.

    ![Anslutningssträngar](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. De uppdatera strängen med användarnamn och lösenord för SQL Database Managed Instance.
3. När strängen har konfigurerats kan ersätta de den aktuella anslutningssträngen i web.config-filen av programmet.
4. När du uppdaterar filen och spara den, de startas om IIS på WEBVM genom att köra `IISRESET /RESTART` i Kommandotolkens fönster.
5. När IIS startas använder appen den databas som körs på SQL Database Managed Instance.
6. Då kan de stänga av den lokala SQLVM-datorn. Migreringen har slutförts.

*Behöver du mer hjälp?*

- Lär dig hur du [kör ett redundanstest](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Lär dig hur du [skapa en återställningsplan](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Lär dig hur du [redundansväxlar till Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

SmartHotel360-appen körs på en virtuell Azure-dator med migreringen har slutförts, och SmartHotel360-databasen är tillgänglig i Azure SQL Database Managed Instance.  

Nu kan behöver Contoso göra följande rensning:  

- Ta bort den WEBVM datorn från vCenter Server-lagret.
- Ta bort den SQLVM datorn från vCenter Server-lagret.
- Ta bort WEBVM och SQLVM från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentation för att visa den nya platsen och IP-adress för WEBVM.
- Ta bort SQLVM från interna dokumentation. Du kan också ändra Contoso i dokumentationen om du vill visa SQLVM tagits bort och inte längre i den virtuella datorn inventering.
- Granska eventuella resurser som interagerar med de inaktiverade virtuella datorerna. Uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.

## <a name="review-the-deployment"></a>Granska distributionen

Med de migrerade resurserna i Azure måste Contoso fullständigt operationalisera och skydda den nya infrastrukturen.

### <a name="security"></a>Säkerhet

Contoso security-teamet granskar virtuella datorer i Azure och SQL Database Managed Instance för att söka efter eventuella säkerhetsproblem med dess implementering:

- Teamet granskar nätverkssäkerhetsgrupper som används för att styra åtkomsten för den virtuella datorn. Nätverkssäkerhet grupper säkerställer att endast trafik som tillåts i appen kan skicka.
- Contosos överväger security också skydda data på disken med hjälp av Azure Disk Encryption och Azure Key Vault.
- Teamet gör det möjligt för identifiering av hot på den hanterade instansen. Hotidentifiering skickar en avisering till Contosos team/service desk säkerhetssystem att öppna en biljett om ett hot har identifierats. Läs mer om [hotidentifiering för hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Hanterad instans-security - hotidentifiering](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

Läs mer om säkerhetsrutiner för virtuella datorer i [säkerhetsmetodtips för IaaS-arbetsbelastningar i Azure](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control).

### <a name="bcdr"></a>BCDR

Contoso utförs följande åtgärder för affärskontinuitet och haveriberedskap (BCDR):

- Skydda data: Contoso säkerhetskopierar data på de virtuella datorerna med Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- Håll appar igång: Contoso replikerar appen virtuella datorer i Azure till en sekundär region med hjälp av Site Recovery. [Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).
- Contoso lär sig mer om att hantera SQL-hanterad instans, inklusive [säkerhetskopior](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).


### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

- Contoso har en befintlig Licensiering för WEBVM. Om du vill dra nytta av priser med Azure Hybrid-förmånen, konverterar Contoso befintlig Azure-VM.
- Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Cost Management är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper till att Contoso använda och hantera Azure och andra molnresurser. Läs mer om [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Sammanfattning

I den här artikeln Contoso namnkonflikt SmartHotel360-app i Azure genom att migrera appen klientdelens VM till Azure med hjälp av Site Recovery-tjänsten. Contoso migrerar en lokal databas till en Azure SQL Database Managed Instance med Azure Database Migration Service.

## <a name="next-steps"></a>Nästa steg

I nästa artikel i serien, Contoso [namnkonflikt SmartHotel360-app på Azure Virtual Machines](contoso-migration-rehost-vm.md) med hjälp av Azure Site Recovery-tjänsten.

