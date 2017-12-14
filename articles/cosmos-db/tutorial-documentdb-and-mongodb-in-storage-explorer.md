---
title: Hantera Azure Cosmos DB i Azure Lagringsutforskaren
description: "Lär dig hur du hanterar Azure Cosmos-DB i Azure Lagringsutforskaren."
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: 
author: jejiang
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: Jejiang
ms.openlocfilehash: 8afb85eac8dec502406ca419623407b1dc228931
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Hantera Azure Cosmos DB i Azure Lagringsutforskaren (förhandsversion)

Med hjälp av Azure Cosmos DB i Azure Lagringsutforskaren kan du hantera Azure Cosmos DB-enheter, manipulera data och uppdatera lagrade procedurer och utlösare tillsammans med andra Azure entiteter som Storage-blobbar och köer. Nu kan du använda samma verktyg för att hantera dina Azure olika enheter i ett och samma ställe. För tillfället stöder Azure Lagringsutforskaren SQL och MongoDB-konton. Azure Lagringsutforskaren fungerar inte med Azure Cosmos DB lokala emulatorn. 

I den här artikeln får du lära dig hur du använder Lagringsutforskaren för att hantera Azure Cosmos DB.


## <a name="prerequisites"></a>Krav

Ett Azure DB som Cosmos-konto för SQL API eller MongoDB-API. Om du inte har ett konto kan du skapa en i Azure-portalen, enligt beskrivningen i [Azure Cosmos DB: skapa en SQL-API-webbprogram med .NET och Azure portal](create-documentdb-dotnet.md).

## <a name="installation"></a>Installation

Installera de senaste Azure Lagringsutforskaren bits här: [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/), nu vi stöder Windows, Linux och MAC-version.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

1. När du har installerat den **Azure Lagringsutforskaren**, klicka på den **plugin** ikonen till vänster som visas i följande bild.
       
   ![Plugin-ikon](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/plug-in-icon.png)
 
2. Välj **Lägg till ett Azure-konto**, och klicka sedan på **inloggning**.

   ![Ansluta till Azure-prenumeration](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-azure-subscription.png)

2. I den **Azure inloggning** dialogrutan **logga in**, och ange dina autentiseringsuppgifter för Azure.

    ![Logga in](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/sign-in.png)

3. Välj din prenumeration i listan och klicka sedan på **tillämpa**.

    ![Ansök](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/apply-subscription.png)

    Explorer-fönstret uppdateras och visar konton i den valda prenumerationen.

    ![Listan över användarkonton](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/account-list.png)

    Du har anslutit till din **Azure Cosmos DB konto** till din Azure-prenumeration.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Ansluta till Azure Cosmos DB med hjälp av en anslutningssträng

Ett alternativt sätt att ansluta till en Azure-Cosmos-databas är att använda en anslutningssträng. Använd följande steg för att ansluta med en anslutningssträng.

1. Hitta **lokala och bifogad** i vänster träd högerklickar du på **Azure Cosmos DB konton**, Välj **Anslut till Azure Cosmos DB...**

    ![ansluta till Azure Cosmos-databas med anslutningssträngen](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-db-by-connection-string.png)

