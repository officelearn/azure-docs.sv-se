---
title: Exempel på Azure CLI-skript – Publicera en tillgång | Microsoft Docs
description: Använd Azure CLI-skriptet för att publicera en tillgång.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 5394ec12e0a11ac68585dcd64dc5255c38249937
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161371"
---
# <a name="cli-example-publish-an-asset"></a>CLI-exempel: Publicera en tillgång

Azure CLI-skriptet i den här artikeln visar hur du skapar en strömningspositionerare och får tillbaka strömnings-URL:er. 

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
