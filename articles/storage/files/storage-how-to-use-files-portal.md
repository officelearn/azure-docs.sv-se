---
title: Snabbstart för hantering av Azure-filresurser med Azure-portalen
description: I den här snabbstarten lär du dig hur du använder Azure-portalen till att hantera Azure Files.
services: storage
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 69e7dba06fce14c2411a82dfade344ba1b14a6d3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698753"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Snabbstart: Skapa och hantera Azure-filresurser med Azure-portalen 
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Windows, Linux och macOS. Den här guiden vägleder dig igenom grunderna i att arbeta med Azure-filresurser med [Azure Portal](https://portal.azure.com/).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Skapa en Azure-filresurs:

1. Välj lagringskonto från instrumentpanelen.
2. På sidan för lagringskontot i avsnittet **Tjänster** väljer du **Filer**.
    ![En skärmbild av avsnittet Tjänster i lagringskontot; välj Filtjänst](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. På menyn överst på sidan **Filtjänst** klickar du på **+ filresurs**. Sidan **Ny filresurs** rullas ned.
4. I **namn** skriver du *myshare*.
5. Klicka på **OK** för att skapa Azure-filresursen.

Resursnamn får bara innehålla gemener, siffror och enskilda bindestreck, men får inte inledas med bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Använda Azure-filresursen
Azure Files har två metoder för att arbeta med filer och mappar i din Azure-filresurs: branschstandardprotokollen [SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) och [fil-REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Om du vill montera en filresurs med SMB läser du följande dokument baserat på ditt operativsystem:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Använda en Azure-filresurs via Azure-portalen
Alla begäranden som görs via Azure Portal görs med REST API-filen så att du kan skapa, ändra och ta bort filer och kataloger på klienter utan SMB-åtkomst. Det är möjligt att arbeta direkt med fil-REST-protokollet (dvs. att själv skapa REST HTTP-anrop manuellt) men det vanligaste sättet (förutom att använda Azure-portalen) att använda fil-REST-protokollet är att använda [Azure PowerShell-modulen](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md) eller ett Azure Storage-SDK. Alla ger en bra omslutning runt fil-REST-protokollet i valfritt skript-/programmeringsspråk. 

Vi förväntar att de flesta som använder Azure Files vill arbeta med sin Azure-filresurs via SMB-protokollet, eftersom det gör att de kan använda de befintliga programmen och verktygen som de förväntar sig att kunna använda. Men det finns flera anledningar till varför det är fördelaktigt att använda fil-REST-API:et istället för SMB, till exempel:

- Du måste göra en snabb ändring av Azure-filresursen i farten, till exempel via en bärbar dator utan SMB-åtkomst, en surfplatta eller en mobil enhet.
- Du måste köra ett skript eller program från en klient som inte kan montera en SMB-resurs, till exempel lokala klienter, som inte har port 445 avblockerad.
- Du utnyttjar serverlösa resurser, som [Azure Functions](../../azure-functions/functions-overview.md). 

I följande exempel visas hur du använder Azure-portalen till att ändra din Azure-filresurs med fil-REST-protokollet. 

Nu när du har skapat en Azure-filresurs kan du montera filresursen med SMB på [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) eller [macOS](storage-how-to-use-files-mac.md). Du kan också arbeta med din Azure-filresurs med Azure Portal. 

#### <a name="create-a-directory"></a>Skapa en katalog
Skapa en ny katalog med namnet *myDirectory* vid roten på Azure-filresursen:

1. På sidan **Filtjänst** väljer du filresursen **myshare**. Då öppnas sidan för filresursen.
2. På menyn överst på sidan väljer du **+ Lägg till katalog**. Sidan **Ny katalog** rullas ned.
3. Skriv *myDirectory* och klicka sedan på **OK**.

#### <a name="upload-a-file"></a>Överför en fil 
För att ladda upp en fil, måste du först skapa eller välja en fil som ska laddas upp. Du kan göra detta på det sätt du känner är lämpligt. När du har valt den fil som du vill ladda upp:

1. Klicka på katalogen **myDirectory**. Panelen **myDirectory** öppnas.
2. På den översta menyn, klicka på **Ladda upp**. Panelen **Ladda upp filer** öppnas.  
    ![En skärmbild av panelen ladda upp filer](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Klicka på mappikonen för att öppna ett fönster för att bläddra bland dina lokala filer. 
4. Välj en fil och klicka sedan på **Öppna**. 
5. På sidan **Ladda upp filer**, kontrollera filnamnet och klicka sedan på **Ladda upp**.
6. När du är klar ska filen visas i listan på sidan **myDirectory**.

#### <a name="download-a-file"></a>Hämta en fil
Du kan ladda ned en kopia av filen du överförde genom att högerklicka på filen. När du klickar på knappen ladda ner, beror den exakta upplevelsen på det operativsystem och den webbläsare du använder.

## <a name="clean-up-resources"></a>Rensa resurser
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är Azure Files?](storage-files-introduction.md)
