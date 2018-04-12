---
title: Visual Studio Connected Service för Azure Cosmos DB
description: Gör det möjligt för utvecklare att ansluta sitt Azure Cosmos DB-konto enkelt och hantera resurser via Visual Studio Connected Services
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: d496d80f1d87b234e0c94333b01ad2c23cc037ab
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2018
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: Visual Studio Connected Service (förhandsversion)

Med Visual Studio Connected Services kan utvecklare enkelt ansluta sitt Azure Cosmos DB-konto och hantera sina resurser.

Du kan även använda Datautforskaren i Connected Service för att skapa lagrade procedurer, UDF:er och utlösare för att utföra affärslogik på serversidan. Datautforskaren visar all den inbyggda programmässiga dataåtkomsten som finns tillgänglig i API:erna, men ger enkel åtkomst till dina data.

## <a name="prerequisites"></a>Nödvändiga komponenter

Se till att du har följande objekt:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 
* Ett Azure Cosmos DB-konto. Om du inte redan har ett följer du anvisningarna för hur du [skapar ett Azure Cosmos DB-konto](create-sql-api-dotnet.md) för att skapa ett i Azure-portalen eller läs [Create an Azure Cosmos DB account in the Connected Service tool](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool) (Skapa ett Azure Cosmos DB i Connected Service-verktyget). 
* Om du vill använda en lokal miljö i utvecklingssyfte kan du använda [Azure Cosmos DB-emulatorn](local-emulator.md). Miljön emulerar Azure Cosmos DB-tjänsten.
* [Visual Studio](http://www.visualstudio.com/).
* De senaste Azure Cosmos DB Connected Service-bitarna. Du kan ladda ned Azure Cosmos DB Connected Service från Visual Studio Marketplace enligt följande skärmbild. Öppna **Visual Studio** på datorn. På menyn **Tools** (Verktyg) väljer du **Extensions and Updates...** (Tillägg och uppdateringar...) och väljer sedan **Online** / **Visual Studio Marketplace**. Ange **cosmosdb** för att söka efter bitarna.

    Du kan även installera Azure Cosmos DB Connected Service från [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709).

    ![Skärmbild av Connected Service – ladda ned bitar.png](./media/connected-service/connected-service-downloadbits.png) 

När du har hämtat tillägget Azure Cosmos DB Connected Service stänger du Visual Studio och installerar tillägget.

## <a id="SetupVS"></a>Konfigurera din Visual Studio-lösning
1. Öppna **Visual Studio** på datorn.
2. I menyn **Arkiv** väljer du **Nytt** och sedan **Projekt**.
3. I dialogrutan **New Project** (Nytt projekt) väljer du **Visual C#** / **Console App (.NET Framework)** eller **WPF App (.NET Framework)**, namnger projektet och klickar sedan på **OK**.

    ![Skärmdump som visar fönstret Nytt projekt](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>Lägga till Connected Service och lägga till konto
1. I Solution Explorer högerklickar du på projektnoden **Add** (Lägg till) / **Connected Service**. Eller klicka på menyn **Project** (Projekt) och välj sedan **Add Connected Service** (Lägg till Connected Service).

    ![Skärmbild av fönstret Add Connected Service (Lägg till Connected Service)](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. På Connected Service-sidan klickar du på **Connected Services** / **Azure Cosmos DB** för att öppna **Azure Cosmos DB**-sidan.

    ![Skärmbild av Azure Cosmos DB-fönstret](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. Klicka på nedpilen för att logga in för första gången eller lägg till ett konto. Efter inloggningen visas alla Azure Cosmos DB-konton i det tomma utrymmet. Välj ett Azure Cosmos DB-konto att lägga till i ditt projekt.

    ![Skärmbild av fönstret för inloggning och listat db-konto](./media/connected-service/connected-service-add-db-account.png)
4. När du har lagt till ett Azure Cosmos DB-konto läggs en Connected Service-mapp för Azure Cosmos DB-kontot för projektet. Du kan lägga till fler än ett Azure Cosmos DB-konto genom att upprepa steg 1 till 3.

    ![Skärmbild av Connected Service-mappfönstret](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. När du har lagt till en an Azure Cosmos DB Connected Service ändrar du projektet för att möjliggöra åtkomst till Azure Cosmos DB på något av följande sätt:

    * Vissa nuget-paket som krävs av Azure Cosmos DB-klienten installeras. Du ser dem via din paketkonfigurationsfil. 

        ![Nya Azure Cosmos DB-paketkonfigurationen](./media/connected-service/connected-service-packages-config.png)   
    
    * Azure Cosmos DB-anslutningens URI och nyckel läggs till i projektkonfigurationsfilen, i det här fallet App.config. 

        ![Nya Azure Cosmos DB-appkonfigurationen](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Öppna Azure Cosmos DB Explorer
1. Högerklicka på projektnoden och välj **Open Cosmos DB Explorer...** (Öppna Cosmos DB Explorer...).

    ![Skärmbild av att försöka att öppna fönstret för Datautforskaren](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. På sidan **Choose a Cosmos DB Account** (Välj ett Cosmos DB-konto) klickar du på listrutan för att välja ett Azure Cosmos DB-konto.

    ![Skärmbild av Connected Service-fönstret Added account (Tillagt konto)](./media/connected-service/connected-service-open-explorer.png)
3. Klicka på **Open** (Öppna) och sedan visas datautforskarens fönster.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>Skapa ett Azure Cosmos DB-konto i Connected Service-verktyget
1. På Connected Service-sidan, i fönstret längst ned till vänster, klickar du på **Create a New Cosmos DB Account** (Skapa ett nytt Cosmos DB-konto) för att öppna sidan **Create Cosmos DB Account** (Skapa Cosmos DB-konto).

    ![Skärmbild av öppet startpunktsfönster för Create Azure Cosmos DB Account (Skapa Azure Cosmos DB-konto)](./media/connected-service/connected-service-click-new-db-account.png)
2. På sidan **Create Cosmos DB Account** (Skapa Cosmos DB-konto) anger du den konfiguration du vill ha för det här Azure Cosmos DB-kontot.

    Fyll i fälten på sidan **Create Cosmos DB Account** (Skapa Cosmos DB-konto), med informationen i följande skärmbild som referens. 
 
    ![Den nya Azure Cosmos DB-sidan](./media/connected-service/connected-service-create-new-account.png)        
3. Skapa kontot genom att klicka på **Skapa**.

## <a name="use-data-explorer"></a>Använda Datautforskaren

När du har öppnat Datautforskaren kan du göra följande:
* Skapa och ta bort databaser
* Skapa och ta bort samlingar
* Skapa, ta bort och filtrera dokument
* Skapa och ta bort lagrade procedurer
* Skapa och ta bort utlösare och användardefinierade funktioner för att utföra affärslogik på serversidan. 

![Den nya Azure Cosmos DB-sidan](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>Demo

Titta på följande video för att se hur du använder Azure Cosmos DB Connected Service i Visual Studio: [Använda Azure Cosmos DB Connected Service i Visual Studio](https://go.microsoft.com/fwlink/?linkid=858711)

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig följande:

> [!div class="checklist"]
> * Skapa ett Azure Cosmos DB-konto
> * Lägga till Connected Service och lägga till konto
> * Öppna Azure Cosmos DB Explorer
> * Använda Datautforskaren

När du nu har fått igång Connected Services med ditt Azure Cosmos DB-konto fortsätter du till någon av självstudiekurserna för att börja utveckla din lösning:

* [Utveckla med SQL API i .NET](tutorial-develop-sql-api-dotnet.md).
* [Azure Cosmos DB: Självstudiekurs för att komma igång med API för SQL](sql-api-get-started.md).
* Vill du testa skalning och prestanda med Azure Cosmos DB? Mer information finns i avsnittet om hur du [testar prestanda och skalning med Azure Cosmos DB](performance-testing.md).
* Lär dig hur du [övervakar ett Azure Cosmos DB-konto](monitor-accounts.md).

