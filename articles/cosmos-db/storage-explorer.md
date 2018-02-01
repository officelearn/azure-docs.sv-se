---
title: Hantera Azure Cosmos DB i Azure Storage Explorer
description: "Lär dig hantera Azure Cosmos DB i Azure Storage Explorer."
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
ms.openlocfilehash: baa7eee614159f9c6af493aff74b27773471f7d7
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Hantera Azure Cosmos DB i Azure Storage Explorer (förhandsversion)

Med hjälp av Azure Cosmos DB i Azure Storage Explorer kan du hantera Azure Cosmos DB-entiteter, manipulera data och uppdatera lagrade procedurer och utlösare, och även andra Azure-entiteter som lagringsblobar och köer. Nu kan du använda samma verktyg för att hantera olika Azure-entiteter på ett och samma ställe. Just nu har Azure Storage Explorer stöd för SQL <!--and MongoDB-->-konton. Azure Storage Explorer fungerar inte med Azure Cosmos DB Local Emulator. 

I den här artikeln får du lära dig hur du använder Storage Explorer för att hantera Azure Cosmos DB.


## <a name="prerequisites"></a>Nödvändiga komponenter

Ett Azure Cosmos DB-konto för SQL-API:et <!--or MongoDB API-->. Om du inte har ett konto kan du skapa en i Azure-portalen, enligt beskrivningen i [Azure Cosmos DB: Skapa en SQL API-webbapp med .NET och Azure Portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Installation

Installera den senaste versionen av Azure Storage Explorer här: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Nu finns det versioner för Windows, Linux och MAC.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

1. När du har installerat **Azure Storage Explorer** ska du klicka på **plugin**-ikonen till vänster, som visas i följande bild.
       
   ![Plugin-ikon](./media/storage-explorer/plug-in-icon.png)
 
2. Välj **Lägg till ett Azure-konto** och klicka sedan på **Logga in**.

   ![Ansluta till Azure-prenumerationen](./media/storage-explorer/connect-to-azure-subscription.png)

2. Välj **Logga in** i dialogrutan **Azure-inloggning** och ange dina autentiseringsuppgifter för Azure.

    ![Logga in](./media/storage-explorer/sign-in.png)

3. Välj din prenumeration i listan och klicka sedan på **Använd**.

    ![Använd](./media/storage-explorer/apply-subscription.png)

    Explorer-fönstret uppdateras och visar konton i den valda prenumerationen.

    ![Kontolista](./media/storage-explorer/account-list.png)

    Du har anslutit ditt **Azure Cosmos DB-konto** till din Azure-prenumeration.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Ansluta till Azure Cosmos DB med hjälp av en anslutningssträng

Ett alternativt sätt att ansluta till en Azure Cosmos DB är att använda en anslutningssträng. Följ stegen nedan för att ansluta med en anslutningssträng.

1. Hitta **Lokala och anslutna** i trädet till vänster, högerklicka på **Azure Cosmos DB-konton** och välj **Anslut till Azure Cosmos DB...**

    ![Ansluta till Azure Cosmos DB med hjälp av en anslutningssträng](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Välj lämplig **Default Experience** (Standardupplevelse) för kontotypen, <!--either--> **DocumentDB** <!--or **MongoDB**-->, klistra in **söksträngen** och klicka sedan på **OK** för att ansluta till Azure Cosmos DB-kontot. Information om hur du hämtar anslutningssträngen finns i [Hämta anslutningssträngen](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Anslutningssträng](./media/storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Resurshantering för Azure Cosmos DB

Du kan hantera ett Azure Cosmos DB-konto med följande åtgärder:
* Öppna kontot i Azure-portalen
* Lägg till resursen i listan Snabbåtkomst
* Sök och uppdatera resurser
* Skapa och ta bort databaser
* Skapa och ta bort samlingar
* Skapa, redigera, ta bort och filtrera dokument
* Hantera lagrade procedurer, utlösare och användardefinierade funktioner

### <a name="quick-access-tasks"></a>Uppgifter för snabbåtkomst

Genom att högerklicka på en prenumeration i Explorer-fönstret kan du utföra många snabbåtgärder:

* Högerklicka på ett Azure Cosmos DB-konto eller en databas så kan du välja alternativet för att **öppna i portalen** och hantera resursen i Azure-portalen i webbläsaren.

     ![Öppna i portalen](./media/storage-explorer/open-in-portal.png)

* Du kan också lägga till Azure Cosmos DB-konto, databas och samling i **Snabbåtkomst**.
* **Sök härifrån** aktiverar nyckelordssökning under den valda sökvägen.

    ![sök härifrån](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Databas- och samlingshantering
#### <a name="create-a-database"></a>Skapa en databas 
Högerklicka Azure Cosmos DB-kontot, välj **Skapa databas** ange databasnamnet och slutför genom att trycka på **Retur**.

![Skapa databas](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Ta bort en databas
Högerklicka på databasen, klicka på **Ta bort databas** och klicka på **Ja** i popup-fönstret. Databasnoden tas bort och Azure Cosmos DB-konto uppdateras automatiskt.

![Ta bort databas1](./media/storage-explorer/delete-database1.png)  

![Ta bort databas2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Skapa en samling
Högerklicka på databasen, välj **Skapa samling** och ange sedan följande information som **Samlings-ID**, **Lagringskapacitet** o.s.v. Klicka på **OK** för att slutföra. Mer information om inställningar för partitionsnycklar finns i [Design för partitionering](partition-data.md#designing-for-partitioning).

Om en partitionsnyckel används när du skapar en samling kan partitionsnyckelvärdet inte ändras i samlingen när den väl har skapats.

![Skapa samling1](./media/storage-explorer/create-collection.png)

![Skapa samling2](./media/storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>Ta bort en samling
Högerklicka på samlingen, klicka på **Ta bort samling** och klicka sedan på **Ja** i popup-fönstret. 

Samlingsnoden tas bort och databasen uppdateras automatiskt.  

![Ta bort samling](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Dokumenthantering

#### <a name="create-and-modify-documents"></a>Skapa och ändra dokument
Du kan skapa ett nytt dokument genom att öppna **Documents** (Dokument) i det vänstra fönstret, klicka på **New Document** (Nytt dokument), redigera innehållet i den högra rutan och sedan klicka på **Save** (Spara). Du kan också uppdatera ett befintligt dokument och sedan klicka på **Save**. Ändringar kan ignoreras genom att klicka på **Discard** (Ignorera).

![Dokument](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Ta bort ett dokument
Klicka på knappen **Delete** (Ta bort) om du vill ta bort det valda dokumentet.
#### <a name="query-for-documents"></a>Fråga för dokument
Redigera dokumentfilter genom att ange en [SQL-fråga](sql-api-sql-query.md) och sedan klicka på **Apply** (Tillämpa).

![Filter](./media/storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Hantera lagrade procedurer, utlösare och UDF:er
* Skapa en lagrad procedur genom att i vänster träd högerklicka på **Lagrad procedur**, välj **Skapa lagrad procedur**, ange ett namn i vänstra fönstret, ange skripten för den lagrade proceduren i det högra fönstret och klicka sedan på **Skapa**. 
* Du kan också redigera befintliga, lagrade procedurer genom att dubbelklicka, göra uppdateringen och sedan klicka på **Uppdatera** om du vill spara eller på **Ignorera** om du vill avbryta ändringen.

![Lagrad procedur](./media/storage-explorer/stored-procedure.png)

* Åtgärderna för **utlösare** och **UDF** liknar dem för **lagrade procedurer**.

## <a name="next-steps"></a>Nästa steg

* Titta på följande videoklipp om du vill se hur du använder Azure Cosmos DB i Azure Storage Explorer: [Använda Azure Cosmos-DB i Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Läs mer om Storage Explorer och om att ansluta fler tjänster i [Kom igång med Storage Explorer (förhandsversion)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

