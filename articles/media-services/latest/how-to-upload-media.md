---
title: Ladda upp media
titleSuffix: Azure Media Services
description: Lär dig hur du laddar upp media för strömning eller kodning.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: inhenkel
ms.openlocfilehash: 3da313803aa546c2399e3c8c18858bee5cfa2ea1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684379"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Ladda upp media för strömning eller kodning
I Media Services laddar du upp digitala filer (Media) i en BLOB-behållare som är kopplad till en till gång. [Till gångs](/rest/api/media/operations/asset) enheten kan innehålla video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning (samt metadata om dessa filer). När filerna har överförts till till gångens behållare lagras innehållet på ett säkert sätt i molnet för vidare bearbetning och strömning.

Innan du börjar kan du behöva samla in eller tänka på några värden.

1. Den lokala fil Sök vägen till filen som du vill ladda upp
1. Till gångens ID för till gången (container)
1. Det namn som du vill använda för den överförda filen inklusive tillägget
1. Namnet på det lagrings konto som du använder
1. Åtkomst nyckeln för ditt lagrings konto

## <a name="portal"></a>[Portal](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="cli-shell"></a>[CLI-gränssnitt](#tab/clishell/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli-shell.md)]

## <a name="rest"></a>[REST](#tab/rest/)

När du har [skapat en till gång med Postman eller andra REST-metoder och fått SAS-URL: en för till gången](how-to-create-asset.md?tabs=rest)använder du Azure Storage API: er eller SDK: er (till exempel [lagrings REST API](../../storage/common/storage-rest-api-auth.md) eller [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

---
<!-- add these to the tabs when available -->
Andra metoder finns i [Azure Storage dokumentationen](https://docs.microsoft.com/azure/storage/blobs/) för att arbeta med blobbar i [.net](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), [python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)och [Java Script (Node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs).

## <a name="next-steps"></a>Nästa steg

> [Översikt över Media Services](media-services-overview.md)
