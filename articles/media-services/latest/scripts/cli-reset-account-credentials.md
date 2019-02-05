---
title: Exempel på Azure CLI-skript – Återställa autentiseringsuppgifterna för ditt konto | Microsoft Docs
description: Använd Azure CLI-skriptet för att återställa autentiseringsuppgifterna för ditt konto och app.config-inställningarna.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 7b9976d7f9d0644c11a5decf607e95ac05508e06
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100061"
---
# <a name="cli-example-reset-the-account-credentials"></a>CLI-exempel: Återställa autentiseringsuppgifterna för kontot

Azure CLI-skriptet i den här artikeln visar hur du kan återställa autentiseringsuppgifterna för ditt konto och app.config-inställningarna.

## <a name="prerequisites"></a>Nödvändiga komponenter 

[Skapa ett Media Services-konto](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/reset-account-credentials/Reset-Account-Credentials.sh "Reset credentials")]

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
