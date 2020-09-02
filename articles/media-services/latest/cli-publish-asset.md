---
title: Exempel på Azure CLI-skript – Publicera en tillgång | Microsoft Docs
description: Den här artikeln visar hur du använder Azure CLI-skriptet för att publicera en till gång.
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3b3c358a84dd74595c476f029a1c8f28bc3c901f
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295877"
---
# <a name="cli-example-publish-an-asset"></a>CLI-exempel: Publicera en tillgång

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure CLI-skriptet i den här artikeln visar hur du skapar en strömningspositionerare och får tillbaka strömnings-URL:er. 

## <a name="prerequisites"></a>Förutsättningar 

[Skapa ett Media Services-konto](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services](media-services-overview.md)
