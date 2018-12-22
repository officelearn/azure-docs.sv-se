---
title: Azure CLI-skriptexempel – Skapa en SignalR Service
description: Azure CLI-skriptexempel – Skapa SignalR Service
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: 364a8b6574b06aa2403ea028fecd0676ba0342a7
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256221"
---
# <a name="create-a-signalr-service"></a>Skapa en Azure SignalR Service 

Det här exempelskriptet skapar en ny Azure SignalR Service-resurs i en ny resursgrupp med ett slumpmässigt namn.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här skriptet använder tillägget *signalr* för Azure CLI. Utför följande kommando för att installera *signalr*-tillägget för Azure CLI innan du använder det här exempelskriptet:

```azurecli-interactive
az extension add -n signalr
```

Det här skriptet skapar en ny SignalR Service-resurs och en ny resursgrupp. 

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-service-and-group/create-signalr-service-and-group.sh "Creates a new Azure SignalR Service resource and resource group")]

Anteckna det faktiska namnet som genereras för den nya resursgruppen. Du använder resursgruppens namn när du vill ta bort alla gruppresurser.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az signalr create](/cli/azure/group#az-group-create) | Skapar en Azure SignalR-tjänstresurs. |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | Listar nycklarna som kommer att användas av programmet när det skickar uppdateringar av innehåll i realtid med SignalR. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure SignalR Service CLI-skriptexempel finns i [Azure SignalR Service-dokumentationen](../signalr-cli-samples.md).