2. Välj lämplig **standard upplevelse** för vilken typ av konto antingen **DocumentDB** eller **MongoDB**, klistra in i din **anslutningssträngen**, och klicka sedan på **OK** att ansluta till Azure DB som Cosmos-konto. Information om hur du hämtar anslutningssträngen finns [hämta anslutningssträngen](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![anslutningssträng](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Azure DB Cosmos-resurshantering

Du kan hantera ett Azure DB som Cosmos-konto genom att göra följande åtgärder:
* Öppna kontot i Azure-portalen
* Lägger till resursen i listan Snabbåtkomst
* Sök och uppdatera resurser
* Skapa och ta bort databaser
* Skapa och ta bort samlingar
* Skapa, redigera, ta bort och filtrera dokument
* Hantera lagrade procedurer, utlösare och användardefinierade funktioner

### <a name="quick-access-tasks"></a>Snabbåtkomst uppgifter

Genom att högerklicka på en prenumeration i Utforskaren, kan du utföra många snabb åtgärd aktiviteter:

* Högerklicka på ett Azure DB som Cosmos-konto eller en databas, kan du välja **öppna i portalen** och hantera resurser i webbläsaren på Azure-portalen.

     ![Öppna i portalen](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/open-in-portal.png)

* Du kan också lägga till Azure DB som Cosmos-kontot, databas, samling till **Snabbåtkomst**.
* **Sök härifrån** aktiverar nyckelordssökning under den valda sökvägen.

    ![Sök härifrån](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Hantering av databas och samling
#### <a name="create-a-database"></a>Skapa en databas 
Högerklicka på Azure DB som Cosmos-kontot, Välj **Create Database**, inkommande databasnamnet och tryck på **RETUR** ska slutföras.

![Skapa databas](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Ta bort en databas
Högerklicka på databasen, klickar du på **ta bort databasen**, och klicka på **Ja** i popup-fönstret. Databasnoden tas bort och Azure DB som Cosmos-konto uppdateras automatiskt.

![Ta bort database1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database1.png)  

![Ta bort database2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Skapa en samling
Högerklicka på databasen, väljer **skapa samling**, och ange sedan följande information som **Samlings-ID**, **lagringskapacitet**osv. Klicka på **OK** för att slutföra. Mer information om inställningar för partitionen finns [Design för partitionering](partition-data.md#designing-for-partitioning).

Om en partitionsnyckel används när du skapar en samling när skapa har slutförts, kan nyckelvärdet partitionen inte ändras i samlingen.

![Skapa collection1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection.png)

![Skapa collection2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>Ta bort en samling
Högerklicka på samlingen klickar du på **ta bort samlingen**, och klicka sedan på **Ja** i popup-fönstret. 

Noden samlingen tas bort och databasen uppdateras automatiskt.  

![Ta bort samlingen](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Dokumenthantering

#### <a name="create-and-modify-documents"></a>Skapa och ändra dokument
Om du vill skapa ett nytt dokument öppna **dokument** i det vänstra fönstret klickar du på **nytt dokument**, redigera innehållet i den högra rutan och klicka på **spara**. Du kan också uppdatera ett befintligt dokument och klicka sedan på **spara**. Ändringar kan ignoreras genom att klicka på **Ignorera**.

![Dokument](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/document.png)

#### <a name="delete-a-document"></a>Ta bort ett dokument
Klicka på den **ta bort** för att ta bort det valda dokumentet.
#### <a name="query-for-documents"></a>Frågan för dokument
Redigera dokumentfilter genom att ange en [SQL-frågan](documentdb-sql-query.md) och klicka sedan på **tillämpa**.

![Filter](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Hantera lagrade procedurer, utlösare och UDF: er
* Om du vill skapa en lagrad procedur i vänster träd, högerklicka på **lagrade proceduren**, Välj **skapa lagrade proceduren**, ange ett namn i vänstra, Skriv de lagrade proceduren skript i det högra fönstret och sedan Klicka på **skapa**. 
* Du kan också redigera befintliga lagrade procedurer genom att dubbelklicka på att uppdateringen och sedan klicka på **uppdatera** spara, eller klicka på **Ignorera** avbryta ändringen.

![Lagrad procedur](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/stored-procedure.png)

* Åtgärder för **utlösare** och **UDF** liknar de för **lagrade procedurer**.

## <a name="next-steps"></a>Nästa steg

* Titta på följande videoklipp om du vill se hur du använder Azure Cosmos DB i Azure Lagringsutforskaren: [använder Azure Cosmos-DB i Azure Lagringsutforskaren](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Mer information om Lagringsutforskaren och ansluta flera tjänster i [Kom igång med Lagringsutforskaren (förhandsversion)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

