---
ms.topic: include
ms.date: 09/10/2020
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.technology: azure-cli
ms.service: azure-cli
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 923eee619fdff7b128d9429f38cf713803cc6a13
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347402"
---
### <a name="prepare-your-environment-for-the-azure-cli"></a>Förbereda din miljö för Azure CLI

- Använd [Azure Cloud Shell](/azure/cloud-shell/quickstart) med bash-miljön.

   [![Inbäddad start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)   
- Om du vill kan du i stället [installera](/cli/azure/install-azure-cli) Azure CLI för att köra CLI-referenskommandon.
   - Om du använder en lokal installation loggar du in med Azure CLI med hjälp av kommandot [az login](/cli/azure/reference-index#az-login).  Slutför autentiseringsprocessen genom att följa stegen som visas i terminalen.  Fler inloggningsalternativ finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).
  - När du uppmanas till det installerar du Azure CLI-tillägg vid första användning.  Mer information om tillägg finns i [Använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Kör [az version](/cli/azure/reference-index?#az_version) om du vill hitta versionen och de beroende bibliotek som är installerade. Om du vill uppgradera till den senaste versionen kör du [az upgrade](/cli/azure/reference-index?#az_upgrade).

