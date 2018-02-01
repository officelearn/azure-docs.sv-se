---
title: "Etablera en Azure SSIS-integreringskörning med hjälp av portalen | Microsoft Docs"
description: "Den här artikeln förklarar hur du skapar en Azure SSIS-integreringskörning med hjälp av Azure Portal."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: 281fe65393086ec6a04dcba5aae868f4fec097ad
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>Etablera en Azure SSIS-integreringskörning med Data Factory-användargränssnittet
Den här självstudien innehåller steg för att använda Azure Portal för att distribuera en Azure SSIS-integreringskörning (IR) i Azure Data Factory. Sedan kan du använda SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera SQL Server Integration Services-paket (SSIS) till den här körningen i Azure. Begreppsrelaterad information om Azure-SSIS IR finns i [översikten över Azure SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa och starta en Azure SSIS-integreringskörning

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. 
- **Azure SQL Database-server**. Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Azure Data Factory skapar SSIS-katalogdatabasen (SSISDB) på den här databasservern. Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Den här konfigurationen gör att Integration Runtimes skrivkörning loggas till SSISDB utan att korsa Azure-regioner. 
    - Bekräfta att inställningen **tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Mer information finns i [säkra din Azure SQL-databas](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Om du vill aktivera den här inställningen med hjälp av PowerShell, se [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Lägg till IP-adressen för klientdatorn eller ett intervall med IP-adresser som innehåller IP-adressen för klientdatorn till klientens IP-adresslista i brandväggsinställningarna för databasservern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../sql-database/sql-database-firewall-configure.md).
    - Bekräfta att din Azure SQL Database-server inte har någon SSIS-katalog (SSIDB-databas). Distributionen av Azure-SSIS IR stöder inte användning av en befintlig SSIS-katalog.
 
## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure-portalen](https://portal.azure.com/).    
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. På sidan **Ny datafabrik** anger du **MyAzureSsisDataFactory** som **namn**. 
      
     ![Sida för ny datafabrik](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnMyAzureSsisDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
      Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. Endast de platser som har stöd för att skapa datafabriker visas i listan.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
   ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Klicka på **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Etablera en Azure SSIS-integreringskörning

1. Klicka på ikonen för att **konfigurera en SSIS-integreringskörning** på sidan för att komma igång. 

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Utför följande steg på sidan **Allmänna inställningar** i **installationsprogrammet för Integration Runtime**: 

   ![Allmänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Ange ett **namn** för integreringskörningen.
    2. Välj en **plats** för integreringskörningen. Endast platser som stöds visas.
    3. Välj den **storlek för noden** som ska konfigureras med SSIS-körningsmiljön.
    4. Ange **antalet noder** i klustret.
    5. Klicka på **Nästa**. 
1. I **SQL-inställningar** utför du följande steg: 

    ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Ange Azure-**prenumerationen** som har Azure SQL-servern. 
    2. Välj din Azure SQL-server för **serverslutpunkten för katalogdatabasen**.
    3. Ange **administratörens** användarnamn.
    4. Ange **lösenordet** för administratören.  
    5. Välj **tjänstnivån** för SSIDB-databasen. Standardvärdet är Basic.
    6. Klicka på **Nästa**. 
1.  På sidan **Avancerade inställningar** väljer du ett värde för **maximalt antal parallellkörningar per nod**.   

    ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Det här steget är **valfritt**. Om du har ett klassiskt virtuellt nätverk (VNet) som du vill att integreringskörningen ska ansluta till väljer du alternativet **Välj ett virtuellt nätverk som din Azure SSIS-integreringskörning ska ansluta till och låt Azure-tjänsterna konfigurera behörigheter/inställningar för det virtuella nätverket**. Gör sedan följande: 

    ![Avancerade inställningar med virtuella nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Ange **prenumerationen** som har det klassiska virtuella nätverket. 
    2. Välj **Virtuellt nätverk**. <br/>
    4. Välj **Undernät**.<br/> 
1. Klicka på **Slutför** för att starta skapandet av Azure SSIS-integreringskörningen. 

    > [!IMPORTANT]
    > - Den här processen tar cirka 20 minuter att slutföra
    > - Data Factory-tjänsten ansluts till din Azure SQL Database för att förbereda SSISDB. Skriptet konfigurerar också behörigheter och inställningar för ditt VNet, om det anges, och ansluter den nya instansen för Azure-SSIS Integration Runtime till VNet.
    > - När du etablerar en instans av SQL Database som värd för SSISDB installeras också Azure Feature Pack för SSIS och Access Redistributable. Med dessa komponenter upprättar du en anslutning till Excel- och Access-filer och till olika Azure-datakällor, utöver de datakällor som stöds av de inbyggda komponenterna. Du kan inte installera komponenter från tredje part för SSIS vid den här tidpunkten (inklusive komponenter från tredje part från Microsoft, som Oracle- och Teradata-komponenterna från Attunity och SAP BI-komponenterna).

7. I fönstret **Anslutningar** växlar du till **integreringskörningar** om det behövs. Om du vill uppdatera statusen klickar du på **Uppdatera**. 

    ![Skapandestatus](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Använd länkarna under kolumnen **Åtgärder** för att övervaka, stoppa/starta, redigera eller ta bort integreringskörningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats. 

    ![Azure SSIS IR – åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. Klicka på länken **Övervaka** under **Åtgärder**.  

    ![Azure SSIS IR – information](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. Om ett **fel** associerat med Azure SSIS IR uppstår så visas antalet fel och en länk för att se detaljer om felet på den här sidan. Om SSIS-katalogen till exempel redan finns på databasservern så visas ett felmeddelande som påpekar att SSIDB-databasen finns.  
11. Klicka på **integreringskörningar** längst upp för att gå tillbaka till den föregående sidan där du kan se alla integreringskörningar som är associerade med datafabriken.  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS-integreringskörningar i portalen

1. I användargränssnittet för Azure Data Factory växlar du till fliken **Redigera**, klickar på **Anslutningar**, sedan växlar du till fliken **integreringskörningar** för att se befintliga integreringskörningar i din datafabrik. 
    ![Visa befintliga IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Klicka på **Ny** för att skapa en ny Azure-SSIS IR. 

    ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. För att skapa en Azure-SSIS-integreringskörning klickar du på **Ny**, som du kan se i bilden. 
3. I installationsfönstret för Integration Runtime väljer du **Lift-and-shift existing SSIS packages to execute in Azure** (Lift and Shift-hantera befintliga SSIS-paket för att köra i Azure) och klicka sedan på **Nästa**.

    ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure SSIS-integreringskörning. 

 
## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket
Använd nu SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera dina SSIS-paket till Azure. Anslut till Azure SQL-servern som är värd för SSIS-katalogen (SSISDB). Namnet på Azure SQL-servern är i formatet: `<servername>.database.windows.net` (för Azure SQL Database). 

Se följande artiklar från SSIS-dokumentationen: 

- [Distribuera, köra och övervaka ett SSIS-paket på Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ansluta till SSIS-katalogen på Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schemalägga paketetkörning på Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa och starta en Azure SSIS-integreringskörning

Fortsätt till följande självstudie och lär dig att kopiera data från en lokal plats till molnet: 

> [!div class="nextstepaction"]
>[Kopiera data i molnet](tutorial-copy-data-portal.md)
