---
title: "Flytta Data till och från Blob Storage med Azure Lagringsutforskaren | Microsoft Docs"
description: "Flytta data till och från Azure Blob Storage med Azure Storage Explorer"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 3db06eed16191ef906f2c565d78fd0063cdb2f7b
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Flytta data till och från Azure Blob Storage med hjälp av Azure Lagringsutforskaren
Azure Lagringsutforskaren är ett kostnadsfritt verktyg från Microsoft som hjälper dig att arbeta med Azure Storage data på Windows-, macOS- och Linux. Det här avsnittet beskriver hur du använder den för att ladda upp och hämta data från Azure blob storage. Verktyget kan hämtas från [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Om du använder en virtuell dator som har konfigurerats med skript som tillhandahålls av [datavetenskap virtuella datorer i Azure](virtual-machines.md), och sedan Azure Lagringsutforskaren redan är installerad på den virtuella datorn.
> 
> [!NOTE]
> En fullständig introduktion till Azure blob storage finns i [grunderna i Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob-tjänsten](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Krav
Det här dokumentet förutsätter att du har en Azure-prenumeration, ett lagringskonto och motsvarande lagringsnyckel för det kontot. Innan du laddar upp/hämtar data, måste du känna till din Azure storage-konto och nyckel. 

* Om du vill konfigurera en Azure-prenumeration finns [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett lagringskonto och för att hämta konto och viktig information, se [om Azure storage-konton](../../storage/common/storage-create-storage-account.md). Notera snabbtangenten för ditt lagringskonto som du behöver denna nyckel för att ansluta till kontot med verktyget Azure Lagringsutforskaren.
* Verktyget Azure Lagringsutforskaren kan hämtas från [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/). Godkänn standardinställningarna under installationen.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Använda Azure Lagringsutforskaren
Följande steg beskrivs hur du överför/hämta data med hjälp av Azure Lagringsutforskaren. 

1. Starta Microsoft Azure Lagringsutforskaren.
2. Så att den **logga in på ditt konto...**  väljer **Azure kontoinställningar** ikonen sedan **Lägg till ett konto** och ange autentiseringsuppgifter. ![Lägg till ett Azure storage-konto](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Så att den **Anslut till Azure Storage** guiden väljer du den **Anslut till Azure storage** ikon. ![Ansluta till Azure-lagring](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Ange snabbtangent från Azure storage-konto den **Anslut till Azure Storage** guiden och sedan **nästa**. ![Ansluta till Azure-lagring](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Ange lagringskontonamn i den **kontonamn** rutan och välj sedan **nästa**. ![Anslut extern lagring](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Lagringskontot läggs ska nu visas. Skapa en blob-behållare i ett lagringskonto, högerklicka på den **Blob-behållare** nod på det kontot, Välj **skapa Blob-behållaren**, och ange ett namn.
7. Markera Målbehållaren som för att överföra data till en behållare, och klicka på den **överför** knappen.![ Storage-konton](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klicka på den **...**  till höger om den **filer** väljer du en eller flera filer som ska överföra från filsystemet och klicka på **överför** att börja överföra filerna.![ Ladda upp filer](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Att hämta data, att välja blob i motsvarande behållare för att hämta och klickar på **hämta**. ![Hämta filer](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

