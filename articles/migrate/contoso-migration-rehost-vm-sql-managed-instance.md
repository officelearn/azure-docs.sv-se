---
title: Ange ny värd för en Contoso-app i Azure genom att migrera till en virtuell dator i Azure och Azure SQL Managed Instance | Microsoft Docs
description: Lär dig hur Contoso namnkonflikt en lokal app på Azure virtuella datorer och Azure SQL Managed Instance
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: raynew
ms.openlocfilehash: 99eda135161a228fde139458de30f5120af55153
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723947"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Contoso-migrering: Appvärd till Azure virtuella datorer och Azure SQL Managed Instance lokalt

Den här artikeln visar vi hur Contoso migrerar dess SmartHotel app klientdelens VM till virtuella Azure-datorer med Azure Site Recovery-tjänsten och app-databasen till en Azure SQL Managed Instance.

> [!NOTE]
> Azure SQL Managed Instance är för närvarande i förhandsversion.

Det här dokumentet är fjärde i en serie av artiklar som dokumenterar hur det fiktiva företaget Contoso migrerar sina lokala resurser till Microsoft Azure-molnet. Serien innehåller bakgrundsinformation och ett antal scenarier som illustrerar hur du konfigurerar en infrastruktur för migreringen och kör olika typer av migreringar. Scenarier växer i komplexitet och vi ska lägga till ytterligare artiklar med tiden.


**Artikel** | **Detaljer** | **Status**
--- | --- | ---
[Artikel 1: översikt](contoso-migration-overview.md) | Översikt över Contosos migreringsstrategi, artikelserien och exempelappar som vi använder. | Tillgängligt
[Artikel 2: Distribuera en Azure-infrastruktur](contoso-migration-infrastructure.md) | Beskriver hur Contoso förbereder sina lokala och Azure-infrastrukturen för migrering. Samma infrastruktur används för alla scenarier för migrering av Contoso. | Tillgängligt
[Artikel 3: Utvärdera lokala resurser](contoso-migration-assessment.md)  | Visar hur Contoso körs en bedömning av sina lokala tvålagers-SmartHotel-app som körs på VMware. De utvärdera app virtuella datorer med den [Azure Migrate](migrate-overview.md) -tjänsten och SQL Server-databas för appen med den [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tillgängligt
Artikel 4: Byt värd till virtuella Azure-datorer och en SQL-hanterad instans (den här artikeln) | Visar hur Contoso migrerar SmartHotel appen till Azure. De migrera appen klientdelens VM med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), och appen databasen med den [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) service för att migrera till en SQL-hanterad instans. | Tillgängligt
[Artikel 5: Byt värd till virtuella Azure-datorer](contoso-migration-rehost-vm.md) | Visar hur Contoso migrerar appen SmartHotel virtuella datorer med Site Recovery endast.
[Artikel 6: Byt värd till virtuella Azure-datorer och SQL Server-Tillgänglighetsgrupper](contoso-migration-rehost-vm-sql-ag.md) | Visar hur Contoso migrerar SmartHotel appen. De kan använda Site Recovery för att migrera de virtuella datorerna för appen och tjänsten Databasmigrering för att migrera app-databas till en SQL Server-tillgänglighetsgrupp. | Tillgängligt
[Artikel 7: Byta Appvärd en Linux på Azure virtuella datorer](contoso-migration-rehost-linux-vm.md) | Visar hur Contoso migrerar Linux osTicket-app till Azure virtuella datorer med Site Recovery. | Tillgängligt
[Artikel 8: Rehost en Linux-app till Azure virtuella datorer och Azure MySQL-Server](contoso-migration-rehost-linux-vm-mysql.md) | Visar hur Contoso migrerar Linux osTicket-app till Azure virtuella datorer med Site Recovery och till en Azure MySQL-Server-instans med MySQL Workbench. | Tillgängligt

