---
title: Rehost en Contoso-app i Azure genom att migrera till en virtuell dator i Azure och Azure SQL-instans som hanterade | Microsoft Docs
description: Lär dig hur Contoso ändrar värd en lokal app på Azure Virtual Machines och Azure SQL-instans som hanteras
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: c7dc9e8406494739aa5d8f21397a606e0b74a617
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301260"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Contoso-migrering: Rehost en lokal app på Azure Virtual Machines och Azure SQL-instans som hanteras

Den här artikeln visar hur Contoso migrerar dess SmartHotel app klientdel VM till virtuella Azure-datorer med hjälp av Azure Site Recovery-tjänsten och app-databasen till en Azure SQL hanteras-instans.

> [!NOTE]
> Azure SQL-hanterad-instansen är för närvarande under förhandsgranskning.

Det här dokumentet är fjärde i en serie med artiklar i hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och ett antal scenarier som visar hur du ställer in en infrastruktur för migrering och kör olika typer av migreringar. Scenarier växa i komplexitet och vi ska lägga till ytterligare artiklar över tid.


**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | En översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Beskriver hur Contoso förbereder dess lokalt och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla Contoso-Migreringsscenarier. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser](contoso-migration-assessment.md)  | Visar hur Contoso körs en bedömning av sina lokala två nivåer SmartHotel app som körs på VMware. De utvärdera app virtuella datorer med den [Azure migrera](migrate-overview.md) tjänsten och SQL Server-databasen app med den [Azure databasen Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
Artikel 4: Rehost virtuella Azure-datorer och en SQL hanteras-instans (den här artikeln) | Visar hur Contoso migrerar SmartHotel appen till Azure. Migrerar appen klientdel VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och app databasen med hjälp av [Azure Databasmigrering](https://docs.microsoft.com/azure/dms/dms-overview) tjänsten för att migrera till en hanterad SQL-instans. | Tillgängligt
[Artikel 5: Rehost till virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Visar hur Contoso migrerar appen SmartHotel VMs endast med Site Recovery.
[Artikel 6: Rehost virtuella Azure-datorer och SQL Server-Tillgänglighetsgrupper](contoso-migration-rehost-vm-sql-ag.md) | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera appen virtuella datorer och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Rehost en Linux-app på virtuella Azure-datorer](contoso-migration-rehost-linux-vm.md) | Visar hur Contoso migrerar Linux osTicket app till Azure virtuella datorer med Site Recovery. | Tillgängligt
[Artikel 8: Rehost en Linux-app till Azure virtuella datorer och Azure MySQL-Server](contoso-migration-rehost-linux-vm-mysql.md) | Visar hur Contoso migrerar Linux osTicket app till Azure virtuella datorer med hjälp av Site Recovery och till ett Azure MySQL-Server-instans med MySQL-arbetsstationen. | Tillgängligt

Om du vill använda SmartHotel exempelappen används i den här artikeln kan du hämta det från [github](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Lokal-arkitektur

Här är ett diagram som visar den aktuella Contoso lokal infrastrukturen.

![Contoso-arkitektur](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso har en huvudsakliga datacenter finns i den stad i New York i östra USA.
- De har tre ytterligare lokala grenar i USA.
- Det huvudsakliga datacentret är ansluten till internet med en Fiber metro ethernet-anslutning (500 Mbit/s).
- Varje gren är lokalt anslutet till internet med business klass anslutningar med IPSec VPN-tunnlar tillbaka till det huvudsakliga datacentret. Detta gör att hela nätverket anslutas permanent och optimerar Internetanslutning.
- Det huvudsakliga datacentret är fullständigt virtualiserat med VMware. De har två ESXi 6.5 virtualiseringsvärdar, hanteras av vCenter Server 6.5.
- Contoso använder Active Directory för Identitetshantering och DNS-servrar i det interna nätverket.
- Domänkontrollanter i datacenter kör på virtuella VMware-datorer. Domänkontrollanter på lokala grenar som körs på fysiska servrar.



## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har arbetat tillsammans med deras affärspartner att förstå vad företaget vill uppnå med migreringen:

- **Åtgärda tillväxt**: Contoso växer och det finns därför trycket på sina lokala datorer och infrastruktur.
- **Öka effektiviteten**: Contoso måste ta bort onödiga procedurer och förenkla processer för utvecklare och användare.  Affärsbehov IT att snabbt och inte avfall tid eller pengar, vilket leverera snabbare på kunders behov.
- **Öka flexibilitet**: Contoso IT måste kunna snabbare efter behov för företaget. Måste kunna reagera snabbare än ändringarna i marketplace om du vill aktivera framgången i en global ekonomi.  Det får inte vara i vägen eller bli en business-blockerare.
- **Skala**: takt med att företaget växer har Contoso IT måste ange system som kan växa i samma takt.

## <a name="migration-goals"></a>Mål för migrering

Contoso molnet team har fäst ned mål för den här migreringen. Dessa mål används för att avgöra den bästa migreringsmetoden:

- Efter migreringen ska app i Azure ha samma prestanda som idag i sina lokala VMWare-miljön.  Flytt till molnet innebär inte att appens prestanda är mindre viktigt.
- Contoso vill inte investera i den här appen.  Det är kritiska och viktiga för verksamheten, men i sin nuvarande form de vill flytta det som att molnet.
- Databasen administrativa uppgifter bör minimeras appen har migrerats.
- Contoso vill inte använda en Azure SQL Database för den här appen och är ute efter alternativ.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

I det här scenariot:

- Contoso vill migrera sina två nivåer lokala resa app.
- Appen nivåer över två virtuella datorer (WEBVM och SQLVM) och finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5)
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), körs på en virtuell dator.
- De migrera app-databasen (SmartHotelDB) till en hanterad om Azure SQL-instans.
- De migrera lokala VMwares virtuella datorer till en Azure VM.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Lokala virtuella datorer i Contoso-datacenter ska inaktiveras när migreringen är klar.

![Scenariots arkitektur](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Management-tjänsten för databasen](https://docs.microsoft.com/azure/dms/dms-overview) | DMS möjliggör sömlös migrering från flera databaskällor till Azure data plattformar, med minimal avbrottstid. | Lär dig mer om [regioner som stöds](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) för DMS och get [prisinformationen](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure SQL hanteras instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Hanterade instans är en hanterad databas-tjänst som representerar en fullständigt hanterad SQL Server-instans i Azure-molnet. Den delar samma kod med den senaste versionen av SQL Server Database Engine och har de senaste funktionerna, prestanda och säkerhetskorrigeringar. | Med Azure SQL Database hanterade instanser körs i Azure betalar avgifter baserat på kapacitet. [Läs mer](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten samordnar hanterar migrering och katastrofåterställning för virtuella datorer i Azure och lokala virtuella datorer och fysiska servrar.  | Azure Storage avgifter tas ut vid replikering till Azure.  Virtuella Azure-datorer skapas och debiteras när växling vid fel. [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.

 

## <a name="migration-process"></a>migreringsprocessen

Contoso kommer att migrera både webb- och data nivåer av SmartHotel appen till Azure.

1. Contoso har redan Azure-infrastrukturen på plats, så att de behöver bara lägga till några specifika Azure komponenter för det här scenariot.
2. Datanivå migreras med hjälp av Data migrering Service (DMS).  DMS ansluter till lokala SQL Server-VM via ett plats-till-plats VPN-anslutning mellan Contoso datacenter och Azure och migrerar sedan databasen.
3. Webbnivån migreras med hjälp av en lift och SKIFT-migrering med Azure Site Recovery. Detta innebär bland annat förbereds lokalt VMware-miljön, konfigurera och aktivera replikering och migrera de virtuella datorerna genom att inte dem via Azure.

     ![Arkitektur för migrering](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>Förutsättningar

Det här är vad Contoso (och du) måste för det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Registrera dig i förhandsgranskningen** | Du måste vara registrerade i SQL hanteras instans begränsad förhandsversion. Du behöver ett Azure-prenumeration för att [registrera](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Registrering kan ta ett par dagar att slutföra, så gör du det innan du börjar distribuera det här scenariot.
**Azure-prenumeration** | Du bör redan har skapat en prenumeration när du har utfört bedömningen i den första artikeln i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du är inte administratör, måste du arbeta med administratören att tilldela behörigheter för ägare eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site recovery (lokalt)** | Din lokala vCenter-servern måste köra version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värd.<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Stöd för [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [lagring](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguration.
**DMS** | För DMS måste en [kompatibel lokala VPN-enhet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Du måste kunna konfigurera den lokala VPN-enheten. Den måste ha ett externt Internetriktade offentlig IPv4-adress och adressen får inte finnas bakom en NAT-enhet.<br/><br/> Kontrollera att du har åtkomst till dina lokala SQL Server-databas.<br/><br/> Windows-brandväggen ska kunna komma åt datakällan databasmotorn. [Läs mer](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Lägg till regler för att tillåta åtkomst till databasen och till filer med hjälp av SMB-port 445 om en brandvägg framför datorn databasen.<br/><br/> De autentiseringsuppgifter som används för att ansluta till datakällan SQL Server och hanterade målinstans måste vara medlemmar i serverrollen sysadmin.<br/><br/> Du behöver ett nätverk delar i din lokala databas som DMS kan använda för att säkerhetskopiera källdatabasen.<br/><br/> Kontrollera att tjänstkontot som kör SQL Server-instansen källa har skrivbehörighet på nätverksresursen.<br/><br/> Anteckna en Windows-användare (och lösenord) som har behörigheten Fullständig behörighet på nätverksresursen. Tjänsten Azure Database migrering personifierar dessa autentiseringsuppgifter för att överföra säkerhetskopieringsfiler till Azure storage-behållare.<br/><br/> Installationen av SQL Server Express anger TCP/IP-protokollet till **inaktiverad** som standard. Kontrollera att den är aktiverad.


## <a name="scenario-steps"></a>Scenarioanvisningar

Här följer hur Contoso kommer att konfigurera distributionen:

> [!div class="checklist"]
> * **Steg 1: Konfigurera en SQL Azure hanteras instans**: de behöver en förskapad hanterade instans som ska migrera lokala SQL Server-databasen.
> * **Steg 2: Förbered DMS**: de behöver för att registrera providern Databasmigrering, skapa en instans och sedan skapa ett DMS-projekt. De måste också konfigurera SA URI för DMS. En signatur för delad åtkomst (SAS) identifierare URI (Uniform Resource) ger delegerad åtkomst till resurser i ditt lagringskonto, så att du kan ge begränsade behörigheter till lagringsobjekt. De har konfigurerat en SAS URI så att DMS kan komma åt kontot lagringsbehållare som tjänsten Överför filer för SQL Server-säkerhetskopiering.
> * **Steg 3: Förbered Azure Site Recovery**: för Site Recovery de måste skapa ett Azure storage-konto för att lagra replikerade data och skapa ett Recovery Services-valvet.
> * **Steg 4: Förbered lokal VMware för Site Recovery**: Contoso ska förbereda konton för VM identifieringen och agentinstallationen på nytt och förbereda för att ansluta till virtuella Azure-datorer efter redundans.
> * **Steg 5: Replikera VMs**: Om du vill konfigurera replikering måste de konfigurera Site Recovery-miljö för källa och mål, ställa in en replikeringsprincip och starta replikera virtuella datorer till Azure storage.
> * **Steg 6: Migrera databasen med DMS**: nu kan de migrera databasen.
> * **Steg 7: Migrera de virtuella datorerna med Site Recovery**: de kör ett redundanstest för att kontrollera att allt fungerar och kör sedan en fullständig växling för att migrera de virtuella datorerna till Azure.


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>Steg 1: Förbered en hanterad Azure SQL-instans

Om du vill konfigurera en Azure SQL hanteras instans måste Contoso ett undernät:

- Undernätet måste vara dedikerade. Det måste vara tom och inte innehåller några andra Molntjänsten och det får inte vara ett gateway-undernät.
- När den hantera instansen har skapats kan du får inte lägga till resurser till den.
- Undernätet får inte ha en NSG som kopplats till den.
- Undernätet måste ha en användare väg tabellen (UDR) med 0.0.0.0/0 Internet för nästa hopp som endast väg tilldelats. 
- Valfri anpassad DNS: om anpassade DNS har angetts för det virtuella nätverket, Azures rekursiv matchare IP-adress (till exempel 168.63.129.16) måste läggas till i listan. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Undernätet får inte ha en tjänstens slutpunkt (lagring eller SQL) som är kopplade till den. Slutpunkter ska inaktiveras på det virtuella nätverket.
- Undernätet måste ha minst 16 IP-adresser. [Lär dig mer](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) om storleksanpassa hanterade instans undernätet.
- I sina hybridmiljöer krävs anpassade DNS-inställningar. Contoso konfigurerar DNS-inställningar om du vill använda en eller flera av deras Azure DNS-servrar. [Lär dig mer](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) om DNS-anpassning.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Konfigurera ett virtuellt nätverk för den hantera instansen

Contoso har konfigurerat VNet på följande sätt: 

1. Contoso skapar ett nytt virtuellt nätverk (VNET-SQLMI-EU2) i den primära östra USA 2 regionen i resursgruppen ContosoNetworkingRG.
2. Contoso tilldelar ett adressutrymme för 10.235.0.0/24, se till att de inte överlappar med andra nätverk i företaget Contoso.
2. De lägger till två undernät i nätverket:
    - SQLMI DS EUS2 (10.235.0.0.25)
    - SQLMI-SÅG-EUS2 (10.235.0.128/29). Det här undernätet används för att ansluta directory till hanterade instansen (SQLMI).

    ![Instansen nätverk](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Efter att VNet och undernät har distribuerats, Contoso peers nätverk på följande sätt:

    - Peer-datorer VNET-SQLMI-EUS2 med VNET-hubb-EUS2 (NAV VNet för östra USA 2).
    - Peer-datorer VNET-SQLMI-EUS2 med VNET PRODUKTPRENUMERATION EUS2 (produktionsnätverket).

    ![Nätverk peering](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso anger anpassade DNS-inställningar. DNS pekar först till sina Azure-domänkontrollanter. Azure DNS kommer att sekundära. Contoso Azure domänkontrollanter finns på följande sätt:

    - Finns i PRODUKTPRENUMERATION-DC-EUS2-undernätet i östra USA 2 produktionsnätverket (VNET-PRODUKTPRENUMERATION-EUS2).
    - CONTOSODC3 adress: 10.245.42.4
    - CONTOSODC4 adress: 10.245.42.5
    - Azure DNS-matchning: 168.63.129.16

     ![Nätverket DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**Behöver du mer hjälp?**

- [Få en översikt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) för Azure SQL-instanser som hanteras.
- [Lär dig mer om](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances) om hur du skapar ett virtuellt nätverk för hanterade SQL-instansen.
- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering) konfigurerar peering.
- [Lär dig mer om](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns) Azure AD, DNS-inställningarna uppdateras.



### <a name="set-up-routing"></a>Konfigurera routning

Den hanterade instansen placeras i ett privat virtuellt nätverk, så Contoso måste en routningstabell att kommunicera med Azure Management-tjänsten. Om den inte kan kommunicera med tjänsten som hanterar den blir tillgänglig.

- Routningstabellen innehåller en uppsättning regler (vägar) som anger hur paket som skickas från hanterade instans ska dirigeras i virtuella nätverk.
- Routningstabellen är kopplad till undernät där hanterade instanser har distribuerats. Varje paket som lämnar ett undernät hanteras baserat på den associerade routningstabellen.
- Ett undernät kan bara associeras med en enda vägtabell.
- Det finns inga ytterligare avgifter för att skapa vägtabeller i Microsoft Azure.

1. Contoso skapar en användardefinierad routningstabell. Routningstabellen skapas i resursgrupp ContosoNetworkingRG.

    ![Routningstabell](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. För att följa hanteras instans krav när routningstabellen (MIRouteTable) har distribuerats kan Contoso lägga till en väg med ett adressprefix för 0.0.0.0/0, och **nästa hopptyp** inställd på **Internet**.

    ![Väg Tabellprefix](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso associera routningstabellen med SQLMI-DB-EUS2 undernät (i VNET-SQLMI-EUS2-nätverk). 

    ![Väg tabell undernät](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route) konfigurerar vägar för en hanterad instans.

### <a name="create-a-managed-instance"></a>Skapa en hanterad instans

Nu kan Contoso etablera en SQL-hanterade databasinstans.

1. Eftersom den hanterade instansen har en app för företag, Contoso distribuerar den i deras primära östra USA 2 region, i resursgrupp ContosoRG 
2. De välja en prisnivå nivån och storlek beräkning och lagring för instansen. [Lär dig mer](https://azure.microsoft.com/pricing/details/sql-database/managed/) om priser.

    ![Hanterad instans](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. När den hanterade instansen har distribuerats visas två nya resurser i resursgruppen ContosoRG:

    - En virtuell kluster om du har flera hanterade instanser
    - SQLServer hanterade instans. 

    ![Hanterad instans](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) etablering av en hanterad instans.

## <a name="step-2-prepare-dms"></a>Steg 2: Förbered DMS

Om du vill förbereda DMS Contoso behöver göra några saker:

- Registrera providern DMS i Azure
- DMS ger åtkomst till Azure-lagring för uppladdning av de säkerhetskopiera filerna som används för att migrera en databas. De gör detta genom att skapa en behållare för blob storage och generera en delad signatur åtkomst (SAS) URI för den. 
- Skapa ett DMS-projekt.


Utför följande steg:

1. Contoso registrera providern Databasmigrering under sin prenumeration.
    ![DMS registrera](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. De skapar en lagringsblobbehållare och generera en SAS-URI så att DMS kan komma åt den.

    ![SAS-URI](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Dessutom skapas en DMS-instans. 

    ![DMS-instans](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso placerar DMS-instans i PROD-DC-EUS2 undernätet i VNET-PRODUKTPRENUMERATION-DC-EUS2 VNet.
    - De placeras det eftersom den måste vara i ett virtuellt nätverk som har åtkomst till den lokala SQL Server-VM via en VPN-gateway.
    - VNET-PRODUKTPRENUMERATION-EUS2 peerkoppla till VNET-hubb-EUS2 och har behörighet att använda remote gateways.  Detta säkerställer att DMS kan kommunicera efter behov.

        ![DMS-nätverk](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**Behöver du mer hjälp?**
- [Lär dig mer om](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) konfigurerar DMS.
- [Lär dig mer](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-2) om att skapa och med hjälp av SAS.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Steg 3: Förbered Azure för Site Recovery-tjänsten

Det finns ett antal Azure element Contoso behövs för att ställa in Site Recovery för migrering av deras webbnivå VM (WEBMV)

- Ett virtuellt nätverk som växlar över resurserna finns.
- Ett Azure storage-konto för att lagra replikerade data. 
- Ett Recovery Services-valv i Azure.

De har konfigurerat Site Recovery på följande sätt:

1. Eftersom den virtuella datorn är en webbtjänst klientdel till appen SmartHotel, växlar de den virtuella datorn till deras befintliga produktionsnätverk (VNET-PRODUKTPRENUMERATION-EUS2) och undernät (PROD-FE-EUS2) i den primära regionen östra USA 2. De in det här nätverket sig när de [distribuerat Azure-infrastrukturen](contoso-migration-infrastructure.md)
2. De skapar ett Azure storage-konto (contosovmsacc20180528). De använder ett generella-konto med standardlagring och LRS replikering.

    ![Site Recovery-lagring](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Med kontot nätverk och lagring för kan Contoso nu skapa ett valv (ContosoMigrationVault) och placera det i gruppen ContosoFailoverRG resurs i den primära regionen östra USA 2.

    ![Recovery Services-valv](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) ställa in Azure Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Steg 4: Förbered lokal VMware för Site Recovery

Om du vill förbereda VMware för Site Recovery är här vad Contoso måste göra:

- Förbered ett konto på vCenter server eller vSphere ESXi värden, att automatisera VM-identifiering.
- Förbered ett konto som ger automatisk installation av mobilitetstjänsten på virtuella VMware-datorer som du vill replikera.
- Förbereda lokala virtuella datorer, så att de kan ansluta till virtuella Azure-datorer när de skapas efter växling vid fel.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Automatiskt identifiera virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som till exempel att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso har konfigurerat kontot på följande sätt:

1. Skapar en roll på vCenter-nivå.
2. Tilldelar rollen behörigheterna som krävs.

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på den virtuella dator som du vill replikera.

- Site Recovery kan göra en automatisk push-installation av den här komponenten när du aktiverar replikering för den virtuella datorn.
- För automatisk push-installation måste du förbereda ett konto som Site Recovery för att komma åt den virtuella datorn.
- Du kan ange det här kontot när du konfigurerar replikering i Azure-konsolen.
- Behöver du en domän eller lokalt konto med behörighet för att installera på den virtuella datorn.

**Behöver du mer hjälp**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill kunna ansluta till de replikerade virtuella datorerna i Azure efter en redundansväxling till Azure. Detta gör finns det några saker som de behöver göra på den lokala virtuella datorn innan de kör migreringen: 

1. De aktiverar du RDP på den lokala virtuella datorn innan redundans för åtkomst via internet, och kontrollera att TCP och UDP-regler har lagts till för den **offentliga** profil och att RDP tillåts i **Windows-brandväggen**  >  **Tillåtna appar** för alla profiler.
2. De aktiverar du RDP på den lokala datorn för åtkomst via deras plats-till-plats-VPN och Tillåt RDP i den **Windows-brandväggen** -> **tillåtna appar och funktioner** för **domän och Privata** nätverk.
3. De angetts operativsystemets SAN-principen på den lokala virtuella datorn till **OnlineAll**.

Dessutom, när de kör en redundansväxling behöver de för att kontrollera följande:

- Det ska vara ingen Windows-uppdateringar som väntar på den virtuella datorn när utlösa en växling vid fel. Om det finns, kan de inte logga in på den virtuella datorn förrän uppdateringen är klar.
- De ska kontrollera efter växling vid fel, **starta diagnostik** att visa en skärmbild av den virtuella datorn. Om det inte fungerar de bör kontrollera att den virtuella datorn körs och granska dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Steg 5: Replikera lokala virtuella datorer till Azure med Site Recovery

Innan du kör en migrering till Azure måste Contoso konfigurerar replikering och aktivera replikering för sina lokala virtuella datorn.

### <a name="set-a-replication-goal"></a>Ange ett mål för replikering

1. I valvet, under valvnamnet (ContosoVMVault) de ange ett mål för replikering (**komma igång** > **Site Recovery** > **Förbered infrastruktur**.
2. De kan ange att deras datorer är lokalt, att de är virtuella VMware-datorer och att de vill replikera till Azure.

    ![Replikeringsmål](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta att planera distribution

Om du vill fortsätta, de behöver för att bekräfta att de har slutfört planera distribution genom att välja **Ja, jag har gjort det**. I den här distributionen Contoso bara migrera en enda virtuell dator, och behöver inte planera distribution.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Nu måste konfigurera sina källmiljön. Om du vill de hämtar ett OVF-mall och distribuera den konfigurationsservern och dess associerade komponenter med hög tillgänglighet, lokal VMware VM. Komponenterna på servern inkluderar:

- Konfigurationsservern som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processerver som fungerar som en replikerings-gateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.
- Efter konfigurationsservern VM skapas och igång och Contoso kan du registrera det i valvet.


Contoso utför de här stegen på följande sätt:

1. De hämtar OVF-mall från Azure-portalen (**Förbered infrastrukturen** > **källa** > **konfigurationsservern**).
    
    ![Hämta OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. De importera mallen till VMware för att skapa och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
4. När installationen är klar kan logga de in på den virtuella datorn som administratör. Vid första gången inloggning kör konfigurationsverktyget för Azure Site Recovery som standard.
5. I verktyget Ange de ett namn som ska användas när du registrerar konfigurationsservern i valvet.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats kan de välja **logga in**, för att logga in på Azure-prenumerationen. Autentiseringsuppgifterna måste ha åtkomst till valvet där konfigurationsservern ska registreras. 

    [Registrera konfigurationsservern](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn. De loggar in på datorn igen och guiden Konfigurera Server Management startar automatiskt.
8. I guiden Välj de nätverkskort för att ta emot replikeringstrafik. Den här inställningen kan inte ändras när den har konfigurerats.
9. De Välj prenumerationen, resursgruppen och valvet att registrera konfigurationsservern.
        ![Valvet](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. De och sedan ladda ned och installera MySQL-Server och VMWare PowerCLI. Sedan validera de serverinställningar.
11. När valideringen ange de FQDN eller IP-adressen för vCenter-server eller vSphere-värd. De lämnar standardporten och anger ett eget namn för vCenter-servern i Azure.
12. De behöver för att ange det konto som de skapade tidigare, så att Site Recovery kan automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. 
13. De ange autentiseringsuppgifterna för att installera Mobilitetstjänsten automatiskt när replikeringen är aktiverad. För Windows-datorer måste kontot behörighet som lokal administratör på virtuella datorer. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. När registreringen är klar i Azure-portalen Contoso dubbla kontrollerar att konfigurationsservern och VMware-server visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
8. Site Recovery kan du sedan ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Contoso måste konfigurera målmiljön för replikering.

1. I **Förbered infrastruktur** > **mål**, de Välj inställningar för målet.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i det angivna målet.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

När källa och mål har konfigurerats, är Contoso redo att skapa en replikeringsprincip och koppla den med konfigurationsservern.

1. I **Förbered infrastruktur** > **replikeringsinställningarna** > **Replikeringsprincipen** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Återställningspunktmål**: standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållningstid för återställningspunkten**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperiod är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens av programkonsekventa ögonblicksbilder**. Standardvärdet för en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.
 
        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincipen](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**Behöver du mer hjälp?**

- Du kan läsa en fullständig genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurerar replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Aktivera replikering

Contoso kan nu starta replikering av WebVM.

1. I **replikera program** > **källa** > **+ replikera** de välja inställningar för datakälla.
2. De anger att de vill aktivera virtuella datorer, Välj vCenter-servern och konfigurationsservern.

 ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Nu kan ange de Målinställningar, inklusive resursgruppen och nätverk där den virtuella Azure-datorn kommer att finnas efter en redundansväxling och lagringskontot som replikerade data ska sparas.

     ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso väljer WebVM för replikering. Site Recovery installerar Mobilitetstjänsten på varje virtuell dator när du aktiverar replikering för den. 

    ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso kontrollerar att rätt replikeringsprincipen är markerad och aktiverar replikering för WEBVM. De spåra replikeringsförloppet på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.
6. I **Essentials** i Azure-portalen Contoso kan se strukturen för de virtuella datorerna som replikerar till Azure.

    ![Visa infrastruktur](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**Behöver du mer hjälp?**

Du kan läsa en fullständig genomgång av de här stegen i [Aktivera replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>Steg 6: Migrera databasen med DMS

Contoso måste skapa ett DMS-projekt och migrerar-databasen.

### <a name="create-a-dms-project"></a>Skapa ett DMS-projekt
1. De skapar ett DMS-projekt. De anger typ av server som SQL Server, och mål som hanterade Azure SQL Database-instans.

     ![DMS-projekt](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. När du har skapat projektet öppnas guiden Migrera.

### <a name="migrate-the-database"></a>Migrera databasen 

1. I guiden Migrera anger Contoso källa VM som den lokala databasen finns och autentiseringsuppgifter för att komma åt den.

    ![DMS-källa](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. De Markera databasen som ska migreras (SmartHotel.Registration).

    ![DMS-databasen](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Som ett mål ange de namnet på instansen hanteras i Azure och autentiseringsuppgifter.

    ![DMS-mål](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. I **+ ny aktivitet** > **kör migrering**, de anger inställningar för att köra migreringen:
    - Käll- och autentiseringsuppgifter.
    - Databasen som ska migreras.
    - Nätverksresursen som de har skapats på den lokala virtuella datorn. DMS tar källa säkerhetskopieringar till den här resursen.
        - Kontot kör källan SQL Server måste ha skrivbehörighet på den här resursen.
        - Ange FQDN-sökvägen till resursen.
    - SAS-URI som ger tillgång till kontot lagringsbehållare som tjänsten Överför säkerhetskopiorna för migrering av DMS.

        ![DMS-inställningar](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. De spara migreringen och köra den.
6. I **översikt**, övervakar statusen för migrering.

    ![DMS-Övervakaren](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. När migreringen har slutförts, verifiera de att måldatabaserna finns på hanterade instansen.

    ![DMS-Övervakaren](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>Steg 7: Migrera den virtuella datorn med Site Recovery

Contoso körs snabbt testa redundans och sedan migrera den virtuella datorn.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Innan du migrerar WEBVM, testa redundans kan du kontrollera att allt fungerar som förväntat. 

1. De köra ett redundanstest för den senaste tillgängliga tidpunkten (**senaste bearbetas**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan växling vid fel. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 

    - En kravkontroll körs se till att alla villkor krävs för migrering på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.
3. När redundansväxlingen är klar visas repliken Azure VM i Azure-portalen. De kontrollera att allt fungerar korrekt. Den virtuella datorn har rätt storlek, den är ansluten till rätt nätverk och körs. 
4. När du har verifierat redundanstestningen Contoso rensar redundans och poster och sparar synpunkter. 

### <a name="migrate-the-vm"></a>Migrera den virtuella datorn

1. När du har verifierat att du testar redundansen fungerade som förväntat, skapar en återställningsplan för migrering i Contoso. De lägger till WEBVM planen.

     ![Återställningsplan](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. De kör sedan en växling vid fel på planen. De markerar du den senaste återställningspunkten och ange Site Recovery ska försöka stänga av den lokala virtuella datorn innan växling vid fel.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Efter växling vid fel Kontrollera Contoso att Azure VM visas som förväntat i Azure-portalen.

   ![Återställningsplan](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. När du har verifierat att den virtuella datorn i Azure, slutföra de migreringen för att slutföra migreringen, stoppa replikering för den virtuella datorn och stoppa Site Recovery-faktureringen för den virtuella datorn.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Uppdatera anslutningssträngen

Contoso uppdaterar anslutningssträngen för att peka på den migrerade databas som körs på deras SQL MI som det sista steget i migreringen.

1. I Azure-portalen, de kan hitta anslutningssträngen genom att klicka på **inställningar** > **anslutningssträngar**.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. De uppdatera strängen med användarnamn och lösenord för SQL-instansen hanteras.
3. När strängen har konfigurerats, ersätter de aktuella anslutningssträngen i web.config-filen för sina program.
4. De starta om IIS på WEBVM när uppdaterar filen och spara filen. Användarna kan göra detta med **IISRESET /RESTART** från en kommandotolk.
5. När IIS har startats med appen databas som körs på den hanterade SQL-instansen.
6. Contoso kan nu stänga av den SQLVM dator lokalt och migreringen är klar.

**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) kör ett redundanstest. 
- [Läs](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) så här skapar du en återställningsplan.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) redundansväxlar till Azure.

## <a name="clean-up-after-migration"></a>Rensa efter migreringen

SmartHotel appen körs på en virtuell dator i Azure med migreringen är klar, och SmartHotel databasen är tillgänglig på Azure SQL hanteras-instansen.  

Nu behöver Contoso göra vissa rensning på följande sätt:  

- Ta bort den WEBVM datorn från vCenter-lagret.
- Ta bort den SQLVM datorn från vCenter-lagret.
- Ta bort WEBVM och SQLVM från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentationen visar den nya platsen, IP-adress för WEBVM.
- Ta bort SQLVM-dokumentationen. De kan också markera det om du vill visa tagits bort och inte längre i den virtuella datorn inventering.
- Granska alla resurser som interagerar med inaktiverade virtuella datorer och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.

## <a name="review-the-deployment"></a>Granska distributionen

Med de migrerade resurserna i Azure måste Contoso helt operationalisera och skydda den nya infrastrukturen.

### <a name="security"></a>Säkerhet

Contoso security-teamet har granskat den virtuella Azure-datorer och SQL hanteras-instans för att fastställa eventuella säkerhetsproblem med dess implementering.

- De granska Nätverkssäkerhetsgrupper (NSG: er) för den virtuella datorn för åtkomstkontroll. NSG: er används för att se till att endast trafik som tillåts i appen kan skicka.
- De också med tanke på att skydda data på disken med hjälp av Azure Disk Encryption och Azure KeyVault.
- De aktivera hotidentifiering på SQL hanteras instansen (SQLMI). De skickar aviseringar till deras team/service supportavdelningen säkerhetssystem att öppna en biljett om ett hot upptäcks. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Hanterade instans säkerhet](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) om säkerhetsåtgärder för virtuella datorer.

### <a name="backups"></a>Säkerhetskopior
Contoso kommer att säkerhetskopiera data på WEBVM med hjälp av Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licens- och optimering

1. Contoso har befintliga licensiering för WEBVM och använder förmån för Azure-Hybrid.  De kommer att konverteras den befintliga virtuella Azure-datorn för att dra nytta av den här prisnivån.
2. Contoso aktiverar Azure kostnaden Management licensierade av Cloudyn, ett Microsoft-kontor. Det är en lösning för flera molnkostnad som hjälper dig att använda och hantera Azure och andra molnresurser.  [Lär dig mer](https://docs.microsoft.com/azure/cost-management/overview) om hantering av Azure kostnaden. 


## <a name="conclusion"></a>Sammanfattning

I den här artikeln rehosted Contoso SmartHotel app i Azure genom att migrera app-klientdel VM till Azure med Site Recovery-tjänsten. De migrerade lokala databasen till en Azure SQL hanteras instans med DMS.

## <a name="next-steps"></a>Nästa steg

I nästa artikel i serien får du lära hur Contoso rehost SmartHotel app på virtuella Azure-datorer med hjälp av Azure Site Recovery-tjänsten.

