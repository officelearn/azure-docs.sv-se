---
title: Snabb start – skapa en blob med Azure Portal
titleSuffix: Azure Storage
description: I den här snabbstarten använder du Azure-portalen i objektlagring (Blob). Sedan använder du Azure-portalen och laddar upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en container.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 04/16/2020
ms.author: tamram
ms.openlocfilehash: a9a048801c6bea74e6b5318eeedf76f92709bee6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535185"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Snabbstart: Ladda upp, ladda ned och lista blobar med hjälp av Azure-portalen

I den här snabbstarten får du lära dig att använda [Azure Portal](https://portal.azure.com/) för att skapa en container i Azure Storage, samt ladda upp och ladda ned blockblobar i den containern.

## <a name="prerequisites"></a>Krav

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Skapa en container

Följ dessa steg för att skapa en container i Azure Portal:

1. Navigera till ditt nya lagringskonto i Azure Portal.
2. I den vänstra menyn för lagrings kontot, bläddrar du till avsnittet **BLOB service** och väljer **behållare**.
3. Välj knappen **+ Container**.
4. Ange ett namn för den nya containern. Containernamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-). Mer information om behållare och blob-namn finns i [namngivning och referens av behållare, blobbar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Ställ in nivån för allmän åtkomst till containern. Standardnivån är **Privat (ingen anonym åtkomst)**.
6. Klicka på **OK** för att skapa containern.

    ![Skärmbild som visar hur du skapar en container i Azure Portal](media/storage-quickstart-blobs-portal/create-container.png)

## <a name="upload-a-block-blob"></a>Ladda upp en blockblob

Blockblobar består av datablock som har satts samman för att bilda en blob. De flesta scenarier med bloblagring använder blockblobar. Blockblobar är utmärkta för att lagra text eller binära data i molnet, t.ex. filer, avbildningar och videor. Den här snabbstarten visar hur du arbetar med blockblobar.

Följ de här stegen för att ladda upp en blockblob till den nya containern i Azure Portal:

1. Navigera till den container som du skapade i föregående avsnitt i Azure Portal.
1. Välj containern för att visa en lista över blobar som den innehåller. Den här behållaren är ny, så den kommer ännu inte att innehålla några blobbar.
1. Välj knappen **överför** för att öppna bladet överför och bläddra i det lokala fil systemet för att hitta en fil som ska laddas upp som en Block-Blob. Du kan också expandera avsnittet Avancerat om du vill konfigurera andra inställningar för överförings åtgärden.

    ![Skärmbild som visar uppladdning av en blob från den lokala disken](media/storage-quickstart-blobs-portal/upload-blob.png)

1. Välj knappen **överför** för att överföra blobben.
1. Ladda upp så många blobar du vill på det här sättet. Du kan se att de nya blobarna nu visas i listan i containern.

## <a name="download-a-block-blob"></a>Ladda ned en blockblob

Du kan ladda ned en blockblob som ska visas i webbläsaren eller spara den i det lokala filsystemet. Följ de här stegen om du vill ladda ned en blockblob:

1. Gå till listan över blobar som du överförde i föregående avsnitt.
1. Högerklicka på den blob som du vill ladda ned och välj **Ladda ned**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de resurser du har skapat i den här snabbstarten kan du ta bort containern. Alla blobar i containern tas också bort.

Ta bort containern:

1. Gå till listan med containrar i ditt lagringskonto i Azure Portal.
1. Välj den container som ska tas bort.
1. Välj knappen **Mer** (**...**) och välj **Ta bort**.
1. Bekräfta att du vill ta bort containern.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med hjälp av Azure-portalen. Om du vill veta mer om att arbeta med Blob Storage kan du fortsätta till anvisningarna om Blob Storage.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](storage-dotnet-how-to-use-blobs.md)
