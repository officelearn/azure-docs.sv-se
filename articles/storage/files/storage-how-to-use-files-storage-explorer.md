---
title: Hantera Azure-filresurser med Azure Storage Explorer
description: Lär dig att hantera Azure Files med Azure Storage Explorer.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.openlocfilehash: f628f393f1ff5922d9d2c82b30d4fdbf4b5caf23
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738181"
---
# <a name="manage-azure-file-shares-with-azure-storage-explorer"></a>Hantera Azure-filresurser med Azure Storage Explorer 
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Den här artikeln vägleder dig igenom grunderna i att arbeta med Azure-filresurser med [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Storage Explorer är ett populärt klientverktyg som finns för Windows, macOS och Linux. Du kan använda Storage Explorer för att hantera Azure-filresurser och andra lagringsresurser.

För den här snabbstarten krävs det att Storage Explorer är installerat. Hämta och installera det genom att gå till [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en resursgrupp och ett lagringskonto
> * Skapa en Azure-filresurs 
> * Skapa en katalog
> * Överför en fil
> * Hämta en fil
> * Skapa och använda en ögonblicksbild av en resurs

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Du kan inte skapa nya resurser med Storage Explorer. can't use Storage Explorer to create new resources. Skapa lagringskontot i [Azure-portalen](https://portal.azure.com/) för den här demon. 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Ansluta Storage Explorer till Azure-resurser
När du först startar Storage Explorer visas fönstret **Microsoft Azure Storage Explorer – anslut**. I Storage Explorer finns flera olika sätt att ansluta till lagringskonton: 

- **Logga in med Azure-kontot**: Du kan logga in med autentiseringsuppgifterna för din organisation eller ditt Microsoft-konto. 
- **Anslut till ett specifikt lagringskontot med en anslutningssträng eller SAS-token**: En anslutningssträng är en speciell sträng som innehåller ett lagringskontonamn och en lagringskontonyckel/SAS-token. Med denna token får Storage Explorer direktåtkomst till lagringskontot (istället för att bara se alla lagringskonton på Azure-kontot). Mer information om anslutningssträngar finns i [Konfigurera anslutningssträngar för Azure Storage](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Ansluta till ett specifikt lagringskonto med ett lagringskontonamn och nyckel**: Anslut till Azure Storage genom att använda lagringskontonamnet och nyckeln till ditt lagringskonto.

I den här snabbstarten loggar vi för enkelhetens skull in med ditt Azure-konto. Välj **Lägg till ett Azure-konto** och välj sedan **Logga in**. Följ anvisningarna för att logga in på ditt Azure-konto.

![En skärmbild av fönstret Microsoft Azure Storage Explorer – anslut](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Skapa en filresurs
Skapa din första Azure-filresurs inom lagringskontot *storageacct<random number>*:

1. Expandera det lagringskonto du har skapat.
2. Högerklicka på **Filresurser** och välj **Skapa filresurs**.  
    ![En skärmbild av filresursmappen och snabbmenyn](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. För filresursen anger du *myshare* och trycker på Retur.

> [!IMPORTANT]  
> Resursnamn får bara innehålla gemener, siffror och enskilda bindestreck (men får inte inledas med bindestreck). Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

När filresursen har skapats öppnas en flik för filresursen i den högra rutan. 

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Arbeta med innehållet i Azure-filresursen
Nu när du har skapat en Azure-filresurs kan du montera filresursen med SMB på [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) eller [macOS](storage-how-to-use-files-mac.md). Du kan också arbeta med din Azure-filresurs med Azure CLI. Fördelen med att använda Azure CLI istället för att montera filresursen med SMB är att alla förfrågningar som görs med Azure CLI görs med fil-REST-API:n. Du kan använda fil-REST-API:n för att skapa, ändra och ta bort filer och kataloger på klienter som inte har SMB-åtkomst.

### <a name="create-a-directory"></a>Skapa en katalog
Om du lägger till en katalog får du en hierarkisk struktur där du kan hantera din filresurs. Du kan skapa flera nivåer i din katalog. Men du måste se till att de överordnade katalogerna finns måste du skapa underkataloger. För sökvägen myDirectory/mySubDirectory måste du t.ex. först skapa katalogen *myDirectory*. Därefter kan du skapa *mySubDirectory*. 

1. Klicka på knappen **Ny mapp** på toppmenyn på filresursens flik. Fönstret **Skapa ny katalog**.
    ![En skärmbild av knappen Ny mapp i sitt sammanhang](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. För katalognamnet anger du *myDirectory* och väljer **OK**. 

Katalogen *myDirectory* visas på fliken för filresursen *myshare*.

### <a name="upload-a-file"></a>Överför en fil 
Du kan överföra en fil från din lokala dator till den nya katalogen i filresursen. Du kan ladda upp en hel mapp eller en enda fil.

1. I menyn högst upp väljer du **Ladda upp**. Den här åtgärden ger dig möjlighet att ladda upp en mapp eller fil.
2. Välj **Överför fil** och markera den fil på din lokala dator som ska överföras.
3. Skriv *myDirectory* i **Överför till en katalog** och klicka sedan på **Överför**. 

När du är klar visas filen i fönstret *myDirectory*.

### <a name="download-a-file"></a>Hämta en fil
Om du vill ladda ned en kopia av en fil från din filresurs högerklickar du på filen och väljer därefter **Ladda ned**. Ange var du vill placera filen på den lokala datorn och klicka sedan på **Spara**.

Hämtningsförloppet visas i rutan **Aktiviteter** längst ned i fönstret.

## <a name="create-and-modify-share-snapshots"></a>Skapa och ändra resursögonblicksbilder
En ögonblicksbild bevarar en kopia vid en viss tidpunkt av en Azure-filresurs. Ögonblicksbilder av filresurser liknar vissa andra tekniker som du kanske redan är bekant med:
- [Tjänsten Volume Shadow Copy(VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) för Windows-filsystem, till exempel NTFS och ReFS
- [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) ögonblicksbilder för Linux-system
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) ögonblicksbilder för macOS

Så här skapar du en ögonblicksbild av en resurs:

1. Välj fliken för filresursen *myshare*.
2. I menyn högst upp väljer du **Skapa ögonblicksbild**. (Du kanske först måste välja **Mer** för att se det här alternativet, beroende på fönstrets dimensioner i Storage Explorer.)  
    ![En skärmbild av den knappen Skapa ögonblicksbild i sitt sammanhang](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Visa lista över och bläddra bland ögonblicksbilder av resurser
När ögonblicksbilden har skapats kan du klicka på **Visa ögonblicksbilder för filresurs** för att visa en lista över ögonblicksbilder för resursen. (Du kanske först måste välja **Mer** för att se det här alternativet, beroende på fönstrets dimensioner i Storage Explorer.) Dubbelklicka på ögonblicksbilden om du vill bläddra i den.

![En skärmbild av bläddringsfönstret för ögonblicksbilder](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Återställ från en resursögonblicksbild
För att demonstrera återställning av en fil från en ögonblicksbild av en resurs, behöver du först ta bort en fil från live-Azure-filresursen. Gå till mappen *myDirectory*, högerklicka på den uppladdade filen och klicka sedan på **Ta bort**. Så här återställer du filen från ögonblicksbilden för resursen:

1. Välj **Visa ögonblicksbilder för filresurs**. (Du kanske först måste välja **Mer** för att se det här alternativet, beroende på fönstrets dimensioner i Storage Explorer.)
2. I listan med resursögonblicksbilder dubbelklickar du på resursens ögonblicksbild.
3. Bläddra i ögonblicksbilden tills du hittar filen du tog bort. Välj filresursen och välj därefter **Återställ ögonblicksbild**. (Du kanske först måste välja **Mer** för att se det här alternativet, beroende på fönstrets dimensioner i Storage Explorer.) Ett fönster öppnas som visar en varning om att om du återställer filen så skrivs innehållet i filresursen över, och att det inte går att ångra detta. Välj **OK**.
4. Nu bör filen finnas på sin ursprungliga plats och under den levande Azure-filresursen.

### <a name="delete-a-share-snapshot"></a>Ta bort en resursögonblicksbild
För att ta bort en ögonblicksbild av en resurs [går du till listan över ögonblicksbilder av resurser](#list-and-browse-share-snapshots). Högerklicka på den resursögonblicksbild du vill ta bort och välj **Ta bort**.

## <a name="clean-up-resources"></a>Rensa resurser
Du kan inte ta bort resurser med Storage Explorer. Om du vill rensa från den här snabbstarten kan du använda [Azure-portalen](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg
- [Hantera filresurser med Azure Portal](storage-how-to-use-files-portal.md)
- [Hantera filresurser med Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Hantera filresurser med Azure CLI](storage-how-to-use-files-cli.md)
- [Planera för en Azure Files-distribution](storage-files-planning.md)