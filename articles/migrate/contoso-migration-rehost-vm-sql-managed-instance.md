---
title: Ange ny värd för en lokal Contoso-app genom att migrera till Azure virtuella datorer och Azure SQL Database Managed Instance | Microsoft Docs
description: Lär dig hur Contoso namnkonflikt en lokal app på Azure Virtual Machines och genom att använda Azure SQL Database Managed Instance.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 3e3f8dffbaa7109423aacdbfbaa658bada8bb84a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215347"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Contoso-migrering: byta Appvärd ett lokalt på en virtuell dator i Azure och SQL Database Managed Instance

I den här artikeln Contoso migrerar dess SmartHotel app klientdelens VM till en Azure virtuell dator med hjälp av Azure Site Recovery-tjänsten. Contoso migrerar också app-databas till Azure SQL Database Managed Instance.

> [!NOTE]
> Azure SQL Database Managed Instance finns för närvarande i förhandsversion.

Den här artikeln är i en serie av artiklar som dokumenterar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och ett antal scenarier som illustrerar hur du konfigurerar en infrastruktur för migreringen och kör olika typer av migreringar. Scenarier växer i komplexitet. Artiklar läggs till seriens över tid.


Artikel | Information | Status
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över Contosos migreringsstrategi, artikelserien och exempelappar som används i serien. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Contoso förbereder den lokala infrastrukturen och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla migreringsartiklar om i serien. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser för migrering till Azure](contoso-migration-assessment.md) | Contoso kör en utvärdering av dess lokal tvålagers-SmartHotel-app som körs på VMware. Contoso utvärderar app virtuella datorer med hjälp av den [Azure Migrate](migrate-overview.md) service. Contoso utvärderar app SQL Server-databas med hjälp av [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
Artikel 4: Ange ny värd för en app på en virtuell Azure-dator och SQL Database Managed Instance | Contoso körs en lift and shift-migrering till Azure för dess lokal SmartHotel app. Contoso migrerar appen klientdelens VM med hjälp av [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migrerar app-databasen till en Azure SQL Database Managed Instance med hjälp av den [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Den här artikeln
[Artikel 5: Ange ny värd för en app på virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Contoso migrerar dess SmartHotel app virtuella datorer till virtuella Azure-datorer med hjälp av Site Recovery-tjänsten. | Tillgängligt
[Artikel 6: Ange ny värd för en app på virtuella Azure-datorer och i en SQL Server AlwaysOn-tillgänglighetsgrupp](contoso-migration-rehost-vm-sql-ag.md) | Contoso migrerar SmartHotel appen. Contoso använder Site Recovery för att migrera de virtuella datorerna för appen. Database Migration Service används för att migrera app-databas till SQL Server-kluster som skyddas av en AlwaysOn-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Contoso Slutför en lift and shift-migrering av dess Linux osTicket app på virtuella Azure-datorer med Site Recovery. | Tillgängligt
[Artikel 8: Byta Appvärd en Linux på Azure virtuella datorer och Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migrerar dess osTicket Linux-app till virtuella Azure-datorer med hjälp av Site Recovery. Den migrerar app-databas till Azure Database för MySQL med MySQL Workbench. | Tillgängligt
[Artikel 9: Omstrukturera en app i en Azure webbapp och Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migrerar dess SmartHotel-app till en Azure webbapp och migrerar app-databasen till en Azure SQL Server-instans. | Tillgängligt
[Artikel 10: Omstrukturera en Linux-app i en Azure-webbapp och en Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migrerar dess osTicket Linux-app till en Azure-webbapp på flera platser. Webbappen är integrerad med GitHub för kontinuerlig leverans. Contoso migrerar app-databasen till en Azure Database for MySQL-instans. | Tillgängligt
[Artikel 11: Omstrukturera Team Foundation Server i Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Contoso migrerar sin lokala Team Foundation Server-distributionen genom att migrera den till Visual Studio Team Services i Azure. | Tillgängligt
[Artikel 12: Omforma en app i Azure-behållare och Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migrerar dess SmartHotel-app till Azure och sedan rearchitects appen. Contoso rearchitects webbnivån appen som en Windows-behållare och rearchitects app-databasen med hjälp av Azure SQL Database. | Tillgängligt
[Artikel 13: Återskapa en app i Azure](contoso-migration-rebuild.md) | Contoso återskapas dess SmartHotel-app med en mängd Azure-funktioner och tjänster, inklusive Azure App Service, Azure Kubernetes Service, Azure Functions, Azure Cognitive Services och Azure Cosmos DB. | Tillgängligt

Du kan ladda ned SmartHotel exempelappen som används i den här artikeln från [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Lokala arkitektur


Det här diagrammet visar den aktuella Contoso lokal infrastrukturen:

![Aktuell Contoso-arkitektur](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso har en huvuddatacentret. Datacentret finns i den stad i New York i östra USA.
- Contoso har tre ytterligare lokala grenar i USA.
- Huvuddatacentret är ansluten till internet med en fiber appar i Metro-Ethernet-anslutning (500 Mbit/s).
- Varje gren är lokalt ansluten till internet med hjälp av företagsklass anslutningar med IPsec VPN-tunnlar till huvuddatacentret. Installationsprogrammet tillåter Contosos hela nätverket ska anslutas permanent och optimerar Internetanslutning.
- Huvuddatacentret är fullständigt virtualiserat med VMware. Contoso har två ESXi 6.5-virtualiseringsvärdar som hanteras av vCenter Server 6.5.
- Contoso använder Active Directory för Identitetshantering. Contoso använder DNS-servrar i det interna nätverket.
- Domänkontrollanterna i datacentret körs på virtuella VMware-datorer. Domänkontrollanter på lokala grenar som körs på fysiska servrar.

## <a name="business-drivers"></a>Affärsdrivande faktorer

Contosos IT-ledning har arbetat tillsammans med företagets affärspartners att förstå vad företaget vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer. Därför ökat trycket på företagets lokala system och infrastruktur.
- **Öka effektiviteten**: Contoso behöver du ta bort onödiga procedurer och att effektivisera processer för utvecklare och användare. Affärsbehov IT är fast och som inte spill tid eller pengar, så företaget kan leverera snabbare på kunders behov.
- **Förbättrad flexibilitet**: Contoso IT måste vara mer responsiva gentemot behoven i verksamheten. Måste kunna reagera snabbare än de ändringar som sker i marketplace för företag för att utföra en global ekonomi. IT på Contoso måste inte vara i vägen eller bli en blockerare för företag.
- **Skala**: enligt företagets verksamhet växer har Contoso IT måste tillhandahålla system som kan växa i samma takt.

## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har identifierat mål för den här migreringen. Företaget använder migreringen mål för att avgöra den bästa migreringsmetoden.

- Efter migreringen bör app i Azure ha samma prestandafunktioner som appen har idag i Contosos lokala VMWare-miljön. Flytta till molnet innebär inte att prestanda är mindre viktigt.
- Contoso vill inte investera i appen. Appen är kritiska och viktiga för verksamheten, men Contoso helt enkelt vill flytta appen i sin nuvarande form till molnet.
- Databasadministrationsuppgifter värden ska minimeras, efter att appen har migrerats.
- Contoso vill inte använda en Azure SQL-databas för den här appen. Är ute efter alternativ.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

I det här scenariot:

- Contoso vill migrera dess tvålagers-lokal reseapp.
- Appen är nivåindelad över två virtuella datorer (**WEBVM** och **SQLVM**) och som finns på en version 6.5 VMware ESXi-värd (**contosohost1.contoso.com**). 
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**) som körs på en virtuell dator.
- Contoso migrerar app-databasen (**SmartHotelDB**) till en Azure SQL Database Managed Instance.
- Contoso migrerar lokala VMware-datorer till en Azure-dator.
- Contoso har ett lokalt datacenter (**contoso-datacenter**) och en lokal domänkontrollant (**contosodc1**).
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.

![Scenariots arkitektur](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Azure-tjänster

Tjänst | Beskrivning | Kostnad
--- | --- | ---
[Database Management-tjänsten](https://docs.microsoft.com/azure/dms/dms-overview) | Database Management-tjänsten gör det möjligt för sömlös migrering från flera databaskällor till Azure-Dataplattformar med minimal avbrottstid. | Lär dig mer om [regioner som stöds](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) och [Database Management-tjänsten priser](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance är en hanterad databastjänst som representerar en fullständigt hanterad SQL Server-instans i Azure-molnet. Den använder samma kod som den senaste versionen av SQL Server Database Engine och har de senaste funktionerna, prestandaförbättringar och säkerhetsuppdateringar. | Med hjälp av en SQL Database Managed Instance som körs i Azure debiteras avgifter baserat på kapacitet. Läs mer om [Managed Instance priser](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten Site Recovery dirigerar och hanterar migrering och haveriberedskap för virtuella datorer i Azure och lokala datorer och fysiska servrar.  | Azure Storage-avgifter tillkommer under replikering till Azure.  Virtuella Azure-datorer skapas och betalar avgifter vid redundans. Läs mer om [Site Recovery-avgifter och priser](https://azure.microsoft.com/pricing/details/site-recovery/).

 ## <a name="migration-process"></a>Migreringsprocessen

Contoso migrera webb- och data nivåer av dess SmartHotel-app till Azure genom att följa dessa steg:

1. Contoso har redan Azure-infrastruktur på plats, så den behöver bara lägga till några specifika Azure-komponenter för det här scenariot.
2. Datanivån kommer att migreras med hjälp av Data Migration Service. Data Migration Service ansluter till en lokal SQL Server-dator via en plats-till-plats VPN-anslutning mellan Contoso-datacenter och Azure. Data Migration Service migrerar sedan databasen.
3. Webbnivån kommer att migreras med hjälp av en lift and shift-migrering med hjälp av Site Recovery. Signeras förbereda lokala VMware-miljö, konfigurerar och aktiverar replikering och migrerar de virtuella datorerna genom att föra över dem till Azure.

     ![Migreringsarkitektur för](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

## <a name="prerequisites"></a>Förutsättningar

Contoso och andra användare måste uppfylla följande krav för det här scenariot:

Krav | Information
--- | ---
**Registrera dig i förhandsversionen av Managed Instance** | Du måste vara registrerade i den begränsade offentliga förhandsversionen för SQL Database Managed Instance. Du behöver en Azure-prenumeration till [registrera](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Registreringen kan ta ett par dagar att slutföra, så se till att registrera dig innan du börjar distribuera det här scenariot.
**Azure-prenumeration** | Du bör redan har skapat en prenumeration när du utför utvärderingen i den första artikeln i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör för prenumerationen, måste du be administratören tilldela dig ägar-eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan se [använda rollbaserad åtkomstkontroll för att hantera Site Recovery åtkomst](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site Recovery (lokalt)** | Din lokala vCenter Server-instansen bör köra version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värden.<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Stöds [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [storage](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguration.
**Database Management-tjänsten** | För den Database Management-tjänsten behöver du en [kompatibla lokala VPN-enheten](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Du måste kunna konfigurera den lokala VPN-enhet. Den måste ha en offentlig IPv4-adress för utåtriktade. Adressen får inte finnas bakom en NAT-enhet.<br/><br/> Kontrollera att du har åtkomst till din lokala SQL Server-databas.<br/><br/> Windows-brandväggen ska kunna komma åt käll-databasmotorn. Lär dig hur du [konfigurerar Windows-brandväggen för access Database Engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Om det finns en brandvägg framför din databasdator, lägger du till regler för att tillåta åtkomst till databasen och filer via SMB-port 445.<br/><br/> Autentiseringsuppgifterna som används för att ansluta till SQL Server-källinstansen och som mål Managed Instance måste vara medlemmar i rollen sysadmin.<br/><br/> Du behöver ett nätverk delar i din lokala databas som Database Management-tjänsten kan använda för att säkerhetskopiera källdatabasen.<br/><br/> Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörigheter på nätverksresursen.<br/><br/> Anteckna en Windows-användare och lösenord som har fullständig behörighet på nätverksresursen. Database Management Service personifierar dessa autentiseringsuppgifter för att ladda upp filer för säkerhetskopiering till Azure Storage-behållare.<br/><br/> SQL Server Express-installationen anger TCP/IP-protokollet till **inaktiverad** som standard. Kontrollera att den är aktiverad.

## <a name="scenario-steps"></a>Scenarioanvisningar

Här är hur Contoso planerar att konfigurera distributionen:

> [!div class="checklist"]
> * **Steg 1: Konfigurera en SQL Database Managed Instance**: Contoso behöver en förskapad hanterad instans som en lokal SQL Server-databasen ska migreras.
> * **Steg 2: Förbereda Database Management-tjänsten**: Contoso måste registrera databasprovider för migrering, skapa en instans och sedan skapa ett projekt för Database Management-tjänsten. Contoso måste ställa in en signatur för delad åtkomst (SAS) identifierare URI (Uniform Resource) för Database Management-tjänsten. En SAS-URI ger delegerad åtkomst till resurser i Contosos storage-konto, så Contoso kan ge begränsade behörigheter till storage-objekt. Contoso ställer in en SAS-URI så Database Management-tjänsten kan komma åt den lagringskontobehållare som tjänsten Överför säkerhetskopieringsfilerna för SQL Server.
> * **Steg 3: Förbereda Azure Site Recovery**: Contoso måste skapa ett lagringskonto för att lagra replikerade data för Site Recovery. Det måste också skapa ett Azure Recovery Services-valv.
> * **Steg 4: Förbereda lokala VMware för Site Recovery**: Contoso förbereder konton för VM-identifiering och agentinstallation installationen ska ansluta till virtuella Azure-datorer efter redundans.
> * **Steg 5: Replikera datorer**: Om du vill konfigurera replikering Contoso konfigurera Site Recovery-miljöer för källa och mål, ställer in en replikeringsprincip och börjar replikera datorer till Azure Storage.
> * **Steg 6: Migrera databasen med hjälp av hanteringstjänsten databasen**: Contoso migrerar databasen.
> * **Steg 7: Migrera de virtuella datorerna med Site Recovery**: Contoso kör ett redundanstest för att kontrollera att allt fungerar. Contoso körs sedan en fullständig redundans för att migrera de virtuella datorerna till Azure.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>Steg 1: Förbered en SQL Database Managed Instance

Om du vill konfigurera en Azure SQL Database Managed Instance behöver Contoso ett undernät som uppfyller följande krav:

- Undernätet måste vara reserverad. Det måste vara tomt och får inte innehålla någon annan molntjänst. Undernätet får inte vara ett gateway-undernät.
- När den hanterade instansen har skapats kan Contoso bör inte lägga till resurser i undernätet.
- Undernätet kan inte ha en nätverkssäkerhetsgrupp som är kopplade till den.
- Undernätet måste ha en användardefinierad routning (UDR) routningstabell. Den enda vägen tilldelade ska vara för nästa hopp internet 0.0.0.0/0. 
- Valfri anpassad DNS: om anpassad DNS har angetts i Azure-nätverk, Azures rekursiva matchare IP-adress (till exempel 168.63.129.16) måste läggas till i listan. Lär dig hur du [konfigurera anpassad DNS för en hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Undernätet får inte ha en slutpunkt (lagring eller SQL) som är associerade med den. Tjänstslutpunkter bör inaktiveras i det virtuella nätverket.
- Undernätet måste ha minst 16 IP-adresser. Lär dig hur du [storlek Managed Instance undernätet](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances).
- I Contosos hybridmiljö krävs anpassade DNS-inställningar. Contoso konfigurerar DNS-inställningarna om du vill använda en eller flera av företagets Azure DNS-servrar. Läs mer om [DNS-anpassning](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Konfigurera ett virtuellt nätverk för den hanterade instansen

Contoso ställer in det virtuella nätverket på följande sätt: 

1. Contoso skapar ett nytt virtuellt nätverk (**VNET-SQLMI-EU2**) i den primära regionen östra USA 2. Den lägger till det virtuella nätverket till den **ContosoNetworkingRG** resursgrupp.
2. Contoso tilldelar ett adressutrymme för 10.235.0.0/24. Contoso säkerställer att de inte överlappar andra nätverk i sitt företag.
2. Contoso lägger till två undernät i nätverket:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Det här undernätet används för att koppla en katalog till den hanterade instansen.

    ![Hanterad instans - skapa virtuellt nätverk](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. När det virtuella nätverk och undernät har distribuerats, Contoso-peerenheter nätverk på följande sätt:

    - Peer-datorer **VNET-SQLMI-EUS2** med **VNET-HUB-EUS2** (det virtuella navnätverket för USA, östra 2).
    - Peer-datorer **VNET-SQLMI-EUS2** med **VNET-PROD-EUS2** (produktionsnätverket).

    ![Nätverks-peering](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso anger anpassade DNS-inställningar. DNS pekar först Contosos Azure-domänkontrollanter. Azure DNS är sekundär. Contoso Azure-domänkontrollanter finns på följande sätt:

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

 Du ställer in Routning:

1. Contoso skapar en UDR-tabell. Contoso skapar routningstabellen i den **ContosoNetworkingRG** resursgrupp.

    ![Routningstabell](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Att uppfylla krav för hanterad instans, efter routningstabellen (**MIRouteTable**) har distribuerats, Contoso lägger till en väg med adressprefixet 0.0.0.0/0. Den **nästa hopptyp** alternativet är inställt på **Internet**.

    ![Väg Tabellprefix](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso associerar routningstabellen med den **SQLMI-DB-EUS2** undernät (i den **VNET-SQLMI-EUS2** nätverk). 

    ![Dirigera tabell undernät](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*Behöver du mer hjälp?*

Lär dig hur du [konfigurera vägar för en hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route).

### <a name="create-a-managed-instance"></a>Skapa en hanterad instans

Contoso kan nu etablera en SQL Database Managed Instance:

1. Eftersom den hanterade instansen har en business-app, distribuerar Contoso den hanterade instansen i företagets primära regionen för östra USA 2. Contoso lägger till den hanterade instansen till den **ContosoRG** resursgrupp.
2. Contoso väljer en prisnivå nivå, storlek beräkning och lagring för instansen. Läs mer om [Managed Instance priser](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Managed Instance](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. När den hanterade instansen har distribuerats, två nya resurser visas i den **ContosoRG** resursgrupp:

    - Ett virtuellt kluster i fallet Contoso har flera hanterade instanser.
    - SQL Server Database Managed Instance. 

    ![Managed Instance](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*Behöver du mer hjälp?*

Lär dig hur du [etablera en hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-management-service"></a>Steg 2: Förbereda Database Management-tjänsten

För att förbereda Database Management-tjänsten, behöver Contoso göra några saker:

- Registrera providern Database Management-tjänsten i Azure.
- Ange Database Management-tjänsten med åtkomst till Azure Storage för att ladda upp de säkerhetskopiera filerna som används för att migrera en databas. För att ge åtkomst till Azure Storage, skapar en Azure Blob storage-behållare i Contoso. Contoso genererar en SAS-URI för Blob storage-behållare. 
- Skapa ett projekt för Database Management-tjänsten.

Sedan utföra Contoso följande steg:

1. Contoso registrerar migrering databasprovider under prenumerationen.
    ![Database Management-tjänsten – registrera dig](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Contoso skapar ett Blob storage-behållare. Contoso genererar en SAS-URI så att databasen Management-tjänsten kan komma åt den.

    ![Databasen Management-tjänsten – Generera en SAS-URI](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Contoso skapar en instans av Database Management-tjänsten. 

    ![Databasen Management-tjänsten – skapa instans](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso placerar Database Management Service-instans i den **PROD-DC-EUS2** undernät för den **VNET-PROD-DC-EUS2** virtuellt nätverk.
    - Contoso placerar det Database Management-tjänsten eftersom tjänsten måste vara i ett virtuellt nätverk som har åtkomst till en lokal SQL Server-dator via en VPN-gateway.
    - Den **VNET-PROD-EUS2** är peer-kopplas till **VNET-HUB-EUS2** och får inte Använd fjärrgateway. Den **Använd fjärrgateway** alternativet säkerställer att Database Management-tjänsten kan kommunicera efter behov.

        ![Databasen Management-tjänsten – konfigurera nätverket](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*Behöver du mer hjälp?*

- Lär dig hur du [konfigurera Database Management-tjänsten](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Lär dig hur du [skapar och använder SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Steg 3: Förbereda Azure för Site Recovery-tjänsten

Flera Azure-element krävs för Contoso att konfigurera Site Recovery för migrering av dess webbnivå VM (**WEBMV**):

- Ett virtuellt nätverk där över resurserna finns.
- Ett lagringskonto för att lagra replikerade data. 
- Ett Recovery Services-valv i Azure.

Contoso ställer in Site Recovery på följande sätt:

1. Eftersom den virtuella datorn är en frontwebb till appen SmartHotel, Contoso redundansväxlar den virtuella datorn till dess befintliga produktionsnätverket (**VNET-PROD-EUS2**) och undernät (**PROD-FE-EUS2**). Nätverk och undernät finns i den primära regionen östra USA 2. Contoso konfigurerar du nätverket när det [distribuerade Azure-infrastrukturen](contoso-migration-infrastructure.md).
2. Contoso skapas ett lagringskonto (**contosovmsacc20180528**). Contoso använder ett allmänt konto. Contoso väljer standardlagring och replikering av lokalt redundant lagring.

    ![Site Recovery – skapa storage-konto](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Med nätverks- och storage-kontot på plats, Contoso skapar ett valv (**ContosoMigrationVault**). Contoso placerar valv i den **ContosoFailoverRG** resursgrupp i den primära regionen östra USA 2.

    ![Skapa Recovery Services - valv](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*Behöver du mer hjälp?*

Lär dig hur du [ställa in Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Steg 4: Förbereda lokala VMware för Site Recovery

För att förbereda VMware för Site Recovery, måste Contoso utföra dessa uppgifter:

- Förbereda ett konto på vCenter Server-instans eller vSphere ESXi-värden. Konton som automatiserar VM-identifiering.
- Förbereda ett konto som tillåter automatisk installation av Mobilitetstjänsten på virtuella VMware-datorer som Contoso vill replikera.
- Förbered lokala virtuella datorer att ansluta till virtuella Azure-datorer när de skapas efter en redundansväxling.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Identifiera automatiskt virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Contoso behöver ett konto som kan köra åtgärder som att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso ställer in kontot genom att slutföra dessa uppgifter:

1. Skapar en roll på vCenter-nivå.
2. Tilldelar behörigheterna som krävs till rollen.

*Behöver du mer hjälp?*

Lär dig hur du [skapa och tilldela en roll för automatisk identifiering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av Mobilitetstjänsten

Mobilitetstjänsten måste installeras på den virtuella datorn som Contoso vill replikera. Contoso överväger faktorerna om Mobilitetstjänsten:

- Site Recovery kan göra en automatisk push-installation av den här komponenten när Contoso aktiverar replikering för den virtuella datorn.
- För automatisk push-installation måste Contoso förbereda ett konto som Site Recovery använder för att få åtkomst till den virtuella datorn.
- Contoso anger det här kontot när verktyget konfigurerar replikering i Azure-konsolen.
- Contoso måste ha en domän eller lokalt konto med behörighet att installera på den virtuella datorn.

*Behöver du mer hjälp*

Lär dig hur du [skapa ett konto för push-installation av Mobilitetstjänsten](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill kunna ansluta till de replikerade virtuella datorerna i Azure efter redundansväxlingen till Azure. Om du vill ansluta till de replikerade virtuella datorerna i Azure, måste Contoso utföra några åtgärder på den lokala virtuella datorn före migreringen: 

1. För åtkomst via internet kan Contoso RDP på den lokala virtuella datorn före redundans. Contoso garanterar att TCP och UDP-regler lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler .
2. För åtkomst via Contosos plats-till-plats-VPN aktiverar Contoso RDP på den lokala datorn. Contoso tillåter RDP i **Windows-brandväggen** > **tillåtna appar och funktioner** för **domän och privat** nätverk.
3. Contoso anger operativsystemets SAN-princip på den lokala virtuella datorn till **OnlineAll**.

Contoso behöver också kontrollera dessa objekt när den körs en redundans:

- Det bör finnas inga Windows-uppdateringar som väntar på den virtuella datorn när en redundansväxling har utlösts. Om Windows-uppdateringar är väntande, Contoso kan inte logga in till den virtuella datorn förrän uppdateringen är klar.
- Efter redundansväxlingen Contoso ska kontrollera **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om Contoso inte kan visa startdiagnostiken, det bör kontrollera att den virtuella datorn körs, och granska sedan [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure-by-using-site-recovery"></a>Steg 5: Replikera lokala virtuella datorer till Azure med Site Recovery

Innan du kör en migrering till Azure måste Contoso konfigurerar replikering och aktiverar replikering för den lokala virtuella datorn.

### <a name="set-a-replication-goal"></a>Ange ett replikeringsmål

1. I valvet under valvnamnet (**ContosoVMVault**), Contoso anger ett replikeringsmål (**komma igång** > **Site Recovery**  >   **Förbereda infrastrukturen**).
2. Contoso anger som finns lokalt på en dator, att de är virtuella VMware-datorer och att Contoso vill replikera till Azure.

    ![Förbereda infrastrukturen - skyddsmål](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Contoso måste bekräfta att den har slutförts Distributionsplanering för att fortsätta. För att bekräfta, Contoso väljer **Ja, jag har gjort det**. I den här distributionen Contoso migrerar bara en enda virtuell dator, så att det inte behöver distributionsplanering.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Nu kan måste Contoso konfigurera dess källmiljö. Om du vill konfigurera dess källmiljön Contoso laddar du ned en OVF-mall. Contoso använder mallen för att distribuera konfigurationsservern och dess associerade komponenter som en högtillgänglig, en lokal VMware VM. Komponenterna på servern inkluderar:

- Konfigurationsservern som samordnar kommunikationen mellan den lokala infrastrukturen och Azure. Konfigurationsservern hanterar datareplikering.
- Processervern som fungerar som en replikeringsgateway. Processerver:
    - Tar emot replikeringsdata.
    - Optimerar replikering datum genom att använda cachelagring, komprimering och kryptering.
    - Skickar datum för replikering till Azure Storage.
- Processervern installerar också Mobilitetstjänsten på de virtuella datorerna som Contoso vill replikera. Processervern utför automatisk identifiering av lokala virtuella VMware-datorer.
- När du konfigurationsservern VM skapas och startas, registrerar Contoso servern i valvet.

Konfigurera källmiljön:

1. Contoso hämtar OVF-mall från Azure-portalen (**förbereda infrastrukturen** > **källa** > **konfigurationsservern**).
    
    ![Lägg till en konfigurationsserver](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Contoso importerar mallen till VMware för att skapa och distribuera den virtuella datorn.

    ![Distribuera OVF-mall](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  När Contoso sätter på den virtuella datorn för första gången, startar den virtuella datorn i en Windows Server 2016-installationsproceduren. Contoso accepterar licensavtalet och anger ett administratörslösenord.
4. När installationen är klar, loggar Contoso in på den virtuella datorn som administratör. Första gången som Contoso loggar in på den virtuella datorn och körs Azure Site Recovery-konfigurationsverktyget automatiskt.
5. I konfigurationsverktyget för Site Recovery anger Contoso du ett namn du använder för att registrera konfigurationsservern i valvet.
6. Verktyget kontrollerar den Virtuella datorns anslutning till Azure. När anslutningen har upprättats, Contoso väljer **logga in** att logga in på Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv som konfigurationsservern är registrerad. 

    ![Registrera konfigurationsservern](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn. Contoso loggar in på datorn igen. I guiden Konfigurera serverhantering startar automatiskt.
8. I guiden väljer Contoso nätverkskort för att ta emot replikeringstrafiken. Den här inställningen kan inte ändras när den har konfigurerats.
9. Contoso väljer den prenumeration, resursgrupp och Recovery Services-valv där du vill registrera konfigurationsservern.

    ![Välj Recovery Services-valv](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Contoso hämtar och installerar MySQL-Server och VMWare PowerCLI. Contoso verifierar sedan serverinställningarna.
11. Efter valideringen kan anger Contoso du FQDN eller IP-adress för vCenter Server-instans eller vSphere-värden. Contoso lämnar standardporten och anger ett visningsnamn för vCenter Server-instansen i Azure.
12. Contoso måste ange det konto som du skapade tidigare så att Site Recovery kan automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. 
13. Contoso anger autentiseringsuppgifter, så att Mobilitetstjänsten installeras automatiskt när replikering har aktiverats. Kontot måste ha lokal administratörsbehörighet på de virtuella datorerna för Windows-datorer. 

    ![Konfigurera vCenter Server](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. När registreringen är klar visas i Azure-portalen, Contoso kontrollerar igen att konfigurationsservern och VMware-servern visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
8. Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna. Site Recovery identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Nu kan måste Contoso konfigurera målmiljön för replikeringen:

1. I **Förbered infrastruktur** > **Target**, Contoso väljer målinställningarna.
2. Site Recovery kontrollerar att det är ett lagringskonto och nätverk i det angivna målet.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

När källan och målet har ställts in, Contoso skapar en replikeringsprincip och associerar principen med konfigurationsservern:

1. I **Förbered infrastruktur** > **replikeringsinställningar** > **replikeringsprincip** >  **skapa och Associera**, Contoso skapar den **ContosoMigrationPolicy** princip.
2. Contoso använder standardinställningarna för:
    - **Tröskelvärde för Replikeringspunktmål**: standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållning av återställningspunkt**: standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperioden är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens för appkonsekvent ögonblicksbild**: standardvärdet 1 timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.
 
    ![Replikeringsprincip – skapa](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. Principen associeras automatiskt med konfigurationsservern. 

    ![Replikeringsprincip – koppla](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*Behöver du mer hjälp?*

- Du kan läsa en genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurera replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Aktivera replikering

Contoso kan nu börja replikera WebVM.

1. I **replikera program** > **källa** > **replikera**, Contoso väljer inställningar för datakälla.
2. Contoso indikerar att det vill aktivera virtuella datorer, väljer vCenter Server-instansen och anger konfigurationsservern.

    ![Aktivera replikering - källa](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Contoso anger Målinställningar, inklusive resursgruppen och nätverk där den virtuella Azure-datorn kommer att finnas efter redundansväxling. Contoso anger det lagringskonto som replikerade data ska lagras.

    ![Aktivera replikering - mål](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso väljer **WebVM** för replikering. Site Recovery installerar Mobilitetstjänsten på varje virtuell dator när replikering har aktiverats. 

    ![Aktivera replikering – Välj den virtuella datorn](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso kontrollerar att rätt replikeringsprincip har valts. Sedan det aktiverar replikering för **WEBVM**. Contoso spårar Replikeringsförlopp på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.
6. I **Essentials** i Azure-portalen, Contoso kan se strukturen för de virtuella datorer som replikerar till Azure:

    ![Infrastruktur-vy](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*Behöver du mer hjälp?*

Du kan läsa en genomgång av de här stegen i [Aktivera replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-by-using-the-database-management-service"></a>Steg 6: Migrera databasen med hjälp av Database Management-tjänsten

Contoso behöver skapa ett Database Management Service-projekt och migrera sedan databasen.

### <a name="create-a-database-management-service-project"></a>Skapa ett projekt för Database Management-tjänsten

1. Contoso skapas ett Database Management Service-projekt. Contoso väljer den **SQL Server** typ av källserver. Contoso väljer **Azure SQL Database Managed Instance** som mål.

     ![Database Management-tjänsten – nytt migreringsprojekt](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Guiden öppnas.

### <a name="migrate-the-database"></a>Migrera databasen 

1. I guiden Migrera anger Contoso du den Virtuella där den lokala databasen finns. Contoso anger autentiseringsuppgifter för att få åtkomst till databasen.

    ![Database Management-tjänsten – källinformation](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Contoso väljer databasen för att migrera (**SmartHotel.Registration**):

    ![Database Management-tjänsten – Välj källdatabaser](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. För målet anger Contoso du namnet på den hanterade instansen i Azure. Contoso anger autentiseringsuppgifter för den hanterade instansen.

    ![Database Management-tjänsten – målinformation](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. I **ny aktivitet** > **kör migreringen**, Contoso anger inställningar för att köra migreringen:
    - Autentiseringsuppgifter för källa och mål.
    - Databasen som ska migreras.
    - Nätverksresurs som Contoso som skapats på den lokala virtuella datorn. Database Management-tjänsten tar källa säkerhetskopior till den här resursen. 
        - Kontot som kör SQL Server-källinstansen måste ha skrivbehörighet på den här resursen.
        - FQDN-sökvägen till resursen måste användas.
    - SAS-URI som ger tillgång till den lagringskontobehållare som tjänsten Överför säkerhetskopieringsfilerna för migrering av Database Management-tjänsten.

        ![Databasen Management-tjänsten – konfigurera migreringsinställningar för](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Contoso sparar migreringen och kör den.
6. I **översikt**, Contoso övervakar statusen för migrering.

    ![Database Management-tjänsten - Övervakare](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. När migreringen är klar, kontrollerar Contoso att måldatabaserna finns på den hanterade instansen.

    ![Databasen Management-tjänsten – verifiera Databasmigrering](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-by-using-site-recovery"></a>Steg 7: Migrera den virtuella datorn med hjälp av Site Recovery

Contoso kör en snabb testredundans och sedan migrerar den virtuella datorn.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Innan du migrerar WEBVM, ett redundanstest hjälper till att säkerställa att allt fungerar som förväntat. Contoso utföra följande steg:

1. Contoso kör ett redundanstest till den senaste tillgängliga tidpunkt (**senaste bearbetade**).
2. Contoso väljer **Stäng datorn innan du påbörjar redundans**. Med det här alternativet kan försöker Site Recovery stänga av den Virtuella källdatorn innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 
    1. En kravkontroll körs för att se till att alla villkor som krävs för migrering är uppfyllda.
    2. Redundansen bearbetar data så att du kan skapa en virtuell Azure-dator. Om den senaste återställningspunkten är vald, skapas en återställningspunkt från data.
    3.  Azure VM skapas med hjälp av de data som behandlas i föregående steg.
3. När redundansväxlingen är klar visas repliken virtuell Azure-dator i Azure-portalen. Contoso verifierar att allt fungerar korrekt: den virtuella datorn har rätt storlek, den är ansluten till rätt nätverk och den körs. 
4. När du har verifierat redundanstestningen rensar Contoso växling vid fel. Sedan registrerar och sparar eventuella observationer. 

### <a name="migrate-the-vm"></a>Migrera den virtuella datorn

1. Efter att ha kontrollerat att redundanstestningen fungerar som förväntat, skapar Contoso du en återställningsplan för migrering. Contoso lägger till WEBVM till planen:

     ![Skapa en återställningsplan - Välj objekt](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Contoso kör en redundans enligt planen. Contoso väljer den senaste återställningspunkten. Contoso anger att Site Recovery bör försöker stänga av den lokala virtuella datorn innan du utlöser redundansväxlingen.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Efter redundansväxlingen verifierar Contoso att den virtuella Azure-datorn visas som förväntat i Azure-portalen.

   ![Information om återställning av plan](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. När du har verifierat den virtuella datorn i Azure, Slutför Contoso migreringen du slutför migreringsprocessen, stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-debitering för den virtuella datorn.

    ![Redundans - fullständig migrering](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Uppdatera anslutningssträngen

Som det sista steget i migreringen uppdaterar Contoso anslutningssträngen för programmet för att peka på den migrerade databasen som körs på Contosos Managed Instance.

1. I Azure-portalen hittar Contoso anslutningssträngen genom att välja **inställningar** > **anslutningssträngar**.

    ![Anslutningssträngar](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Contoso uppdaterar strängen med användarnamn och lösenord för SQL Database Managed Instance.
3. När strängen har konfigurerats kan ersätter Contoso den aktuella anslutningssträngen i web.config-filen av programmet.
4. Startar om IIS på WEBVM när du uppdaterar filen och spara den, Contoso. Om du vill starta om IIS, Contoso körs `IISRESET /RESTART` i Kommandotolkens fönster.
5. När IIS startas använder appen den databas som körs på SQL Database Managed Instance.
6. Nu kan Contoso stänga av den lokala datorn för SQLVM. Migreringen har slutförts.

*Behöver du mer hjälp?*

- Lär dig hur du [kör ett redundanstest](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Lär dig hur du [skapa en återställningsplan](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Lär dig hur du [redundansväxlar till Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

Med migreringen har slutförts, SmartHotel appen körs på en Azure virtuell dator och SmartHotel databasen är tillgänglig i Azure SQL Database Managed Instance.  

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

- Security-teamet granskar nätverkssäkerhetsgrupper som används för att styra åtkomsten för den virtuella datorn. Nätverkssäkerhet grupper säkerställer att endast trafik som tillåts i appen kan skicka.
- Contosos överväger security också skydda data på disken med hjälp av Azure Disk Encryption och Azure Key Vault.
- Säkerhetsteamet gör det möjligt för identifiering av hot på den hanterade instansen. Hotidentifiering skickar en avisering till Contosos team/service desk säkerhetssystem att öppna en biljett om ett hot har identifierats. Läs mer om [hotidentifiering för hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Hanterad instans-security - hotidentifiering](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

Läs mer om säkerhetsrutiner för virtuella datorer i [säkerhetsmetodtips för IaaS-arbetsbelastningar i Azure](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control).

### <a name="backups"></a>Säkerhetskopior

Contoso säkerhetskopierar data på WEBVM med hjälp av Azure Backup-tjänsten. Läs mer om [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

- Contoso har en befintlig Licensiering för WEBVM. Om du vill dra nytta av priser med Azure Hybrid-förmånen, konverterar Contoso befintlig Azure-VM.
- Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Cost Management är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper till att Contoso använda och hantera Azure och andra molnresurser. Läs mer om [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Sammanfattning

I den här artikeln Contoso namnkonflikt SmartHotel-app i Azure genom att migrera appen klientdelens VM till Azure med hjälp av Site Recovery-tjänsten. Contoso migrerar en lokal databas till en Azure SQL Database Managed Instance med Azure Database Management-tjänsten.

## <a name="next-steps"></a>Nästa steg

I nästa artikel i serien, Contoso [namnkonflikt SmartHotel appen på Azure Virtual Machines](contoso-migration-rehost-vm.md) med hjälp av Azure Site Recovery-tjänsten.

