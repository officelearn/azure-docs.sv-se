---
title: Montera en filresurs i en Python-funktionsapp - Azure CLI
description: Skapa en serverlös Python-funktionsapp och montera en befintlig filresurs med Hjälp av Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.openlocfilehash: 3d66f84d124b36f1be335c3a2204f21690510ee8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79086462"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Montera en filresurs i en Python-funktionsapp med Azure CLI

Det här exempelskriptet för Azure Functions skapar en funktionsapp och skapar en resurs i Azure-filer. Det dem monterar resursen så att data kan nås av dina funktioner.  

>[!NOTE]
>Funktionsappen som skapats körs på Python version 3.7. Azure Functions stöder också [Python version 3.6 och 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). Exempel skrivs för Bash-skalet och måste ändras för att köras i en Kommandotolk i Windows. 

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en Azure Function-app med hjälp av [förbrukningsplanen](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Konfigurerar ett Azure Storage-konto. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Skapar en Azure-filresurs i lagringskontot. | 
| [az lagringskatalog skapa](/cli/azure/storage/directory#az-storage-directory-create) | Skapar en katalog i resursen. |
| [az webapp config storage-account add az webapp config storage-account add az webapp config storage-account](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Monterar resursen i funktionsappen. |
| [az webapp config lagring-konto lista](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Visar filresurser monterade på funktionsappen. | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
