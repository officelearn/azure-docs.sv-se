---
title: Exempel på Azure CLI-skript – Skapa en Azure Event Grid-prenumeration | Microsoft Docs
description: Azure CLI-skriptet i det här avsnittet visar hur du skapar en Event Grid-prenumeration på kontonivå för förändringar av jobbstatus.
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
ms.openlocfilehash: 5293ac55fdb13bba85996f5ed81034d4ebeeff12
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722889"
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>CLI-exempel: Skapa en Azure Event Grid-prenumeration 

Azure CLI-skriptet i den här artikeln visar hur du skapar en Event Grid-prenumeration på kontonivå för förändringar av jobbstatus.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
