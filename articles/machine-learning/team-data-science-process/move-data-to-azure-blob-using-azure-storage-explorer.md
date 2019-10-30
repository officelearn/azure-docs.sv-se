---
title: Flytta Blob Storage-data med Azure Storage Explorer-Team data science process
description: Lär dig hur du använder Azure Storage Explorer för att ladda upp och hämta data från Azure Blob Storage.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 880b81c07aeed7359dfe35e1361a20ecb11e27dd
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053962"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Flytta data till och från Azure Blob Storage med Azure Storage Explorer
Azure Storage Explorer är ett kostnads fritt verktyg från Microsoft som gör att du kan arbeta med Azure Storage data på Windows, macOS och Linux. I det här avsnittet beskrivs hur du använder det för att ladda upp och hämta data från Azure Blob Storage. Verktyget kan laddas ned från [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Om du använder en virtuell dator som har kon figurer ATS med skripten som tillhandahålls av [data science Virtual Machines i Azure](virtual-machines.md), är Azure Storage Explorer redan installerad på den virtuella datorn.
> 
> [!NOTE]
> En fullständig introduktion till Azure Blob Storage finns i grunderna för [Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob service](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Krav
Det här dokumentet förutsätter att du har en Azure-prenumeration, ett lagrings konto och motsvarande lagrings nyckel för det kontot. Innan du laddar upp/laddar ned data måste du känna till ditt Azure Storage-konto namn och konto nyckel. 

* Information om hur du konfigurerar en Azure-prenumeration finns i [den kostnads fria utvärderings perioden på en månad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett lagrings konto och hämtar information om konton och nycklar finns i [om Azure Storage-konton](../../storage/common/storage-create-storage-account.md). Anteckna åtkomst nyckeln för ditt lagrings konto eftersom du behöver den här nyckeln för att ansluta till kontot med Azure Storage Explorer-verktyget.
* Verktyget Azure Storage Explorer kan laddas ned från [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Acceptera standardinställningarna under installationen.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Använd Azure Storage Explorer
Följande steg beskriver hur du laddar upp/laddar ned data med Azure Storage Explorer. 

1. Starta Microsoft Azure Storage Explorer.
2. För att öppna **inloggningen till ditt konto...** -guide, Välj ikonen för **Azure konto inställningar** och **Lägg sedan till ett konto** och ange autentiseringsuppgifter. ![lägga till ett Azure Storage-konto](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Om du vill öppna guiden **Anslut till Azure Storage** väljer du ikonen **Anslut till Azure Storage** . ![Klicka på Anslut till Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Ange åtkomst nyckeln från ditt Azure Storage-konto i guiden **Anslut till Azure Storage** och klicka sedan på **Nästa**. ![ange åtkomst nyckel från Azure Storage-konto](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Ange lagrings kontots namn i rutan **konto namn** och välj sedan **Nästa**. ![bifoga extern lagring](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Lagrings kontot läggs nu till i listan. Om du vill skapa en BLOB-behållare i ett lagrings konto högerklickar du på noden **BLOB-behållare** i kontot, väljer **skapa BLOB-behållare**och anger ett namn.
7. Om du vill överföra data till en behållare väljer du mål behållaren och klickar på knappen **överför** .![lagrings konton](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klicka på **..** . till höger om rutan **filer** , Välj en eller flera filer som ska laddas upp från fil systemet och klicka på **överför** för att påbörja överföring av filerna.![Ladda upp filer](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Hämta data genom att välja bloben i motsvarande behållare för att ladda ned och klicka på **Hämta**. ![hämta filer](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