Om du vill använda SmartHotel exempelappen används i den här artikeln kan du hämta det från [github](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Lokala arkitektur

Här är ett diagram som visar den aktuella Contoso lokal infrastrukturen.

![Contoso-arkitektur](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso har en huvuddatacentret finns i den stad i New York i östra USA.
- De har tre ytterligare lokala grenar i USA.
- Huvuddatacentret är ansluten till internet med en Fiber metro Ethernet-anslutning (500 Mbit/s).
- Varje gren är lokalt ansluten till internet med företag klass anslutningar med IPSec VPN-tunnlar till huvuddatacentret. På så sätt kan hela nätverket ska anslutas permanent och optimerar Internetanslutning.
- Huvuddatacentret är fullständigt virtualiserat med VMware. De har två ESXi 6.5-virtualiseringsvärdar, hanteras av vCenter Server 6.5.
- Contoso använder Active Directory för Identitetshantering och DNS-servrar i det interna nätverket.
- Domänkontrollanterna i datacentret körs på virtuella VMware-datorer. Domänkontrollanter på lokala grenar som körs på fysiska servrar.



## <a name="business-drivers"></a>Affärsdrivande faktorer

IT-ledning har arbetat tillsammans med deras affärspartner att förstå vad företaget vill uppnå med den här migreringen:

- **Åtgärda tillväxten**: Contoso växer och det finns därför trycket på sina lokala system och infrastrukturer.
- **Öka effektiviteten**: Contoso måste ta bort onödiga procedurer och effektivisera processer för utvecklare och användare.  Affärsbehov IT ska vara snabbt och inte spill tid eller pengar och därför leverera snabbare på kunders behov.
- **Förbättrad flexibilitet**: Contoso IT måste vara mer responsiva gentemot behoven i verksamheten. Måste kunna reagera snabbare än ändringarna i marketplace, aktivera kan användas i en global ekonomi.  Det får inte vara i vägen eller bli en blockerare för företag.
- **Skala**: när verksamheten växer har, Contoso IT måste tillhandahålla system som kan växa i samma takt.

## <a name="migration-goals"></a>Mål för migrering

Contoso cloud-teamet har fästs ned mål för den här migreringen. Dessa mål används till att avgöra den bästa migreringsmetoden:

- Efter migreringen ska app i Azure ha samma prestandafunktioner som idag i sina lokala VMWare-miljön.  Flytta till molnet innebär inte att prestanda är mindre viktigt.
- Contoso vill inte investera i den här appen.  Det är kritiska och viktiga för verksamheten, men i sin nuvarande form de helt enkelt vill flytta det som det är till molnet.
- Administrativa uppgifter för databasen ska minimeras, efter att appen har migrerats.
- Contoso vill inte använda en Azure SQL-databas för den här appen och är ute efter alternativ.

## <a name="proposed-architecture"></a>Föreslagna arkitektur

I det här scenariot:

- Contoso vill migrera sina två skikt lokal reseapp.
- Appen är nivåindelad över två virtuella datorer (WEBVM och SQLVM) och finns på VMware ESXi-värd **contosohost1.contoso.com** (version 6.5)
- VMware-miljön hanteras av vCenter Server 6.5 (**vcenter.contoso.com**), som körs på en virtuell dator.
- De migrera app-databasen (SmartHotelDB) till en Azure SQL-hanterad instans.
- De migrera lokala VMware-datorer till en Azure-dator.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Lokala virtuella datorer i Contoso-datacenter inaktiveras när migreringen är klar.

![Scenariots arkitektur](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Azure-tjänster

**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Database Management-tjänsten](https://docs.microsoft.com/azure/dms/dms-overview) | DMS gör det möjligt för sömlös migrering från flera databaskällor till azures plattformar, med minimal avbrottstid. | Lär dig mer om [regioner som stöds](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) för DMS och få [prisinformation](https://azure.microsoft.com/pricing/details/database-migration/).
[Azure SQL-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance är en hanterad databastjänst som representerar en fullständigt hanterad SQL Server-instans i Azure-molnet. Den delar samma kod med den senaste versionen av SQL Server Database Engine och har de senaste funktionerna, prestandaförbättringar och säkerhetsuppdateringar. | Med hjälp av Azure SQL Database-hanterade instanser i Azure betalar avgifter baserat på kapacitet. [Läs mer](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten dirigerar och hanterar migrering och haveriberedskap för virtuella datorer i Azure och lokala datorer och fysiska servrar.  | Azure Storage-avgifter tillkommer under replikering till Azure.  Virtuella Azure-datorer skapas och betala, vid redundans. [Läs mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.

 

## <a name="migration-process"></a>Migreringsprocessen

Contoso kommer att migrera både webb- och data nivåer av SmartHotel appen till Azure.

1. Contoso har redan Azure-infrastruktur på plats, så att de behöver bara lägga till några specifika Azure-komponenter för det här scenariot.
2. Datanivån kommer att migreras med hjälp av Data Migration Service (DMS).  DMS ansluter till en lokal SQL Server-dator via en plats-till-plats VPN-anslutning mellan Contoso-datacenter och Azure och migrerar databasen.
3. Webbnivån kommer att migreras med hjälp av en lift and shift-migrering med Azure Site Recovery. Detta innebär bland annat förbereda lokala VMware-miljö, konfigurerar och aktiverar replikering och migrerar de virtuella datorerna genom att föra över dem till Azure.

     ![Migreringsarkitektur för](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>Förutsättningar

Här är vad Contoso (och du) som behöver det här scenariot.

**Krav** | **Detaljer**
--- | ---
**Registrera dig i förhandsversion** | Du måste vara registrerade i SQL hanterad instans begränsad förhandsversion. Du behöver en Azure-prenumeration för att [registrera](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Registreringen kan ta ett par dagar att slutföra, så se till att du göra det innan du börjar distribuera det här scenariot.
**Azure-prenumeration** | Du bör redan har skapat en prenumeration när du har utfört utvärderingen i den första artikeln i den här serien. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du inte är administratör, måste du be administratören tilldela dig ägar-eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Webbplatsåterställning (lokalt)** | Din lokala vCenter-servern måste köra version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värden.<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> För att förbereda VMware för Site Recovery, är här vad Contoso måste göra:
**FÖRBEREDA ETT KONTO PÅ VCENTER-SERVERN ELLER VSPHERE ESXI-VÄRDEN, ATT AUTOMATISERA VM-IDENTIFIERING.** | Förbereda ett konto som tillåter automatisk installation av mobilitetstjänsten på virtuella VMware-datorer som du vill replikera.<br/><br/> Förbereda lokala virtuella datorer, så att de kan ansluta till virtuella Azure-datorer när de skapas efter en redundansväxling. Du behöver ett konto som kan köra åtgärder som att skapa och ta bort diskar och aktivering av virtuella datorer.<br/><br/> Contoso ställer in kontot enligt följande:<br/><br/> Skapar en roll på vCenter-nivå. [Läs mer](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Tilldelar behörigheterna som krävs till rollen.<br/><br/> Lär dig mer om skapa och tilldela en roll för automatisk identifiering.<br/><br/> Site Recovery kan göra en automatisk push-installation av den här komponenten när du aktiverar replikering för den virtuella datorn.<br/><br/> För automatisk push-installation måste du förbereda ett konto som Site Recovery använder för att få åtkomst till den virtuella datorn.<br/><br/> Du anger det här kontot när du konfigurerar replikering i Azure-konsolen. Du behöver en domän eller lokalt konto med behörighet att installera på den virtuella datorn.<br/><br/> Behöver du mer hjälp Lär dig mer om skapar ett konto för push-installation av mobilitetstjänsten.


## <a name="scenario-steps"></a>Scenarioanvisningar

Contoso vill kunna ansluta till de replikerade virtuella datorerna i Azure efter redundansväxlingen till Azure.

> [!div class="checklist"]
> * **Detta gör att det finns några saker som de behöver göra på den lokala virtuella datorn innan de kör migreringen:
> * **De aktiverar du RDP på den lokala virtuella datorn före redundans för åtkomst via internet, och ser till att TCP och UDP-regler lagts till för den **offentliga** profilen och att RDP tillåts i Windows-brandväggen  Tillåtna appar för alla profiler. För åtkomst via deras plats-till-plats-VPN, de aktiverar du RDP på den lokala datorn och Tillåt RDP i den Windows-brandväggentillåtna appar och funktioner för domän och Privata nätverk. De ange operativsystemets SAN-princip på den lokala virtuella datorn till OnlineAll. Dessutom när de kör en redundans måste de du kontrollera följande:
> * **Det bör finnas inga Windows-uppdateringar som väntar på den virtuella datorn när du utlöser en redundans.
> * **Om det finns, kan de inte logga in på den virtuella datorn förrän uppdateringen är klar.
> * **Efter redundansväxlingen bör de kontrollera **Startdiagnostik** visar en skärmbild av den virtuella datorn.
> * **Om det inte fungerar de bör kontrollera att den virtuella datorn körs och granska sedan dessa **felsökningstips**.
> * **Steg 5: Replikera lokala virtuella datorer till Azure med Site Recovery


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>Innan du kör en migrering till Azure måste Contoso konfigurerar replikering och aktiverar replikering för sina lokala virtuella datorn.

Ange ett replikeringsmål

- I valvet under valvnamnet (ContosoVMVault) de ange ett replikeringsmål (komma igångSite RecoveryFörbered infrastruktur. De ange som finns lokalt på sina datorer, att de är virtuella VMware-datorer och att de vill replikera till Azure.
- Om du vill fortsätta, de behöver för att bekräfta att de har slutfört distributionsplanering genom att välja Ja, jag har gjort det.
- I den här distributionen Contoso är bara migrera en enskild virtuell dator och behöver inte distributionsplanering.
- Nu måste konfigurera sina källmiljö. 
- Om du vill de ladda ned en OVF-mall och använda den för att distribuera konfigurationsservern och dess associerade komponenter som en högtillgänglig, en lokal VMware VM. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- När du konfigurationsservern VM skapas och startas, registrera Contoso den i valvet. Contoso utför de här stegen på följande sätt:
- De ladda ner OVF-mall från Azure-portalen (förbereda infrastrukturenkällakonfigurationsservern). [Ladda ner OVF
- De importerar mallen till VMware för att skapa och distribuera den virtuella datorn. När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. [De acceptera licensavtalet och ange ett administratörslösenord.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>När installationen är klar, de loggar in på den virtuella datorn som administratör.

Vid första gången inloggning körs Azure Site Recovery-konfigurationsverktyget som standard. 

1. I verktyget Ange de ett namn som du använder när du registrerar konfigurationsservern i valvet.
2. När anslutningen har upprättats kan de välja logga in, för att logga in på Azure-prenumeration.
2. Autentiseringsuppgifterna måste ha åtkomst till det valv som konfigurationsservern ska registreras.
    - REGISTRERA KONFIGURATIONSSERVERN
    - DE LOGGAR IN PÅ DATORN IGEN OCH I GUIDEN KONFIGURERA SERVERHANTERING STARTAR AUTOMATISKT. I guiden Välj de nätverkskort för att ta emot replikeringstrafiken.

    ![Den här inställningen kan inte ändras när den har konfigurerats.](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. De Välj prenumeration, resursgrupp och valv där du vill registrera konfigurationsservern.

    - Valvet
    - De och sedan hämta och installera MySQL-Server och VMWare PowerCLI.

    ![Sedan kan kontrollera de serverinställningarna.](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Efter valideringen kan ange de FQDN eller IP-adress för vCenter-servern eller vSphere-värden. De lämnar standardporten och anger ett eget namn för vCenter-servern i Azure. De måste du ange det konto som de har skapat tidigare, så att Site Recovery kan automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. De anger du autentiseringsuppgifterna för att automatiskt installera Mobilitetstjänsten när replikering har aktiverats.

    - Kontot måste ha lokal administratörsbehörighet på de virtuella datorerna för Windows-datorer.
    - När registreringen är klar, i Azure-portalen, Contoso dubbla kontrollerar att konfigurationsservern och VMware-servern visas på den källa i valvet.
    - Identifieringen kan ta 15 minuter eller mer.
    - Site Recovery kan du sedan ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

     ![Konfigurera mål](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**Behöver du mer hjälp?**

- [Contoso måste konfigurera målmiljön för replikeringen.
- [I [Förbered infrastruktur](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances)Target, de väljer målinställningarna.
- [Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i det angivna målet.
- [När källan och målet har konfigurerats, är Contoso redo att skapa en replikeringsprincip och koppla den med konfigurationsservern.



### <a name="set-up-routing"></a>I Förbered infrastrukturreplikeringsinställningarreplikeringsprincipskapa och Associera, de skapar en princip ContosoMigrationPolicy.

De använder standardinställningarna: Tröskelvärde för Replikeringspunktmål: standardvärdet 60 minuter.

- Kvarhållning av återställningspunkt.
- Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperioden är för varje återställningspunkt.
- Frekvens för appkonsekvent ögonblicksbild.
- Som standard på en timme.

1. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas. Koppla replikeringsprincip

    ![Routningstabell](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Du kan läsa en genomgång av de här stegen i **konfigurera haveriberedskap för lokala virtuella VMware-datorer**.

    ![Detaljerade instruktioner finns som hjälper dig att konfigurera källmiljön, distribuera konfigurationsservern, och konfigurera replikeringsinställningar.](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso kan nu börja replikera WebVM. 

    ![I replikera programkälla+ replikera de Välj inställningar för datakälla.](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**Behöver du mer hjälp?**

[De anger att de vill aktivera virtuella datorer, Välj vCenter-servern och konfigurationsservern.

### <a name="create-a-managed-instance"></a>Nu kan ange de Målinställningar, inklusive resursgruppen och nätverk där den virtuella Azure-datorn kommer att finnas efter redundansväxling och lagringskontot som replikerade data ska lagras.

Contoso väljer WebVM för replikering.

1. Site Recovery installerar Mobilitetstjänsten på varje virtuell dator när du aktiverar replikering för den. 
2. SmartHotel appen körs på en virtuell Azure-dator med migreringen har slutförts, och SmartHotel databasen är tillgänglig på Azure SQL Managed Instance. [Nu kan måste Contoso göra vissa rensning på följande sätt:

    ![Hanterad instans](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Ta bort den WEBVM datorn från vCenter-lagret.

    - Ta bort den SQLVM datorn från vCenter-lagret.
    - Ta bort WEBVM och SQLVM från lokala säkerhetskopieringsjobb. 

    ![Hanterad instans](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**Behöver du mer hjälp?**

[Uppdatera interna dokumentationen visar den nya platsen, IP-adress för WEBVM.

## <a name="step-2-prepare-dms"></a>Ta bort SQLVM-dokumentationen.

De kan också markera det om du vill visa tagits bort och inte längre i den virtuella datorn inventering.

- Contoso security-teamet granskar de virtuella datorer i Azure och SQL-hanterad instans, för att fastställa eventuella säkerhetsproblem med dess implementering.
- De granska Nätverkssäkerhetsgrupper (NSG) för den virtuella datorn för åtkomstkontroll. NSG: er för att säkerställa att endast trafik som tillåts i appen kan skicka. 
- De överväger också skydda data på disken med hjälp av Azure Disk Encryption och Azure KeyVault.


De aktivera hotidentifiering på SQL Managed Instance (SQLMI).

1. De skickar aviseringar till sina team/service desk säkerhetssystem att öppna en biljett om ett hot har identifierats.
    ![Hanterad instans-säkerhet](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Läs mer om säkerhetsrutiner för virtuella datorer.

    ![CONTOSO KOMMER ATT SÄKERHETSKOPIERA DATA PÅ WEBVM MED AZURE BACKUP-TJÄNSTEN.](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Contoso har befintliga licenser för WEBVM och att utnyttja Azure Hybrid-förmånen. 

    ![De konverterar befintliga Azure-VM för att dra nytta av den här prissättning.](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser.
    - I den här artikeln rehosted Contoso SmartHotel-app i Azure genom att migrera app-klientdelens VM till Azure med Site Recovery-tjänsten.
    - De migrerade en lokal databas till en Azure SQL Managed Instance med DMS.  I nästa artikel i serien som, visar vi hur Contoso rehost SmartHotel-app på virtuella Azure-datorer med hjälp av tjänsten Azure Site Recovery.

        ![DMS-nätverk](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**Behöver du mer hjälp?**
- [Lär dig mer om](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) konfigurerar DMS.
- [Läs mer](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2) om att skapa och med hjälp av SAS.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Steg 3: Förbereda Azure för Site Recovery-tjänsten

Det finns ett antal Azure element Contoso behöver för att ställa in Site Recovery för migrering av deras webbnivå VM (WEBMV)

- Ett virtuellt nätverk i vilket växlas över resurserna finns.
- Ett Azure storage-konto för att lagra replikerade data. 
- Ett Recovery Services-valv i Azure.

De har konfigurerat Site Recovery på följande sätt:

1. Eftersom den virtuella datorn är en webbservergrupp till appen SmartHotel, kommer de redundansväxla den virtuella datorn till sina befintliga produktionsnätverk (VNET-PROD-EUS2) och ett undernät (PROD-FE-EUS2), i den primära regionen östra USA 2. De har det här nätverket sig när de [distribuerade Azure-infrastrukturen](contoso-migration-infrastructure.md)
2. De skapar ett Azure storage-konto (contosovmsacc20180528). De använder ett konto för generell användning med standardlagring och LRS-replikering.

    ![Site Recovery-lagring](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Med nätverks- och storage-kontot på plats, kan Contoso nu skapa ett valv (ContosoMigrationVault) och placera den i resursgruppen ContosoFailoverRG på den primära regionen östra USA 2.

    ![Recovery Services-valv](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) ställa in Azure Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Steg 4: Förbereda lokala VMware för Site Recovery

För att förbereda VMware för Site Recovery, är här vad Contoso måste göra:

- Förbereda ett konto på vCenter-servern eller vSphere ESXi-värden, att automatisera VM-identifiering.
- Förbereda ett konto som tillåter automatisk installation av mobilitetstjänsten på virtuella VMware-datorer som du vill replikera.
- Förbereda lokala virtuella datorer, så att de kan ansluta till virtuella Azure-datorer när de skapas efter en redundansväxling.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Identifiera automatiskt virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som att skapa och ta bort diskar och aktivering av virtuella datorer.

Contoso ställer in kontot enligt följande:

1. Skapar en roll på vCenter-nivå.
2. Tilldelar behörigheterna som krävs till rollen.

**Behöver du mer hjälp?**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) skapa och tilldela en roll för automatisk identifiering.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på den virtuella dator som du vill replikera.

- Site Recovery kan göra en automatisk push-installation av den här komponenten när du aktiverar replikering för den virtuella datorn.
- För automatisk push-installation måste du förbereda ett konto som Site Recovery använder för att få åtkomst till den virtuella datorn.
- Du anger det här kontot när du konfigurerar replikering i Azure-konsolen.
- Du behöver en domän eller lokalt konto med behörighet att installera på den virtuella datorn.

**Behöver du mer hjälp**

[Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) skapar ett konto för push-installation av mobilitetstjänsten.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Contoso vill kunna ansluta till de replikerade virtuella datorerna i Azure efter redundansväxlingen till Azure. Detta gör att det finns några saker som de behöver göra på den lokala virtuella datorn innan de kör migreringen: 

1. De aktiverar du RDP på den lokala virtuella datorn före redundans för åtkomst via internet, och ser till att TCP och UDP-regler lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen**  >  **Tillåtna appar** för alla profiler.
2. För åtkomst via deras plats-till-plats-VPN, de aktiverar du RDP på den lokala datorn och Tillåt RDP i den **Windows-brandväggen** -> **tillåtna appar och funktioner** för **domän och Privata** nätverk.
3. De ange operativsystemets SAN-princip på den lokala virtuella datorn till **OnlineAll**.

Dessutom när de kör en redundans måste de du kontrollera följande:

- Det bör finnas inga Windows-uppdateringar som väntar på den virtuella datorn när du utlöser en redundans. Om det finns, kan de inte logga in på den virtuella datorn förrän uppdateringen är klar.
- Efter redundansväxlingen bör de kontrollera **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om det inte fungerar de bör kontrollera att den virtuella datorn körs och granska sedan dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Steg 5: Replikera lokala virtuella datorer till Azure med Site Recovery

Innan du kör en migrering till Azure måste Contoso konfigurerar replikering och aktiverar replikering för sina lokala virtuella datorn.

### <a name="set-a-replication-goal"></a>Ange ett replikeringsmål

1. I valvet under valvnamnet (ContosoVMVault) de ange ett replikeringsmål (**komma igång** > **Site Recovery** > **Förbered infrastruktur**.
2. De ange som finns lokalt på sina datorer, att de är virtuella VMware-datorer och att de vill replikera till Azure.

    ![Replikeringsmål](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Om du vill fortsätta, de behöver för att bekräfta att de har slutfört distributionsplanering genom att välja **Ja, jag har gjort det**. I den här distributionen Contoso är bara migrera en enskild virtuell dator och behöver inte distributionsplanering.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Nu måste konfigurera sina källmiljö. Om du vill de ladda ned en OVF-mall och använda den för att distribuera konfigurationsservern och dess associerade komponenter som en högtillgänglig, en lokal VMware VM. Komponenterna på servern inkluderar:

- Konfigurationsservern som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processervern som fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.
- När du konfigurationsservern VM skapas och startas, registrera Contoso den i valvet.


Contoso utför de här stegen på följande sätt:

1. De ladda ner OVF-mall från Azure-portalen (**förbereda infrastrukturen** > **källa** > **konfigurationsservern**).
    
    ![Ladda ner OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. De importerar mallen till VMware för att skapa och distribuera den virtuella datorn.

    ![OVF-mall](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  När de aktiverar på den virtuella datorn för första gången startar den i en Windows Server 2016-installationsproceduren. De acceptera licensavtalet och ange ett administratörslösenord.
4. När installationen är klar, de loggar in på den virtuella datorn som administratör. Vid första gången inloggning körs Azure Site Recovery-konfigurationsverktyget som standard.
5. I verktyget Ange de ett namn som du använder när du registrerar konfigurationsservern i valvet.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats kan de välja **logga in**, för att logga in på Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv som konfigurationsservern ska registreras. 

    [Registrera konfigurationsservern](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn. De loggar in på datorn igen och i guiden Konfigurera serverhantering startar automatiskt.
8. I guiden Välj de nätverkskort för att ta emot replikeringstrafiken. Den här inställningen kan inte ändras när den har konfigurerats.
9. De Välj prenumeration, resursgrupp och valv där du vill registrera konfigurationsservern.
        ![Valvet](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. De och sedan hämta och installera MySQL-Server och VMWare PowerCLI. Sedan kan kontrollera de serverinställningarna.
11. Efter valideringen kan ange de FQDN eller IP-adress för vCenter-servern eller vSphere-värden. De lämnar standardporten och anger ett eget namn för vCenter-servern i Azure.
12. De måste du ange det konto som de har skapat tidigare, så att Site Recovery kan automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. 
13. De anger du autentiseringsuppgifterna för att automatiskt installera Mobilitetstjänsten när replikering har aktiverats. Kontot måste ha lokal administratörsbehörighet på de virtuella datorerna för Windows-datorer. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. När registreringen är klar, i Azure-portalen, Contoso dubbla kontrollerar att konfigurationsservern och VMware-servern visas på den **källa** i valvet. Identifieringen kan ta 15 minuter eller mer. 
8. Site Recovery kan du sedan ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

### <a name="set-up-the-target"></a>Konfigurera mål

Contoso måste konfigurera målmiljön för replikeringen.

1. I **Förbered infrastruktur** > **Target**, de väljer målinställningarna.
2. Site Recovery kontrollerar att det finns ett Azure storage-konto och nätverk i det angivna målet.

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

När källan och målet har konfigurerats, är Contoso redo att skapa en replikeringsprincip och koppla den med konfigurationsservern.

1. I **Förbered infrastruktur** > **replikeringsinställningar** > **replikeringsprincip** >  **skapa och Associera**, de skapar en princip **ContosoMigrationPolicy**.
2. De använder standardinställningarna:
    - **Tröskelvärde för Replikeringspunktmål**: standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
    - **Kvarhållning av återställningspunkt**. Standardvärdet 24 timmar. Det här värdet anger hur länge kvarhållningsperioden är för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
    - **Frekvens för appkonsekvent ögonblicksbild**. Som standard på en timme. Det här värdet anger med vilken frekvens vid vilken programkonsekventa ögonblicksbilder skapas.
 
        ![Skapa replikeringsprincip](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincip](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**Behöver du mer hjälp?**

- Du kan läsa en genomgång av de här stegen i [konfigurera haveriberedskap för lokala virtuella VMware-datorer](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- Detaljerade instruktioner finns som hjälper dig att [konfigurera källmiljön](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [distribuera konfigurationsservern](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), och [konfigurera replikeringsinställningar](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Aktivera replikering

Contoso kan nu börja replikera WebVM.

1. I **replikera program** > **källa** > **+ replikera** de Välj inställningar för datakälla.
2. De anger att de vill aktivera virtuella datorer, Välj vCenter-servern och konfigurationsservern.

 ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Nu kan ange de Målinställningar, inklusive resursgruppen och nätverk där den virtuella Azure-datorn kommer att finnas efter redundansväxling och lagringskontot som replikerade data ska lagras.

     ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso väljer WebVM för replikering. Site Recovery installerar Mobilitetstjänsten på varje virtuell dator när du aktiverar replikering för den. 

    ![Aktivera replikering](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso kontrollerar att rätt replikeringsprincip har valts och aktiverar replikering för WEBVM. De spåra Replikeringsförlopp på **jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.
6. I **Essentials** i Azure-portalen, Contoso kan se strukturen för de virtuella datorerna replikeras till Azure.

    ![Infrastruktur-vy](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**Behöver du mer hjälp?**

Du kan läsa en genomgång av de här stegen i [Aktivera replikering](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>Steg 6: Migrera databasen med DMS

Contoso behöver skapa ett DMS-projekt och migrera databasen.

### <a name="create-a-dms-project"></a>Skapa ett projekt med DMS
1. Skapar de ett DMS-projekt. De anger typ av källserver som SQL Server, och mål som Azure SQL Database Managed Instance.

     ![DMS-projekt](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. När du har skapat projektet öppnas guiden.

### <a name="migrate-the-database"></a>Migrera databasen 

1. I guiden Migrera anger Contoso du den Virtuella källdatorn som en lokal databas finns och autentiseringsuppgifterna för att komma åt den.

    ![DMS-källa](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. De Välj databas att migrera (SmartHotel.Registration).

    ![DMS källdatabasen](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Som ett mål ange de namnet på den hanterade instansen i Azure och autentiseringsuppgifter.

    ![DMS-mål](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. I **+ ny aktivitet** > **kör migreringen**, de anger inställningar för att köra migreringen:
    - Autentiseringsuppgifter för källa och mål.
    - Databas som ska migreras.
    - Nätverksresursen som de skapat på den lokala virtuella datorn. DMS tar källa säkerhetskopior till den här resursen.
        - Tjänstkontot som kör SQL Server-källans instansen måste ha skrivbehörighet på den här resursen.
        - Ange FQDN-sökvägen till resursen.
    - SAS-URI som ger DMS tillgång till lagringskontobehållare som tjänsten Överför säkerhetskopieringsfilerna för migrering.

        ![DMS-inställningar](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. De spara migreringen och kör den.
6. I **översikt**, de övervaka Migreringsstatus för.

    ![DMS-Övervakare](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. De kontrollerar att måldatabaserna finns på den hanterade instansen när migreringen har slutförts.

    ![DMS-Övervakare](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>Steg 7: Migrera den virtuella datorn med Site Recovery

Contoso körs en snabb testredundans och sedan migrera den virtuella datorn.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

Innan du migrerar WEBVM, ett redundanstest kan du kontrollera att allt fungerar som förväntat. 

1. De kör ett redundanstest till den senaste tillgängliga tidpunkten (**senaste bearbetade**).
2. De väljer **Stäng datorn innan du påbörjar redundans**, så att Site Recovery försöker stänga av den Virtuella källdatorn innan redundansen. Redundansväxlingen fortsätter även om avstängningen misslyckas. 
3. Testa redundans körs: 

    - En kravkontroll körs för att kontrollera att alla de villkor som krävs för migrering är på plats.
    - Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
    - En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.
3. När redundansen är klar visas repliken virtuell Azure-dator i Azure-portalen. De kontrollera att allt fungerar korrekt. Den virtuella datorn har rätt storlek, den är ansluten till rätt nätverk och den körs. 
4. När du har verifierat redundanstestningen Contoso rensar redundans och poster och sparar eventuella observationer. 

### <a name="migrate-the-vm"></a>Migrera den virtuella datorn

1. Efter att ha kontrollerat att redundanstestningen fungerar som förväntat, skapar Contoso du en återställningsplan för migrering. De lägger till WEBVM planen.

     ![Återställningsplan](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. De kör en redundansväxling på planen. De väljer du den senaste återställningspunkten och ange att Site Recovery bör försöka att stänga av den lokala virtuella datorn innan du utlöser redundansväxlingen.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Efter redundansen, Contoso kontrollerar du att den virtuella Azure-datorn visas som förväntat i Azure-portalen.

   ![Återställningsplan](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. När du har verifierat den virtuella datorn i Azure, slutföra de migreringen du slutför migreringsprocessen, stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-debitering för den virtuella datorn.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Uppdatera anslutningssträngen

Contoso uppdaterar anslutningssträngen för programmet för att peka på den migrerade databasen som körs på sin SQL-MI som det sista steget i migreringen.

1. I Azure-portalen kan de hitta anslutningssträngen genom att klicka på **inställningar** > **anslutningssträngar**.

    ![Redundans](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. De uppdatera strängen med användarnamn och lösenord för SQL-hanterad instans.
3. När strängen har konfigurerats kan ersätta de den aktuella anslutningssträngen i filen web.config för sina program.
4. Starta om IIS på WEBVM när du uppdaterar filen och spara den, de. Användarna kan göra detta med **IISRESET /RESTART** från en kommandotolk.
5. När IIS har startats, kommer appen att använda den databas som körs på SQL-hanterad instans.
6. I det här läget Contoso kan stänga av de SQLVM datorn lokalt och migreringen är klar.

**Behöver du mer hjälp?**

- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) som kör ett redundanstest. 
- [Lär dig](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) så här skapar du en återställningsplan.
- [Lär dig mer om](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) redundansväxla till Azure.

## <a name="clean-up-after-migration"></a>Rensa upp efter migreringen

SmartHotel appen körs på en virtuell Azure-dator med migreringen har slutförts, och SmartHotel databasen är tillgänglig på Azure SQL Managed Instance.  

Nu kan måste Contoso göra vissa rensning på följande sätt:  

- Ta bort den WEBVM datorn från vCenter-lagret.
- Ta bort den SQLVM datorn från vCenter-lagret.
- Ta bort WEBVM och SQLVM från lokala säkerhetskopieringsjobb.
- Uppdatera interna dokumentationen visar den nya platsen, IP-adress för WEBVM.
- Ta bort SQLVM-dokumentationen. De kan också markera det om du vill visa tagits bort och inte längre i den virtuella datorn inventering.
- Granska eventuella resurser som interagerar med de inaktiverade virtuella datorerna och uppdatera alla relevanta inställningar eller dokumentation för att återspegla den nya konfigurationen.

## <a name="review-the-deployment"></a>Granska distributionen

Med de migrerade resurserna i Azure måste Contoso fullständigt operationalisera och skydda sina ny infrastruktur.

### <a name="security"></a>Säkerhet

Contoso security-teamet granskar de virtuella datorer i Azure och SQL-hanterad instans, för att fastställa eventuella säkerhetsproblem med dess implementering.

- De granska Nätverkssäkerhetsgrupper (NSG) för den virtuella datorn för åtkomstkontroll. NSG: er för att säkerställa att endast trafik som tillåts i appen kan skicka.
- De överväger också skydda data på disken med hjälp av Azure Disk Encryption och Azure KeyVault.
- De aktivera hotidentifiering på SQL Managed Instance (SQLMI). De skickar aviseringar till sina team/service desk säkerhetssystem att öppna en biljett om ett hot har identifierats. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Hanterad instans-säkerhet](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[Läs mer](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) om säkerhetsrutiner för virtuella datorer.

### <a name="backups"></a>Säkerhetskopior
Contoso kommer att säkerhetskopiera data på WEBVM med Azure Backup-tjänsten. [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licensierings- och optimering

1. Contoso har befintliga licenser för WEBVM och att utnyttja Azure Hybrid-förmånen.  De konverterar befintliga Azure-VM för att dra nytta av den här prissättning.
2. Contoso kan Azure Cost Management licensieras av Cloudyn, ett dotterbolag till Microsoft. Det är en kostnadshanteringslösning med flera moln-hanteringslösning som hjälper dig att utnyttja och hantera Azure och andra molnresurser.  [Läs mer](https://docs.microsoft.com/azure/cost-management/overview) om Azure Cost Management. 


## <a name="conclusion"></a>Sammanfattning

I den här artikeln rehosted Contoso SmartHotel-app i Azure genom att migrera app-klientdelens VM till Azure med Site Recovery-tjänsten. De migrerade en lokal databas till en Azure SQL Managed Instance med DMS.

## <a name="next-steps"></a>Nästa steg

I nästa artikel i serien som, visar vi hur Contoso rehost SmartHotel-app på virtuella Azure-datorer med hjälp av tjänsten Azure Site Recovery.

