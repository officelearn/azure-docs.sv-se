---
title: Hantera Azure-filresurser med Azure Portal
description: Lär dig hur du använder Azure-portalen för att hantera Azure Files.
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
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 24996e80d2e75978a814bbf471176d4edcf22549
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38479180"
---
# <a name="managing-azure-file-shares-with-the-azure-portal"></a>Hantera Azure-filresurser med Azure Portal 
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Windows, Linux och macOS. Den här guiden vägleder dig igenom grunderna i att arbeta med Azure-filresurser med [Azure Portal](https://portal.azure.com/). Lär dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp och ett lagringskonto
> * Skapa en Azure-filresurs 
> * Skapa en katalog
> * Överför en fil 
> * Hämta en fil
> * Skapa och använda en ögonblicksbild av en resurs

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-a-file-share"></a>Skapa en filresurs
Så här skapar du en filresurs:

1. Välj lagringskonto från instrumentpanelen.
2. På sidan för lagringskontot i avsnittet **Tjänster** väljer du **Filer**.
    ![En skärmbild av avsnittet Tjänster i lagringskontot; välj Filtjänst](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. På menyn överst på sidan **Filtjänst** klickar du på **+ filresurs**. Sidan **Ny filresurs** rullas ned.
4. I **namn** skriver du *myshare*.
5. Klicka på **OK** för att skapa Azure-filresursen.

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Arbeta med innehållet i Azure-filresursen
Nu när du har skapat en Azure-filresurs kan du montera filresursen med SMB på [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) eller [macOS](storage-how-to-use-files-mac.md). Du kan också arbeta med din Azure-filresurs med Azure Portal. Alla begäranden som görs via Azure Portal görs med REST API-filen så att du kan skapa, ändra och ta bort filer och kataloger på klienter utan SMB-åtkomst.

### <a name="create-a-directory"></a>Skapa en katalog
Skapa en ny katalog med namnet *myDirectory* vid roten på Azure-filresursen:

1. På sidan **Filtjänst** väljer du filresursen **myshare**. Då öppnas sidan för filresursen.
2. På menyn överst på sidan väljer du **+ Lägg till katalog**. Sidan **Ny katalog** rullas ned.
3. Skriv *myDirectory* och klicka sedan på **OK**.

### <a name="upload-a-file"></a>Överför en fil 
För att ladda upp en fil, måste du först skapa eller välja en fil som ska laddas upp. Du kan göra detta på det sätt du känner är lämpligt. När du har valt den fil som du vill ladda upp:

1. Klicka på katalogen **myDirectory**. Panelen **myDirectory** öppnas.
2. På den översta menyn, klicka på **Ladda upp**. Panelen **Ladda upp filer** öppnas.  
    ![En skärmbild av panelen ladda upp filer](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Klicka på mappikonen för att öppna ett fönster för att bläddra bland dina lokala filer. 
4. Välj en fil och klicka sedan på **Öppna**. 
5. På sidan **Ladda upp filer**, kontrollera filnamnet och klicka sedan på **Ladda upp**.
6. När du är klar ska filen visas i listan på sidan **myDirectory**.

### <a name="download-a-file"></a>Hämta en fil
Du kan hämta en kopia av filen du överförde genom att högerklicka på filen. När du klickar på knappen ladda ner, beror den exakta upplevelsen på det operativsystem och den webbläsare du använder.

## <a name="create-and-modify-share-snapshots"></a>Skapa och ändra resursögonblicksbilder
Ytterligare en användbar uppgift som du kan göra med en Azure-filresurs är att skapa resursögonblicksbilder. En ögonblicksbild bevarar en tidpunkt för en Azure-filresurs. Ögonblicksbilder av resurser liknar de operativsystemtekniker som du kanske redan är bekant med såsom:
- [Tjänsten Volume Shadow Copy(VSS)](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal) för Windows-filsystem, till exempel NTFS och ReFS
- [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) ögonblicksbilder för Linux-system
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) ögonblicksbilder för macOS. 

Så här skapar du en ögonblicksbild av en resurs:

1. Öppna sidan för filresursen genom att öppna lagringskontot från instrumentpanelen > **Filer** > **myshare**. 
2. På sidan för filresursen klickar du på knappen **Ögonblicksbild** på menyn längst upp på sidan och väljer sedan **Skapa en ögonblicksbild**.  
    ![En skärmbild illustrerar hur du hittar knappen Skapa ögonblicksbild](media/storage-how-to-use-files-portal/create-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Visa lista över och bläddra bland ögonblicksbilder av resurser
När ögonblicksbilden har skapats kan du klicka på **Ögonblicksbild** igen och sedan välja **Visa ögonblicksbilder** för att visa en lista över ögonblicksbilder för resursen. Fönstret som öppnas kommer att visa ögonblicksbilder för den här resursen. Klicka på en ögonblicksbild av en resurs för att bläddra i den.

### <a name="restore-from-a-share-snapshot"></a>Återställ från en resursögonblicksbild
För att demonstrera återställning av en fil från en ögonblicksbild av en resurs, behöver vi först ta bort en fil från live-Azure-filresursen. Navigera till mappen *myDirectory*, högerklicka på den uppladdade filen, och klicka sedan på **Ta bort**. Sedan för att återställa filen från ögonblicksbilden för resursen:

1. Klicka på **Ögonblicksbilder** i den översta menyn och välj **Visa ögonblicksbilder**. 
2. Klicka på den ögonblicksbild som du skapade tidigare så öppnas innehållet på en ny sida. 
3. Klicka på **myDirectory** i ögonblicksbilden och du bör se den fil som du har tagit bort. 
4. Högerklicka på den borttagna filen och välj **Återställ**.
5. Ett popup-fönster visas med olika val för att återställa filen som en kopia eller skriva över den ursprungliga filen. Eftersom vi har tagit bort den ursprungliga filen, väljer vi **Skriv över originalfilen** för att återställa filen som den var innan vi tog bort den. Klicka på **OK** för att återställa filen till Azure-filresursen.  
    ![En skärmbild av dialogrutan för att återställa filen](media/storage-how-to-use-files-portal/restore-snapshot-1.png)

6. När filen återställts, stäng sidan för ögonblicksbilden och gå tillbaka till **myshare** > **myDirectory** och filen bör vara tillbaka på sin ursprungliga plats.

### <a name="delete-a-share-snapshot"></a>Ta bort en resursögonblicksbild
För att ta bort en ögonblicksbild av en resurs, [navigerar du till listan över ögonblicksbilder av resurser](#list-and-browse-a-share-snapshot). Klicka på kryssrutan bredvid namnet på ögonblicksbilden av resursen och välj knappen **Ta bort**.

![En skärmbild över att ta bort ögonblicksbild av resurs](media/storage-how-to-use-files-portal/delete-snapshot-1.png)

## <a name="clean-up-resources"></a>Rensa resurser
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg
- [Hantera filresurser med Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Hantera filresurser med Azure-CLI](storage-how-to-use-files-cli.md)
- [Hantera filresurser med Azure Storage Explorer](storage-how-to-use-files-storage-explorer.md)
- [Planera för en Azure Files-distribution](storage-files-planning.md)