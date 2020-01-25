---
title: Flytta Blob storage-data med Azure Storage Explorer – Team Data Science Process
description: Lär dig hur du använder Azure Storage Explorer för att ladda upp och hämta data från Azure Blob Storage.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bfc63c6f5aca92fb7fda9e3ecf63ce4c332b12ef
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720919"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Flytta data till och från Azure Blob Storage med Azure Storage Explorer
Azure Storage Explorer är ett kostnadsfritt verktyg från Microsoft som gör det möjligt att arbeta med Azure Storage-data i Windows, macOS och Linux. Det här avsnittet beskriver hur du använder den för att ladda upp och hämta data från Azure blob storage. Verktyget kan laddas ned från [Microsoft Azure Lagringsutforskaren](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Om du använder VM som konfigurerats med skript som tillhandahålls av [Data Science virtuella datorer i Azure](virtual-machines.md), och sedan Azure Storage Explorer är redan installerad på den virtuella datorn.
> 
> [!NOTE]
> En fullständig introduktion till Azure blob storage finns [grunderna i Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob-tjänsten](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Krav
Det här dokumentet förutsätter att du har en Azure-prenumeration, ett lagringskonto och motsvarande lagringskontots åtkomstnyckel för kontot. Innan du laddar upp/laddar ned data måste du känna till Azure Storage kontots namn och konto nyckel. 

* Om du vill konfigurera en Azure-prenumeration, se [kostnadsfri utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett lagrings konto och hämtar information om konton och nycklar finns i [om Azure Storage-konton](../../storage/common/storage-create-storage-account.md). Skriv ner åtkomstnyckeln för ditt lagringskonto som du behöver den här nyckeln för att ansluta till kontot med Azure Storage Explorer-verktyget.
* Azure Storage Explorer-verktyget kan laddas ned från [Microsoft Azure Lagringsutforskaren](https://storageexplorer.com/). Acceptera standardinställningarna under installationen.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Använda Azure Storage Explorer
Följande steg beskrivs hur du uppladdning/nedladdning data med hjälp av Azure Storage Explorer. 

1. Starta Microsoft Azure Storage Explorer.
2. Att ta fram den **logga in på ditt konto...**  väljer **Azure kontoinställningar** ikonen och sedan **Lägg till ett konto** och anger du autentiseringsuppgifter. 
![lägga till ett Azure Storage konto](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Öppna guiden Anslut till **Azure Storage** genom att välja ikonen **Anslut till Azure Storage** . ![på "Anslut till Azure Storage"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Ange åtkomst nyckeln från Azure Storage-kontot i guiden **Anslut till Azure Storage** och klicka sedan på **Nästa**. ![ange åtkomst nyckel från Azure Storage konto](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Ange lagringskontonamn i den **kontonamn** rutan och välj sedan **nästa**. ![Anslut extern lagring](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Det tillagda lagrings kontot bör nu visas. Skapa en blob-behållare i ett lagringskonto, högerklicka på den **Blob-behållare** nod i kontot, Välj **skapa Blobbehållare**, och ange ett namn.
7. Om du vill överföra data till en behållare väljer du mål behållaren och klickar på knappen **överför** .
![Lagringskonton](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klicka på den **...**  till höger om den **filer** väljer du en eller flera filer att ladda upp från filsystemet och klicka på **överför** att påbörja uppladdningen av filerna.![ Ladda upp filer](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Att hämta data, att välja blob i behållaren motsvarande för att ladda ned och klicka på **hämta**. ![Ladda ned filer](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

