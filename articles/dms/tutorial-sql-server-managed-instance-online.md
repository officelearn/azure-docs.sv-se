---
title: 'Självstudie: Använda Azure Database Migration Service för att utföra en migrering av SQL Server till Azure SQL Database Managed Instance online | Microsoft Docs'
description: Lär dig hur du gör en onlinemigrering från SQL Server lokalt till Azure SQL Database Managed Instance genom att använda Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: ab869e53810f049593803d58b3df75d0c083bbd2
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962902"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-managed-instance-online-using-dms"></a>Självstudie: Migrera SQL Server till Azure SQL Database Managed Instance online med DMS
Du kan använda Azure Database Migration Service till att migrera databaserna från en lokal SQL Server-instans till [hanterad Azure SQL Database-instans](../sql-database/sql-database-managed-instance.md) med minimal avbrottstid. Information om ytterligare metoder som kan kräva manuella insatser finns i artikeln om [migrering av SQL Server-instans till hanterad Azure SQL Database-instans](../sql-database/sql-database-managed-instance-migrate.md).

>[!IMPORTANT]
>Onlinebaserade migreringsprojekt från SQL Server till hanterad Azure SQL Database-instans är förhandsversioner och omfattas av de [kompletterande användningsvillkoren för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här självstudiekursen migrerar du databasen **Adventureworks2012** från en lokal instans av SQL Server till hanterad Azure SQL Database-instans med minimal avbrottstid genom att använda Azure Database Migration Service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt och starta onlinemigreringen med hjälp av Azure Database Migration Service.
> * Övervaka migreringen.
> * Flytta över migreringen när du är klar.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den artikeln beskrivs en onlinemigrering från SQL Server till hanterad Azure SQL Database-instans. En offlinemigrering beskrivs i [Migrera SQL Server till hanterad Azure SQL Database-instans offline med hjälp av DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här kursen behöver du:

- Skapa virtuellt nätverk för Azure Database Migration Service genom att använda Azure Resource Manager-distributionsmodellen, som ger plats-till-plats-anslutning för dina lokala källservrar genom att använda [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Lär dig nätverkstopologier för migreringar av hanterade Azure SQL DB-instanser med Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Kontrollera att dina regler för nätverkssäkerhetsgrupp (NSG) för Azure Virtual Network (VNET) inte blockerar följande kommunikationsportar: 443, 53, 9354, 445, 12000. Mer information om trafikfiltrering för Azure VNET NSG finns i artikeln om att [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurera din [Windows-brandvägg för källdatabasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
- Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
- Om du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering samt filer via SMB-port 445.
- Skapa en hanterad Azure SQL Database-instans genom att följa informationen i artikeln om att [skapa en hanterad Azure SQL Database-instans i Azure Portal](https://aka.ms/sqldbmi).
- Se till att inloggningarna som används för att ansluta SQL Server-källan och den hanterade målinstansen är medlemmar av sysadmin-serverrollen.
- Ange en SMB-nätverksresurs som innehåller alla säkerhetskopierade databasfiler och efterföljande säkerhetskopierade transaktionsloggfiler som Azure Database Migration Service kan använda för databasmigrering.
- Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörighet på nätverksresursen som du har skapat och att datorkontot för källservern har läs-/skrivåtkomst till samma resurs.
- Anteckna en Windows-användare (och lösenordet) som har fullständig kontrollbehörighet på nätverksresursen som du tidigare har skapat. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att ladda upp de säkerhetskopierade filerna till Azure Storage-container för återställning.
- Skapa ett Azure Active Directory-program-ID som genererar den program-ID-nyckel som DMS-tjänsten kan använda för att ansluta till den hanterade Azure Database-målinstansen och Azure Storage-containern. Mer information finns i artikeln [Använda portalen för att skapa ett Azure Active Directory-program och ett huvudnamn för tjänsten som får åtkomst till resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
- Skapa eller anteckna **Standard Performance-nivå** och Azure Storage-konto, som DMS-tjänsten kan överföra de säkerhetskopierade databasfilerna till och använda för att migrera databaser.  Var noga med att Azure Storage-kontot skapas i samma region där DMS-tjänsten har skapats.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

    ![Visa portalprenumerationer](media\tutorial-sql-server-to-managed-instance-online\portal-select-subscriptions.png)        
2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media\tutorial-sql-server-to-managed-instance-online\portal-select-resource-provider.png)

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media\tutorial-sql-server-to-managed-instance-online\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure Database Migration Service-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter **Azure Database Migration Service** och väljer sedan **Azure Database Migration Service** i listrutan.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance-online\portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media\tutorial-sql-server-to-managed-instance-online\dms-create1.png)

3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4.  Välj den plats där du vill skapa instansen av DMS.

5. Välj ett befintligt virtuellt nätverk eller skapa ett.
 
    Det virtuella nätverket ger Azure Database Migration Service åtkomst till SQL Server-källan och den hanterade Azure SQL Database-målinstansen.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [Skapa ett virtuellt nätverk med hjälp av Azure Portal](https://aka.ms/DMSVnet).

    Mer information finns i artikeln om [nätverkstopologier för migreringar av hanterade Azure SQL DB-instanser med Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Välj en SKU från prisnivån ”Affärskritisk (förhandsversion)”.

    > [!NOTE]
    > Online-migreringar stöds endast när du använder nivån ”Affärskritisk (förhandsversion)”. 
   
    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).
   
    ![Skapa DMS-tjänst](media\tutorial-sql-server-to-managed-instance-online\dms-create-service3.png)

7.  Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När en instans av tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta reda på alla instanser Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance-online\dms-search.png)

2. På **Azure Database Migration Service-sidan** söker du efter namnet på instansen som du har skapat och väljer sedan instansen.
 
3. Välj + **Nytt migreringsprojekt**.

4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet, I textrutan **Typ av källserver** väljer du **SQL Server**, i textrutan **Målservertyp** väljer du **Hanterad Azure SQL Database-instans** och sedan för **Välj typ av aktivitet** väljer du **Online-datamigrering (förhandsversion)**.

   ![Skapa DMS-projekt](media\tutorial-sql-server-to-managed-instance-online\dms-create-project3.png)

5. Välj **Skapa och kör aktivitet** för att skapa projektet.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källan.

2. Om du inte har installerat ett betrott certifikat på servern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > SSL-anslutningar som har krypterats med ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte lita på SSL som använder självsignerade certifikat i en produktionsmiljö eller på servrar som är anslutna till internet.

   ![Källinformation](media\tutorial-sql-server-to-managed-instance-online\dms-source-details2.png)

3. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1.  På skärmen **Information om migreringsmål** anger du **Program-ID** och **Nyckel** som DMS-instansen kan använda för att ansluta till målinstansen i Azure SQL Database Managed Instance och Azure Storage-kontot.

    Mer information finns i artikeln [Använda portalen för att skapa ett Azure Active Directory-program och ett huvudnamn för tjänsten som får åtkomst till resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
    
2. Välj den **Prenumeration** som innehåller målinstansen för Azure SQL Database Managed Instance, och välj sedan målinstansen.

    Om du inte redan har etablerat den hanterade Azure SQL Database-instansen väljer du [länken](https://aka.ms/SQLDBMI) som hjälper dig att etablera instansen. När instansen för Azure SQL Database Managed Instance är klar återgår du till det här specifika projektet för att utföra migreringen.

3. Ange **SQL-användare** och **lösenord** för att ansluta till målinstansen för Azure SQL Database Managed Instance.

    ![Välja mål](media\tutorial-sql-server-to-managed-instance-online\dms-target-details3.png)

2.  Välj **Spara**.

## <a name="select-source-databases"></a>Välj källdatabaser

1. På sidan **Välj källdatabaser** väljer du de källdatabaser som du vill migrera.

    ![Välj källdatabaser](media\tutorial-sql-server-to-managed-instance-online\dms-select-source-databases2.png)

2. Välj **Spara**.

## <a name="configure-migration-settings"></a>Konfigurera migreringsinställningar
 
1. På sidan **Konfigurera migreringsinställningar** anger du följande information:

    | | |
    |--------|---------|
    |**SMB-nätverksplatsresurs** | Den lokala SMB-nätverksresurs som innehåller alla säkerhetskopierade databasfiler och säkerhetskopierade transaktionsloggfiler som Azure Database Migration Service kan använda för migrering. Tjänstkontot som kör en SQL Server-källinstansen måste ha läs-/skrivbehörighet på den här nätverksresursen. Ange ett fullständigt domännamn eller IP-adresser för servern i nätverksresursen, till exempel \\\servernamn.domännamn.com\säkerhetskopieringsmapp eller \\\IP-adress\säkerhetskopieringsmapp.|
    |**Användarnamn** | Kontrollera att Windows-användaren har fullständig kontrollbehörighet på nätverksresursen du har angett ovan. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att ladda upp de säkerhetskopierade filerna till Azure Storage-container för återställning. |
    |**Lösenord** | Lösenordet för användaren. |
    |**Prenumeration på Azure Storage-kontot** | Välj den prenumeration som innehåller Azure Storage-kontot. |
    |**Azure Storage-konto** | Välj det Azure Storage-konto som DMS kan överföra de säkerhetskopierade filerna från SMB-nätverksresursen till och använda för databasmigrering.  Vi rekommenderar att du väljer Storage-kontot i samma region som DMS-tjänsten för bästa möjliga prestanda vid filöverföring. |
    
    ![Konfigurera migreringsinställningar](media\tutorial-sql-server-to-managed-instance-online\dms-configure-migration-settings4.png)

2. Välj **Spara**.
 
## <a name="review-the-migration-summary"></a>Granska migreringssammanfattningen

1. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

2. Granska och verifiera informationen som är kopplad till migreringsprojektet.
 
    ![Sammanfattning av migreringsprojekt](media\tutorial-sql-server-to-managed-instance-online\dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Köra och övervaka migreringen

1. Välj **Kör migrering**.

2. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen.
 
   ![Migreringsaktivitet pågår](media\tutorial-sql-server-to-managed-instance-online\dms-monitor-migration2.png)

    Du kan expandera databaserna och inloggningskategorierna ytterligare för att övervaka migreringsstatusen för respektive serverobjekt.

   ![Migreringsaktivitet pågår](media\tutorial-sql-server-to-managed-instance-online\dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Utföra snabbmigrering

Databasen är tillgänglig för att utföra en snabbmigrering när den fullständiga säkerhetskopieringen av databasen har återställts på målinstansen för Azure SQL Database Managed Instance.

1.  När du är redo att slutföra databasmigreringen online väljer du **Starta snabb**.

2.  Stoppa all inkommande trafik till källdatabaser.

3.  Ta [säkerhetskopia av slutlogg], gör säkerhetskopian tillgänglig i SMB-nätverksresursen och vänta sedan tills säkerhetskopieringen av den sista transaktionsloggen har återställts.

    I det här läget visas **Väntande ändringar** med värdet 0.

4.  Välj **Bekräfta** och sedan **Använd**.

    ![Förbereda för att slutföra startpunkt](media\tutorial-sql-server-to-managed-instance-online\dms-complete-cutover.png)

5.  När status för databasmigreringen visar **Slutförd** ansluter du dina program till den nya målinstansen för Azure SQL Database Managed Instance.

    ![Startpunkt slutförd](media\tutorial-sql-server-to-managed-instance-online\dms-cutover-complete.png)

## <a name="next-steps"></a>Nästa steg

- En självstudiekurs om hur du migrerar en databas till en hanterad instans med kommandot T-SQL RESTORE finns i [Återställa en säkerhetskopia till en hanterad instans med kommandot restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Information om hanterade instanser finns i avsnittet [Vad är en hanterad instans?](../sql-database/sql-database-managed-instance.md).
- Information om hur du ansluter appar till en hanterad instans finns i [Ansluta program](../sql-database/sql-database-managed-instance-connect-app.md).
