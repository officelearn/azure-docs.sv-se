---
title: Hantera Azure-filresurser med Azure Storage Explorer
description: Lär dig att hantera Azure Files med Azure Storage Explorer.
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.openlocfilehash: 0c16d3b0ef0b178f99eaafecd74eabb00cca5af9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-azure-storage-explorer"></a>Hantera Azure-filresurser med Azure Storage Explorer 
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Den här guiden vägleder dig igenom grunderna i att arbeta med Azure-filresurser med [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Azure Storage Explorer är ett populärt klientverktyg som är tillgängligt för Windows, macOS och Linux och som används för att hantera Azure-filresurser och andra lagringsresurser.

För den här snabbstarten krävs det att Azure Storage Explorer är installerat. Om du behöver installera det kan du gå till [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) och ladda ned det.

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa en resursgrupp och ett lagringskonto
> * Skapa en Azure-filresurs 
> * Skapa en katalog
> * Överför en fil
> * Hämta en fil
> * Skapa och använda en ögonblicksbild av en resurs

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Azure Storage Explorer kan inte skapa nya resurser, så i den här demonstrationen skapar vi lagringskontot med [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connecting-azure-storage-explorer-to-azure-resources"></a>Ansluta Azure Storage Explorer till Azure-resurser
Första gången programmet startas visas fönstret **Microsoft Azure Storage Explorer – anslut**. I Azure Storage Explorer kan du ansluta till lagringskonton på flera olika sätt: 

- **Logga in med ditt Azure-konto**: Detta innebär att du kan logga in med dina autentiseringsuppgifter för ditt företag eller ditt Microsoft-konto. 
- **Ansluta till ett specifikt lagringskonto med en anslutningssträng eller SAS-token**: En anslutningssträng är en särskild sträng som innehåller lagringskontonamn och lagringskontonyckel/SAS-token som gör att Azure Storage Explorer får direkt åtkomst till lagringskontot (istället för att visa alla lagringskonton i ett Azure-konto). Mer information om anslutningssträngar finns i [Konfigurera anslutningssträngar för Azure Storage](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Ansluta till ett specifikt lagringskonto med ett lagringskontonamn och nyckel**: Anslut till Azure Storage genom att använda lagringskontonamnet och nyckeln till ditt lagringskonto.

I den här snabbstarten loggar vi för enkelhetens skull in med ditt Azure-konto. Välj **Lägg till ett Azure-konto** och klicka på **Logga in...**. Följ anvisningarna på skärmen för att logga in på ditt Azure-konto.

![Fönstret Microsoft Azure Storage Explorer – anslut](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Skapa en filresurs
Skapa din första Azure-filresurs inom lagringskontot *storageacct<random number>*:

1. Expandera det lagringskonto du har skapat.
2. Högerklicka på **Filresurser** och välj **Skapa filresurs**.  
    ![En skärmbild av filresursmappen och snabbmenyn](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Skriv *myshare* för filresursen och tryck på **Retur**.

> [!Important]  
> Resursnamn får bara innehålla gemener, siffror och enskilda bindestreck, men får inte inledas med bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

När filresursen har skapats öppnas en flik för filresursen i den högra rutan. 

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Hantera innehållet i Azure-filresursen
Nu när du har skapat en Azure-filresurs kan du montera filresursen med SMB på [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) eller [macOS](storage-how-to-use-files-mac.md). Du kan också ändra din Azure-filresurs med Azure Portal. Alla begäranden som görs via Azure Portal görs med REST API-filen så att du kan skapa, ändra och ta bort filer och kataloger på klienter utan SMB-åtkomst.

### <a name="create-a-directory"></a>Skapa en katalog
Om du lägger till en katalog får du en hierarkisk struktur där du kan hantera din filresurs. Du kan skapa flera nivåer, men du måste se till att alla överordnade kataloger finns innan du skapar en underkatalog. För sökvägen myDirectory/mySubDirectory måste du t.ex. först skapa katalogen *myDirectory*, och sedan skapa *mySubDirectory*. 

1. Klicka på knappen **+ Ny mapp** på toppmenyn på filresursens flik. Sidan **Skapa ny katalog** öppnas.
    ![En skärmbild av knappen Ny mapp i sitt sammanhang](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Skriv *myDirectory* och klicka sedan på **OK**. 

Katalogen *myDirectory* visas på fliken för filresursen *myshare*.

### <a name="upload-a-file"></a>Överför en fil 
Överför en fil från din lokala dator till den nya katalogen i filresursen. Du kan ladda upp en hel mapp eller bara en enda fil.

1. Klicka på **Ladda upp** på den översta menyn. Den här åtgärden ger dig möjlighet att ladda upp en mapp eller fil.

2. Välj **Överför fil** och markera den fil på din lokala dator som ska överföras.

3. Skriv *myDirectory* i **Överför till en katalog** och klicka sedan på **Överför**. 

När du är klar ska filen visas i listan på sidan **myDirectory**.

### <a name="download-a-file"></a>Hämta en fil
Du kan hämta en kopia av en fil i filresursen genom att högerklicka på filen och välja **Hämta**. Ange var du vill placera filen på den lokala datorn och klicka sedan på **Spara**.

Hämtningsförloppet visas i rutan **Aktiviteter** längst ned i fönstret.

## <a name="create-and-modify-share-snapshots"></a>Skapa och ändra resursögonblicksbilder
En ögonblicksbild bevarar en kopia vid en viss tidpunkt av en Azure-filresurs. Ögonblicksbilder av filresurser liknar vissa andra tekniker som du kanske redan är bekant med:
- [Tjänsten Volume Shadow Copy (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) för Windows-filsystem, till exempel NTFS och ReFS.
- [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) ögonblicksbilder för Linux-system
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) ögonblicksbilder för macOS. 

Så här skapar du en ögonblicksbild av en resurs:

1. Öppna fliken för filresursen *myshare*.
2. Klicka på **Skapa ögonblicksbild** på menyn högst upp på fliken. (Detta kan vara dolt bakom en **... Flera** beroende på fönstrets mått i Azure Storage Explorer).  
    ![En skärmbild av den knappen Skapa ögonblicksbild i sitt sammanhang](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Visa lista över och bläddra bland ögonblicksbilder av resurser
När du har skapat ögonblicksbilden kan du klicka på **Visa ögonblicksbilder för filresurs** (Detta kan vara dolt bakom en **... Flera** beroende på fönstret mått i Azure Storage Explorer) om du vill lista resursens ögonblicksbilder. Dubbelklicka på en resursögonblicksbild om du vill bläddra i den.

![En skärmbild av bläddringsskärmen för ögonblicksbilder](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Återställ från en resursögonblicksbild
För att demonstrera återställning av en fil från en ögonblicksbild av en resurs, behöver vi först ta bort en fil från live-Azure-filresursen. Navigera till mappen *myDirectory*, högerklicka på den uppladdade filen, och klicka sedan på **Ta bort**. Sedan för att återställa filen från ögonblicksbilden för resursen:

1. Klicka på **Visa ögonblicksbilder för filresurs** (Detta kan vara dolt bakom en **... Flera** beroende på fönstrets mått i Azure Storage Explorer).
2. Välj en resursögonblicksbild i listan och navigera genom att dubbelklicka.
3. Navigera i ögonblicksbilden tills du hittar den fil som du har tagit bort, markera den och klicka sedan på **Återställ ögonblicksbild** (Detta kan vara dolt bakom en **... Flera** beroende på fönstrets mått i Azure Storage Explorer). Du får en varning om att om du återställer filen så skrivs innehållet i filresursen över, och att det inte går att ångra detta. Välj **OK**.
4. Nu bör filen finnas på sin ursprungliga plats och under den levande Azure-filresursen.

### <a name="delete-a-share-snapshot"></a>Ta bort en resursögonblicksbild
För att ta bort en ögonblicksbild av en resurs, [navigerar du till listan över ögonblicksbilder av resurser](#list-and-browse-share-snapshots). Högerklicka på den resursögonblicksbild du vill ta bort och välj Ta bort.

## <a name="clean-up-resources"></a>Rensa resurser
Azure Storage Explorer kan inte ta bort resurser. Du kan rensa upp efter den här snabbstarten med [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg
- [Hantera filresurser med Azure Portal](storage-how-to-use-files-portal.md)
- [Hantera filresurser med Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Hantera filresurser med Azure CLI](storage-how-to-use-files-cli.md)
- [Planera för en Azure Files-distribution](storage-files-planning.md)