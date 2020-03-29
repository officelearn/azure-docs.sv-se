---
title: Flytta Blob-lagringsdata med Azure Storage Explorer – Team Data Science Process
description: Lär dig hur du använder Azure Storage Explorer för att ladda upp och hämta data från Azure blob storage.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720919"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Flytta data till och från Azure Blob Storage med Azure Storage Explorer
Azure Storage Explorer är ett kostnadsfritt verktyg från Microsoft som gör att du kan arbeta med Azure Storage-data på Windows, macOS och Linux. I det här avsnittet beskrivs hur du använder den för att ladda upp och hämta data från Azure blob storage. Verktyget kan hämtas från [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Om du använder virtuell dator som har konfigurerats med skript som tillhandahålls av [virtuella datavetenskapsdatorer i Azure,](virtual-machines.md)är Azure Storage Explorer redan installerat på den virtuella datorn.
> 
> [!NOTE]
> En fullständig introduktion till Azure blob-lagring finns i [Azure Blob Basics](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Krav
Det här dokumentet förutsätter att du har en Azure-prenumeration, ett lagringskonto och motsvarande lagringsnyckel för det kontot. Innan du laddar upp/laddar ned data måste du känna till ditt Azure Storage-kontonamn och kontonyckel. 

* Information om hur du konfigurerar en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/)finns i Kostnadsfri en månads provperiod .
* Instruktioner om hur du skapar ett lagringskonto och för att hämta konto- och nyckelinformation finns i [Om Azure Storage-konton](../../storage/common/storage-create-storage-account.md). Anteckna åtkomstnyckeln för ditt lagringskonto eftersom du behöver den här nyckeln för att ansluta till kontot med Azure Storage Explorer-verktyget.
* Azure Storage Explorer-verktyget kan hämtas från [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Acceptera standardinställningarna under installationen.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Använda Azure Storage Explorer
Följande steg dokumenterar hur du överför/hämtar data med Azure Storage Explorer. 

1. Starta Microsoft Azure Storage Explorer.
2. Om du vill visa inloggningen **till ditt konto...** väljer du ikonen **för Azure-kontoinställningar** och **sedan lägger till ett konto** och anger autentiseringsuppgifter för dig. 
![Lägga till ett Azure Storage-konto](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Om du vill visa guiden **Anslut till Azure Storage** väljer du ikonen Anslut till Azure **Storage.** ![Klicka på "Anslut till Azure Storage"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Ange åtkomstnyckeln från ditt Azure Storage-konto i guiden **Anslut till Azure Storage** och sedan **nästa**. ![Ange åtkomstnyckel från Azure Storage-konto](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Ange lagringskontonamn i rutan **Kontonamn** och välj sedan **Nästa**. ![Bifoga extern lagring](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Det lagringskonto som lagts till ska nu visas. Om du vill skapa en blob-behållare i ett lagringskonto högerklickar du på noden **Blob Containers** i det kontot, väljer **Skapa Blob Container**och anger ett namn.
7. Om du vill överföra data till en behållare markerar du målbehållaren och klickar på **knappen Ladda upp.**
![Lagringskonton](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klicka på **...** till höger om **rutan Filer,** välj en eller flera filer att ladda upp från filsystemet och klicka på **Ladda upp** för att börja ladda upp filerna. ![Ladda upp filer](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Om du vill hämta data väljer du blobben i motsvarande behållare för att hämta och klickar på **Hämta**. ![Hämta filer](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

