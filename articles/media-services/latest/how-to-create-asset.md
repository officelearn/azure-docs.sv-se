---
title: Ladda upp innehåll till en Azure Media Services till gång med Azure CLI
description: Azure CLI-skriptet i det här avsnittet visar hur du skapar en Media Services-tillgång för att överföra innehåll till.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 65b0b276dcda58810f44494c14f06dd412d633f6
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719299"
---
# <a name="create-an-asset"></a>Skapa en tillgång

Den här artikeln visar hur du skapar en Media Services till gång.  Du kommer att använda en till gång för att lagra medie innehåll för kodning och strömning.  Läs mer om hur du Media Services till gångar genom att läsa [till gångar i Azure Media Services v3](assets-concept.md)

## <a name="prerequisites"></a>Förutsättningar

Följ stegen i [skapa ett Media Services konto](./create-account-howto.md) för att skapa ett Media Services konto och en resurs grupp som krävs för att skapa en till gång.

## <a name="methods"></a>Metoder

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>Använda REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Använda cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="using-postman"></a>Använda Postman

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services](media-services-overview.md)
