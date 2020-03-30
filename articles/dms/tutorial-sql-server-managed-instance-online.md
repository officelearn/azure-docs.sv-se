---
title: 'Självstudiekurs: Migrera SQL Server online till en SQL-hanterad instans'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en onlinemigrering från SQL Server lokalt till en Azure SQL Database-hanterad instans med hjälp av Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 236c68b3c26049073d3e6e942ce2a6be8b7f4fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298894"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Självstudiekurs: Migrera SQL Server till en Hanterad Azure SQL-databas-hanterad instans online med DMS

Du kan använda Azure Database Migration Service för att migrera databaserna från en lokal SQL Server-instans till en [Azure SQL Database-hanterad instans](../sql-database/sql-database-managed-instance.md) med minimal stilleståndstid. Mer information om metoder som kan kräva viss manuell ansträngning finns i artikeln [SQL Server-instansmigrering till Azure SQL Database-hanterad instans](../sql-database/sql-database-managed-instance-migrate.md).

I den här självstudien migrerar du **Adventureworks2012-databasen** från en lokal instans av SQL Server till en SQL Database-hanterad instans med minimal stilleståndstid med hjälp av Azure Database Migration Service.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
>
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt och starta onlinemigrering med hjälp av Azure Database Migration Service.
> * Övervaka migreringen.
> * Utför migreringsöverskärningen när du är redo.

