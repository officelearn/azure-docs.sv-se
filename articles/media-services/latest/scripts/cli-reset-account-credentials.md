---
title: Exempel på Azure CLI-skript – Återställa autentiseringsuppgifterna för ditt konto | Microsoft Docs
description: Använd Azure CLI-skriptet för att återställa autentiseringsuppgifterna för ditt konto och app.config-inställningarna.
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
ms.openlocfilehash: 5ec63bd8be84481780337d0b5bc2497770f22b0d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161388"
---
# <a name="cli-example-reset-the-account-credentials"></a>CLI-exempel: Återställa autentiseringsuppgifterna för kontot

Azure CLI-skriptet i den här artikeln visar hur du kan återställa autentiseringsuppgifterna för ditt konto och app.config-inställningarna.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/reset-account-credentials/Reset-Account-Credentials.sh "Reset credentials")]

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
