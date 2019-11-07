---
title: 'Självstudie: använda Azure Database Migration Service för att utföra en online-migrering av SQL Server till en Azure SQL Database Hanterad instans | Microsoft Docs'
description: Lär dig att utföra en online-migrering från SQL Server lokalt till en Azure SQL Database Hanterad instans genom att använda Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: 556fb2c1caf9c763cf5a63b71d3dd1e522104e1d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646951"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Självstudie: Migrera SQL Server till en Azure SQL Database Hanterad instans online med DMS

Du kan använda Azure Database Migration Service för att migrera databaserna från en lokal SQL Server instans till en [Azure SQL Database Hanterad instans](../sql-database/sql-database-managed-instance.md) med minimal stillestånds tid. Information om ytterligare metoder som kan kräva manuella insatser finns i artikeln om [migrering av SQL Server-instans till hanterad Azure SQL Database-instans](../sql-database/sql-database-managed-instance-migrate.md).

I den här självstudien migrerar du **Adventureworks2012** -databasen från en lokal instans av SQL Server till en SQL Database Hanterad instans med minimal nedtid genom att använda Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb och starta migrering online genom att använda Azure Database Migration Service.
> * Övervaka migreringen.
> * Utför migreringen start punkt när du är klar.

> [!IMPORTANT]
> För online-migreringar från SQL Server till SQL Database Hanterad instans med Azure Database Migration Service måste du ange den fullständiga säkerhets kopieringen av databasen och efterföljande logg säkerhets kopior i SMB-nätverks resursen som tjänsten kan använda för att migrera dina databaser. Azure Database Migration Service initierar inga säkerhets kopior och använder istället befintliga säkerhets kopior, som du kanske redan har som en del av Disaster Recovery-planen för migreringen.
> Se till att du vidtar [säkerhets kopior med alternativet med kontroll Summa](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Se också till att du inte lägger till flera säkerhets kopior (t. ex. fullständig och t-logg) i ett enda säkerhets kopierings medium. ta varje säkerhets kopia på en separat säkerhets kopierings fil. Slutligen kan du använda komprimerade säkerhets kopieringar för att minska sannolikheten för problem med att migrera stora säkerhets kopior.

> [!NOTE]
> Om du använder Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans utifrån pris nivån Premium.

> [!IMPORTANT]
> För en optimal migrering rekommenderar Microsoft att du skapar en instans av Azure Database Migration Service i samma Azure-region som mål databasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den här artikeln beskrivs en online-migrering från SQL Server till en SQL Database Hanterad instans. En offline-migrering finns i [migrera SQL Server till en SQL Database Hanterad instans offline med DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* Skapa ett Azure-Virtual Network (VNet) för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Lär dig mer om nätverkstopologier för Azure SQL Database migrering av hanterade instanser med Azure Database migration service](https://aka.ms/dmsnetworkformi). Mer information om hur du skapar ett VNet finns i [Virtual Network-dokumentationen](https://docs.microsoft.com/azure/virtual-network/)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under VNet-installationen lägger du till följande tjänst [slut punkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) i under nätet där tjänsten ska tillhandahållas:
    >
    > * Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
    > * Lagrings slut punkt
    > * Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar Internet anslutning.
    >
    >Om du inte har plats-till-plats-anslutning mellan det lokala nätverket och Azure, eller om det finns begränsad bandbredd för plats-till-plats-anslutning, bör du överväga att använda Azure Database Migration Service i hybrid läge (för hands version). Hybrid läge utnyttjar en lokal migrering av arbetare tillsammans med en instans av Azure Database Migration Service som körs i molnet. Information om hur du skapar en instans av Azure Database Migration Service i hybrid läge finns i artikeln [skapa en instans av Azure Database migration service i hybrid läge med hjälp av Azure Portal](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > För det lagrings konto som används som en del av migreringen måste du antingen:
    > * Välj att tillåta alla nätverk att komma åt lagrings kontot.
    > * Konfigurera åtkomst kontrol listor för VNet. Mer information finns i artikeln [konfigurera Azure Storage brand väggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

* Kontrol lera att säkerhets grupp reglerna för VNet-nätverket inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om Azure VNet NSG trafik filtrering finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Konfigurera din [Windows-brandvägg för källdatabasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen för att tillåta Azure Database Migration Service åtkomst till käll SQL Server, vilket som standard är TCP-port 1433.
* Om du kör flera namngivna SQL Server instanser med dynamiska portar kanske du vill aktivera tjänsten SQL Browser och tillåta åtkomst till UDP-port 1434 genom brand väggarna så att Azure Database Migration Service kan ansluta till en namngiven instans på din källa servernamn.
* Om du använder en brand Väggs installation framför dina käll databaser kan du behöva lägga till brand Väggs regler för att tillåta Azure Database Migration Service åtkomst till käll databaserna för migrering, samt filer via SMB-port 445.
* Skapa en SQL Database Hanterad instans genom att följa informationen i artikeln [skapa en Azure SQL Database Hanterad instans i Azure Portal](https://aka.ms/sqldbmi).
* Se till att inloggningarna som används för att ansluta SQL Server-källan och den hanterade målinstansen är medlemmar av sysadmin-serverrollen.
* Ange en SMB-nätverks resurs som innehåller alla säkerhets kopior av databasen och de efterföljande säkerhetskopieringsfilerna för transaktions loggen, som Azure Database Migration Service kan använda för migrering av databasen.
* Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörighet på nätverksresursen som du har skapat och att datorkontot för källservern har läs-/skrivåtkomst till samma resurs.
* Anteckna en Windows-användare (och lösenordet) som har fullständig kontrollbehörighet på nätverksresursen som du tidigare har skapat. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att överföra säkerhetskopieringsfilerna till Azure Storage-behållaren för återställnings åtgärden.
* Skapa ett Azure Active Directory program-ID som genererar den program-ID-nyckel som Azure Database Migration Service kan använda för att ansluta till målets hanterade instans i Azure Database och Azure Storage behållare. Mer information finns i artikeln [Använda portalen för att skapa ett Azure Active Directory-program och ett huvudnamn för tjänsten som får åtkomst till resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > Azure Database Migration Service kräver behörigheten deltagare för prenumerationen för angivet program-ID. Du kan också skapa anpassade roller som ger de angivna behörigheterna som Azure Database Migration Service kräver. Steg för steg-anvisningar om hur du använder anpassade roller finns i artikeln [anpassade roller för SQL Server till SQL Database hanterade instanser online-migreringar](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Skapa eller anteckna **Standard Performance-nivå** och Azure Storage-konto, som DMS-tjänsten kan överföra de säkerhetskopierade databasfilerna till och använda för att migrera databaser.  Se till att skapa Azure Storage kontot i samma region som Azure Database Migration Services instansen skapas.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

    ![Visa portalprenumerationer](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **resurs leverantörer**.

    ![Visa resursprovidrar](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure Database Migration Service-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter **Azure Database Migration Service** och väljer sedan **Azure Database Migration Service** i listrutan.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av DMS.

5. Välj ett befintligt VNet eller skapa ett.

    VNet ger Azure Database Migration Service åtkomst till käll SQL Server och mål SQL Database Hanterad instans.

    Mer information om hur du skapar ett VNet i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](https://aka.ms/DMSVnet).

    Mer information finns i artikeln nätverkstopologier [för Azure SQL Database hanterade instanser av migrering med hjälp av Azure Database migration service](https://aka.ms/dmsnetworkformi).

6. Välj en SKU på Premium-prisnivån.

    > [!NOTE]
    > Onlinemigreringar stöds bara när du använder Premium-nivån.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Skapa DMS-tjänst](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När en instans av tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Hitta alla instanser av Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. På sidan för **Azure Database Migration Service** söker du efter namnet för instansen du har skapat och väljer sedan instansen.

3. Välj + **Nytt migreringsprojekt**.

4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet. I textrutan **Typ av källserver** väljer du **SQL Server**, i textrutan **Målservertyp** väljer du **Hanterad Azure SQL Database-instans** och sedan för **Välj typ av aktivitet** väljer du **Online-datamigrering**.

   ![Skapa Azure Database Migration Service-projekt](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Välj **Skapa och kör aktivitet** för att skapa projektet.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källan.

2. Om du inte har installerat ett betrott certifikat på servern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > SSL-anslutningar som har krypterats med ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte lita på SSL som använder självsignerade certifikat i en produktionsmiljö eller på servrar som är anslutna till internet.

   ![Källinformation](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Välj **Spara**.

4. På sidan **Välj källdatabaser**, välj databsen **Adventureworks2012** för migrering.

   ![Välj källdatabaser](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Om du använder SQL Server Integration Services (SSIS) stöder DMS för närvarande inte migrering av katalogdatabasen för dina SSIS-projekt/-paket (SSISDB) från SQL Server till en hanterad Azure SQL Database-instans. Du kan dock etablera SSIS i Azure Data Factory (ADF) och distribuera om dina SSIS-projekt/-paket till den mål-SSISDB som ligger i den hanterade Azure SQL Database-instansen. Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).

5. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På skärmen **Information om migreringsmål** anger du **Program-ID** och **Nyckel** som DMS-instansen kan använda för att ansluta till målinstansen i den hanterade Azure SQL Database-instansen och Azure Storage-kontot.

    Mer information finns i artikeln [Använda portalen för att skapa ett Azure Active Directory-program och ett huvudnamn för tjänsten som får åtkomst till resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Välj den **Prenumeration** som innehåller målinstansen för den hanterade Azure SQL Database-instansen och välj sedan målinstansen.

    Om du inte redan har etablerat den hanterade Azure SQL Database-instansen väljer du [länken](https://aka.ms/SQLDBMI) som hjälper dig att etablera instansen. När den hanterade Azure SQL Database-instansen är klar återgår du till det här specifika projektet för att utföra migreringen.

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
    |**SMB-nätverksplatsresurs** | Den lokala SMB-nätverks resurs som innehåller fullständiga säkerhets kopior av databasen och säkerhets kopior av transaktions loggen som Azure Database Migration Service kan använda för migrering. Tjänstkontot som kör en SQL Server-källinstansen måste ha läs-/skrivbehörighet på den här nätverksresursen. Ange ett fullständigt domännamn eller IP-adresser för servern i nätverksresursen, till exempel \\\servernamn.domännamn.com\säkerhetskopieringsmapp eller \\\IP-adress\säkerhetskopieringsmapp.|
    |**Användarnamn** | Kontrollera att Windows-användaren har fullständig kontrollbehörighet på nätverksresursen du har angett ovan. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att överföra säkerhetskopieringsfilerna till Azure Storage-behållaren för återställnings åtgärden. |
    |**Lösenord** | Lösenordet för användaren. |
    |**Prenumeration på Azure Storage-kontot** | Välj den prenumeration som innehåller Azure Storage-kontot. |
    |**Azure Storage-konto** | Välj det Azure Storage-konto som DMS kan överföra de säkerhetskopierade filerna från SMB-nätverksresursen till och använda för databasmigrering.  Vi rekommenderar att du väljer Storage-kontot i samma region som DMS-tjänsten för bästa möjliga prestanda vid filöverföring. |

    ![Konfigurera migreringsinställningar](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

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

När den fullständiga säkerhets kopieringen av databasen har återställts på mål instansen av SQL Database hanterade instansen är databasen tillgänglig för att utföra en migrerings-start punkt.

1. När du är redo att slutföra databasmigreringen online väljer du **Starta snabb**.

2. Stoppa all inkommande trafik till källdatabaser.

3. Ta [säkerhetskopia av slutlogg], gör säkerhetskopian tillgänglig i SMB-nätverksresursen och vänta sedan tills säkerhetskopieringen av den sista transaktionsloggen har återställts.

    I det här läget visas **Väntande ändringar** med värdet 0.

4. Välj **Bekräfta** och sedan **Använd**.

    ![Förbereda för att slutföra startpunkt](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. När status för databasmigreringen visar **Slutförd** ansluter du dina program till den nya målinstansen för den hanterade Azure SQL Database-instansen.

    ![Startpunkt slutförd](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Nästa steg

* En självstudie om hur du migrerar en databas till en hanterad instans med kommandot T-SQL RESTORE finns i [Återställa en säkerhetskopia till en hanterad instans med kommandot restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Information om hanterade instanser finns i avsnittet [Vad är en hanterad instans?](../sql-database/sql-database-managed-instance.md).
* Information om hur du ansluter appar till en hanterad instans finns i [Ansluta program](../sql-database/sql-database-managed-instance-connect-app.md).
