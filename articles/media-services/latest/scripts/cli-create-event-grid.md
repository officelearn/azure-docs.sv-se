---
title: Exempel på Azure CLI-skript – Skapa en Azure Event Grid-prenumeration | Microsoft Docs
description: Azure CLI-skriptet i det här avsnittet visar hur du skapar en Event Grid-prenumeration på kontonivå för förändringar av jobbstatus.
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
ms.openlocfilehash: 2fcacd559db0ab143ffa7ed49bbd546785765154
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098913"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>CLI-exempel: Skapa en Azure Event Grid-prenumeration 

Azure CLI-skriptet i den här artikeln visar hur du skapar en Event Grid-prenumeration på kontonivå för förändringar av jobbstatus.

## <a name="prerequisites"></a>Nödvändiga komponenter 

[Skapa ett Media Services-konto](../create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