> [!IMPORTANT]
> För onlinemigreringar från SQL Server till SQL Database-hanterad instans med Azure Database Migration Service måste du tillhandahålla fullständig säkerhetskopiering av databasen och efterföljande loggsäkerhetskopior i SMB-nätverksresursen som tjänsten kan använda för att migrera dina databaser. Azure Database Migration Service initierar inte några säkerhetskopior och använder i stället befintliga säkerhetskopior, som du kanske redan har som en del av din haveriberedskapsplan, för migreringen.
> Se till att du tar [säkerhetskopior med alternativet MED KONTROLLSUMMA](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Se också till att inte lägga till flera säkerhetskopior (dvs. fullständig och t-log) i ett enda säkerhetskopieringsmedia. varje säkerhetskopia på en separat säkerhetskopia. Slutligen kan du använda komprimerade säkerhetskopior för att minska risken för att du får potentiella problem med att migrera stora säkerhetskopior.

> [!NOTE]
> För att använda Azure Database Migration Service för att utföra en onlinemigrering måste du skapa en instans baserat på premiumprisnivån.

> [!IMPORTANT]
> För en optimal migreringsupplevelse rekommenderar Microsoft att du skapar en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

> [!IMPORTANT]
> Minska varaktigheten för onlinemigreringsprocessen så mycket som möjligt för att minimera risken för avbrott som orsakas av instansomkonfiguration eller planerat underhåll. I händelse av en sådan händelse startar migreringsprocessen från början. Vid planerat underhåll finns det en respitperiod på 36 timmar innan migreringsprocessen startas om.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den här artikeln beskrivs en onlinemigrering från SQL Server till en SQL Database-hanterad instans. En offlinemigrering finns i [Migrera SQL Server till en SQL Database-hanterad instans offline med DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Skapa en Microsoft Azure Virtual Network for Azure Database Migration Service med hjälp av distributionsmodellen för Azure Resource Manager, som tillhandahåller anslutning från plats till plats till dina lokala källservrar med hjälp av [Antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Lär dig nätverkstopologier för hanterade instansmigreringar i Azure SQL Database med Hjälp av Azure Database Migration Service](https://aka.ms/dmsnetworkformi). Mer information om hur du skapar ett virtuellt nätverk finns i [dokumentationen](https://docs.microsoft.com/azure/virtual-network/)till det virtuella nätverket och särskilt snabbstartsartiklarna med steg-för-steg-information.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks peering i Microsoft under installationen av virtuella nätverk lägger du till följande [tjänstslutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) i undernätet där tjänsten ska etableras:
    >
    > * Slutpunkt för måldatabas (till exempel SQL-slutpunkt, Cosmos DB-slutpunkt och så vidare)
    > * Slutpunkt för lagring
    > * Ändpunkt för servicebuss
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar internetanslutning.
    >
    >Om du inte har anslutning mellan det lokala nätverket och Azure eller om det finns begränsad anslutningsbandbredd från plats till plats kan du överväga att använda Azure Database Migration Service i hybridläge (förhandsversion). Hybridläge utnyttjar en lokal migreringsarbetare tillsammans med en instans av Azure Database Migration Service som körs i molnet. Information om hur du skapar en instans av Azure Database Migration Service i hybridläge läser du artikeln [Skapa en instans av Azure Database Migration Service i hybridläge med Azure-portalen](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > När det gäller lagringskontot som används som en del av migreringen måste du antingen:
    > * Välj att tillåta alla nätverk att komma åt lagringskontot.
    > * Aktivera [undernätsdelegering](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) i MI-undernätet och uppdatera brandväggsreglerna för lagringskonto så att det här undernätet tillåts.

* Kontrollera att dina regler för nätverkssäkerhetsgrupp för virtuella nätverk inte blockerar följande inkommande kommunikationsportar till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om filtrering av NSG-trafik i det virtuella nätverket finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Konfigurera din [Windows-brandvägg för källdatabasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen så att Azure Database Migration Service kan komma åt källan SQL Server, som som standard är TCP-port 1433.
* Om du kör flera namngivna SQL Server-instanser med dynamiska portar kanske du vill aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via brandväggarna så att Azure Database Migration Service kan ansluta till en namngiven instans på källan Server.
* Om du använder en brandväggsinstallation framför källdatabaserna kan du behöva lägga till brandväggsregler så att Azure Database Migration Service kan komma åt källdatabaserna för migrering samt filer via SMB-port 445.
* Skapa en SQL Database-hanterad instans genom att följa detaljerna i artikeln [Skapa en Hanterad Azure SQL-databas-hanterad instans i Azure-portalen](https://aka.ms/sqldbmi).
* Se till att inloggningarna som används för att ansluta SQL Server-källan och den hanterade målinstansen är medlemmar av sysadmin-serverrollen.
* Ange en SMB-nätverksresurs som innehåller alla fullständiga databassäkerhetskopior och efterföljande säkerhetskopieringsfiler för transaktionslogg, som Azure Database Migration Service kan använda för databasmigrering.
* Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörighet på nätverksresursen som du har skapat och att datorkontot för källservern har läs-/skrivåtkomst till samma resurs.
* Anteckna en Windows-användare (och lösenordet) som har fullständig kontrollbehörighet på nätverksresursen som du tidigare har skapat. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att ladda upp säkerhetskopior till Azure Storage-behållaren för återställning.
* Skapa ett Azure Active Directory-program-ID som genererar den program-ID-nyckel som Azure Database Migration Service kan använda för att ansluta till målad Azure-databashanterad instans och Azure Storage Container. Mer information finns i artikeln [Använda portalen för att skapa ett Azure Active Directory-program och ett huvudnamn för tjänsten som får åtkomst till resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > Azure Database Migration Service kräver deltagarbehörighet för prenumerationen för det angivna program-ID:t. Alternativt kan du skapa anpassade roller som ger de specifika behörigheter som Azure Database Migration Service kräver. Stegvis vägledning om hur du använder anpassade roller finns i artikeln [Anpassade roller för SQL Server till SQL Database hanterade instansanslutna migreringar](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Skapa eller anteckna **Standard Performance-nivå** och Azure Storage-konto, som DMS-tjänsten kan överföra de säkerhetskopierade databasfilerna till och använda för att migrera databaser.  Se till att skapa Azure Storage-kontot i samma region som Azure Database Migration Service-instansen skapas.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

    ![Visa portalprenumerationer](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **Resursleverantörer**.

    ![Visa resursprovidrar](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera**till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure Database Migration Service-instans

1. I Azure-portalen väljer du + **Skapa en resurs**, söker efter Azure Database Migration **Service**och väljer sedan Azure Database **Migration Service** i listrutan.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av DMS.

5. Välj ett befintligt virtuellt nätverk eller skapa ett.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till källan SQL Server och rikta SQL Database hanterad instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [Skapa ett virtuellt nätverk med Azure-portalen](https://aka.ms/DMSVnet).

    Mer information finns i artikeln [Nätverkstopologier för Azure SQL Database-hanterade instansmigreringar med Hjälp av Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Välj en SKU på Premium-prisnivån.

    > [!NOTE]
    > Onlinemigreringar stöds bara när du använder Premium-nivån.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Skapa DMS-tjänst](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När en instans av tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta reda på alla instanser av Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. På **Azure Database Migration Service-sidan** söker du efter namnet på instansen som du har skapat och väljer sedan instansen.

3. Välj + **Nytt migreringsprojekt**.

4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet. I textrutan **Typ av källserver** väljer du **SQL Server**, i textrutan **Målservertyp** väljer du **Hanterad Azure SQL Database-instans** och sedan för **Välj typ av aktivitet** väljer du **Online-datamigrering**.

   ![Skapa Azure Database Migration Service Project](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Välj **Skapa och kör aktivitet** för att skapa projektet.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källan.

2. Om du inte har installerat ett betrott certifikat på servern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > TLS-anslutningar som krypteras med ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte förlita dig på TLS med självsignerade certifikat i en produktionsmiljö eller på servrar som är anslutna till Internet.

   ![Källinformation](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Välj **Spara**.

4. På sidan **Välj källdatabaser**, välj databsen **Adventureworks2012** för migrering.

   ![Välj källdatabaser](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Om du använder SQL Server Integration Services (SSIS) stöder DMS för närvarande inte migrering av katalogdatabasen för dina SSIS-projekt/-paket (SSISDB) från SQL Server till en hanterad Azure SQL Database-instans. Du kan dock etablera SSIS i Azure Data Factory (ADF) och distribuera om dina SSIS-projekt/-paket till den mål-SSISDB som ligger i den hanterade Azure SQL Database-instansen. Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).

5. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På skärmen **Information om migreringsmål** anger du **program-ID** och **nyckel** som DMS-instansen kan använda för att ansluta till målinstansen av Azure SQL Database-hanterad instans och Azure Storage-kontot.

    Mer information finns i artikeln [Använda portalen för att skapa ett Azure Active Directory-program och ett huvudnamn för tjänsten som får åtkomst till resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Välj **prenumerationen** som innehåller målinstansen för hanterad azure SQL-databas-hanterad instans och välj sedan målinstansen.

    Om du inte redan har etablerat den hanterade instansen för Azure SQL Database väljer du [länken](https://aka.ms/SQLDBMI) som hjälper dig att etablera instansen. När den hanterade Azure SQL Database-instansen är klar återgår du till det här specifika projektet för att utföra migreringen.

3. Ange **SQL-användare** och **lösenord** för att ansluta till den hanterade Azure SQL Database-instansen.

    ![Välja mål](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Välj **Spara**.

## <a name="select-source-databases"></a>Välj källdatabaser

1. På sidan **Välj källdatabaser** väljer du de källdatabaser som du vill migrera.

    ![Välj källdatabaser](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Välj **Spara**.

## <a name="configure-migration-settings"></a>Konfigurera migreringsinställningar

1. På sidan **Konfigurera migreringsinställningar** anger du följande information:

    | | |
    |--------|---------|
    |**SMB-nätverksplatsresurs** | Den lokala SMB-nätverksresursen eller Azure-filresursen som innehåller de fullständiga säkerhetskopieringsfilerna för databasen och säkerhetskopieringsfiler för transaktionslogg som Azure Database Migration Service kan använda för migrering. Tjänstkontot som kör en SQL Server-källinstansen måste ha läs-/skrivbehörighet på den här nätverksresursen. Ange ett fullständigt domännamn eller IP-adresser för servern i nätverksresursen, till exempel \\\servernamn.domännamn.com\säkerhetskopieringsmapp eller \\\IP-adress\säkerhetskopieringsmapp. För bättre prestanda rekommenderas att du använder separat mapp för varje databas som ska migreras. Du kan ange sökvägen till filresurs på databasnivå med alternativet **Avancerade inställningar.** |
    |**Användarnamn** | Kontrollera att Windows-användaren har fullständig kontrollbehörighet på nätverksresursen du har angett ovan. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att överföra säkerhetskopieringsfilerna till Azure Storage-behållaren för återställning. Om du använder Azure File-resurs använder du lagringskontonamnet som är förnamn som är förnamn med AZURE\ som användarnamn. |
    |**Lösenord** | Lösenordet för användaren. Om du använder Azure-filresurs använder du en lagringskontonyckel som lösenord. |
    |**Prenumeration på Azure Storage-kontot** | Välj den prenumeration som innehåller Azure Storage-kontot. |
    |**Azure-lagringskonto** | Välj det Azure Storage-konto som DMS kan överföra de säkerhetskopierade filerna från SMB-nätverksresursen till och använda för databasmigrering.  Vi rekommenderar att du väljer Storage-kontot i samma region som DMS-tjänsten för bästa möjliga prestanda vid filöverföring. |

    ![Konfigurera migreringsinställningar](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Om Azure Database Migration Service visar felet "SystemFel 53" eller "SystemFel 57", kan orsaken bero på en oförmåga azure database migrationstjänst att komma åt Azure-filresurs. Om du stöter på något av dessa fel kan du bevilja åtkomst till lagringskontot från det virtuella nätverket med hjälp av instruktionerna [här](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Om loopback check-funktionen är aktiverad och källan SQL Server och filresurs finns på samma dator, kan källan inte komma åt filharen med FQDN. Lös problemet genom att inaktivera loopback check-funktionen med hjälp av instruktionerna [här](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

2. Välj **Spara**.

## <a name="review-the-migration-summary"></a>Granska migreringssammanfattningen

1. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

2. Granska och verifiera informationen som är kopplad till migreringsprojektet.

    ![Sammanfattning av migreringsprojekt](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Köra och övervaka migreringen

1. Välj **Kör migrering**.

2. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen.

   ![Migreringsaktivitet pågår](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Du kan expandera databaserna och inloggningskategorierna ytterligare för att övervaka migreringsstatusen för respektive serverobjekt.

   ![Migreringsaktivitet pågår](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga säkerhetskopieringen av databasen har återställts på målinstansen för SQL Database-hanterad instans är databasen tillgänglig för att utföra en migreringsöverskärning.

1. När du är redo att slutföra databasmigreringen online väljer du **Starta snabb**.

2. Stoppa all inkommande trafik till källdatabaser.

3. Ta [säkerhetskopia av slutlogg], gör säkerhetskopian tillgänglig i SMB-nätverksresursen och vänta sedan tills säkerhetskopieringen av den sista transaktionsloggen har återställts.

    I det här läget visas **Väntande ändringar** med värdet 0.

4. Välj **Bekräfta** och sedan **Använd**.

    ![Förbereda för att slutföra startpunkt](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. När databasmigreringsstatusen visar **Slutförd**ansluter du dina program till den nya målinstansen av Azure SQL Database-hanterad instans.

    ![Startpunkt slutförd](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Nästa steg

* En självstudiekurs som visar hur du migrerar en databas till en hanterad instans med kommandot T-SQL RESTORE finns i [Återställa en säkerhetskopia till en hanterad instans med kommandot Återställ](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Information om hanterad instans finns i [Vad är en hanterad instans](../sql-database/sql-database-managed-instance.md).
* Information om hur du ansluter appar till en hanterad instans finns i [Ansluta program](../sql-database/sql-database-managed-instance-connect-app.md).
