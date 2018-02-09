---
title: "Etablera en SSIS-integreringskörning med Azure Data Factory | Microsoft Docs"
description: "I den här artikeln förklaras hur du skapar en Azure-SSIS-integreringskörning med hjälp av Azure Data Factory."
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
ms.date: 01/29/2018
ms.author: spelluru
ms.openlocfilehash: 9e0b0efdf28e6d8b99d1cdf702dd0698ad87da7b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-azure-data-factory-ui"></a>Etablera en Azure-SSIS-integreringskörning med Azure Data Factory-användargränssnittet
Den här självstudien innehåller steg för att använda Azure Portal för att distribuera en Azure SSIS-integreringskörning (IR) i Azure Data Factory. Sedan kan du använda SQL Server Data Tools eller SQL Server Management Studio för att distribuera SQL Server Integration Services-paket (SSIS) till den här körningen i Azure. Begreppsrelaterad information om Azure-SSIS IR finns i [översikten över Azure SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa och starta en Azure-SSIS-integreringskörning.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 
- **Azure SQL Database-server**. Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Azure Data Factory skapar SSIS-katalogen (SSISDB-databasen) på den här databasservern. Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Den här konfigurationen gör att Integration Runtimes skrivkörning loggas till SSISDB-databasen utan att korsa Azure-regioner. 
   - Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Mer information finns i [säkra din Azure SQL-databas](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Om du vill aktivera den här inställningen med hjälp av PowerShell, se [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
   - Lägg till IP-adressen för klientdatorn eller ett intervall med IP-adresser som innehåller IP-adressen för klientdatorn till klientens IP-adresslista i brandväggsinställningarna för databasservern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../sql-database/sql-database-firewall-configure.md).
   - Bekräfta att din Azure SQL Database-server inte har någon SSIS-katalog (SSISDB-databas). När du ska etablera en Azure-SSIS IR kan du inte ha någon befintlig SSIS-katalog.
 
## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure-portalen](https://portal.azure.com/).    
2. Välj **Nytt** på den vänstra menyn, välj **Data och analys** och välj sedan **Data Factory**. 
   
   ![Valet Data Factory i fönstret Nytt](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. På sidan **Ny datafabrik** anger du **MyAzureSsisDataFactory** under **Namn**. 
      
   ![Sidan Ny datafabrik](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara *globalt unikt*. Om följande fel returneras ändrar du namnet på datafabriken (till exempel **&lt;dittnamn&gt;MyAzureSsisDataFactory**) och provar att skapa fabriken igen. Namngivningsregler för Data Factory-artefakter finns i artikeln [Data Factory – namnregler](naming-rules.md).
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. Välj den Azure-prenumeration där du vill skapa den nya datafabriken för **Prenumeration**. 
5. Gör något av följande för **Resursgrupp**:
     
   - Välj **Använd befintlig** och välj en befintlig resursgrupp från listan. 
   - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
   Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
6. För **Version** väljer du **V2 (förhandsversion)**.
7. För **Plats** väljer du en plats för datafabriken. Endast de platser som har stöd för att skapa datafabriker visas i listan.
8. Välj **fäst till instrumentpanelen**.     
9. Välj **Skapa**.
10. Du ser följande panel på instrumentpanelen med statusen **Distribuerar datafabrik**: 

   ![Panelen Distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. När datafabriken har skapats visas sidan **Datafabrik**.
   
   ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. Välj **Övervaka och hantera** för att öppna användargränssnittet för Data Factory på en separat flik. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Etablering av en Azure-SSIS-integreringskörning

1. På sidan **Nu börjar vi** väljer du ikonen för att **konfigurera en SSIS-integreringskörning**. 

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Gör följande på sidan **Allmänna inställningar** i **installationsprogrammet för Integration Runtime**: 

   ![Allmänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Ange ett namn för integreringskörningen under **Namn**.

   b. Välj en plats för integreringskörningen under **Plats**. Endast platser som stöds visas.

   c. Välj den storlek för noden som ska konfigureras med SSIS-körningsmiljön under **Nodstorlek**.

   d. Ange antalet noder i klustret under **Antal noder**.
   
   e. Välj **Nästa**. 
3. Gör följande på sidan **SQL-inställningar**: 

   ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Ange Azure-prenumerationen som har Azure SQL-servern under **Prenumeration**.

   b. Välj din Azure-databasserver för **Serverslutpunkt för katalogdatabas**.

   c. Ange administratörens användarnamn under **Användarnamn för administratör**.

   d. Ange administratörens lösenord under **Administratörslösenord**.

   e. Välj tjänstnivå för SSISDB-databasen under **Servernivå för katalogdatabas**. Standardvärdet är **Basic**.

   f. Välj **Nästa**. 
4. På sidan **Avancerade inställningar** väljer du ett värde för **Maximalt antal parallellkörningar per nod**.   

   ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Det här steget är *valfritt*. Om du har ett virtuellt nätverk (som skapats i den klassiska distributionsmodellen eller via Azure Resuorce Manager) som du vill att integreringskörningen ska ansluta till markerar du kryssrutan **Välj ett virtuellt nätverk som din Azure SSIS-integreringskörning ska ansluta till och låt Azure-tjänsterna konfigurera behörigheter/inställningar för det virtuella nätverket**. Gör sedan följande: 

   ![Avancerade inställningar med ett virtuellt nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. Ange prenumerationen som har det virtuella nätverket under **Prenumeration**.

   b. Välj namnet på det virtuella nätverket under **Namn på virtuellt nätverk**.

   c. Välj namnet på undernätet i det virtuella nätverket under **Undernätsnamn**. 
6. Välj **Slutför** för att starta skapandet av Azure SSIS-integreringskörningen. 

   > [!IMPORTANT]
   > Den här processen tar cirka 20 minuter att slutföra.
   >
   > Data Factory-tjänsten ansluts till din Azure SQL-databas för att förbereda SSIS-katalogen (SSISDB-databasen). Skriptet konfigurerar även behörigheter och inställningar för det virtuella nätverket om ett sådant anges. Dessutom ansluts den nya instansen av Azure-SSIS-integreringskörningen till det virtuella nätverket.
   > 
   > När du etablerar en instans av en Azure-SSIS IR installeras också Azure Feature Pack för SSIS och Access Redistributable. Med dessa komponenter upprättar du en anslutning till Excel- och Access-filer och till olika Azure-datakällor, utöver de datakällor som stöds av de inbyggda komponenterna. Du kan inte installera komponenter från tredje part för SSIS just nu. (Den här begränsningen gäller även komponenter från tredje part från Microsoft, som Oracle- och Teradata-komponenter från Attunity och SAP BI-komponenterna).

7. På fliken **Anslutningar** växlar du till **Integreringskörningar** om det behövs. Välj **Uppdatera** för att uppdatera statusen. 

   ![Skapandestatus med knappen ”Uppdatera”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Använd länkarna i kolumnen **Åtgärder** till att stoppa/starta, redigera eller ta bort integreringskörningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats. 

   ![Länkar i kolumnen Åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime

1. I användargränssnittet för Azure Data Factory växlar du till fliken **Redigera**, väljer **Anslutningar** och växlar sedan till fliken **Integreringskörningar** för att se befintliga integreringskörningar i din datafabrik. 
   ![Alternativ för att visa befintliga integreringskörningar](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. Välj **Ny** för att skapa en Azure-SSIS IR. 

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. I **installationsfönstret för Integration Runtime** väljer du **Lift-and-shift existing SSIS packages to execute in Azure** (Lift and Shift-hantera befintliga SSIS-paket för att köra i Azure) och väljer sedan **Nästa**.

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure-SSIS-integreringskörning. 

 
## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket
Använd nu SQL Server Data Tools eller SQL Server Management Studio till att distribuera dina SSIS-paket till Azure. Anslut till Azure-databasservern som är värd för SSIS-katalogen (SSISDB-databasen). Namnet på Azure-databasservern har formatet `<servername>.database.windows.net` (för Azure SQL Database). 

Se följande artiklar från SSIS-dokumentationen: 

- [Distribuera, köra och övervaka ett SSIS-paket på Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ansluta till SSIS-katalogen på Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schemalägga paketetkörning på Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa och starta en Azure-SSIS-integreringskörning.

Fortsätt till nästa självstudie om du vill lära dig att kopiera data från en lokal plats till molnet: 

> [!div class="nextstepaction"]
> [Kopiera data i molnet](tutorial-copy-data-portal.md)
