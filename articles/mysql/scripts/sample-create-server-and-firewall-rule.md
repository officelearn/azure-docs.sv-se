---
title: "Azure CLI Script - skapa en Azure-databas för MySQL | Microsoft Docs"
description: "Det här exempelskriptet CLI skapar en Azure-databas för MySQL-server och konfigurerar en brandväggsregel på servernivå."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 05/31/2017
ms.openlocfilehash: 201db294ce362ef3e09cbe62f48bd51c8ea94dbb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Skapa en MySQL-server och konfigurera en brandväggsregel med hjälp av Azure CLI
Det här exempelskriptet CLI skapar en Azure-databas för MySQL-server och konfigurerar en brandväggsregel på servernivå. När skriptet har körts, är MySQL-servern tillgänglig för alla Azure-tjänster och den konfigurerade IP-adressen.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet Redigera markerade rader om du vill anpassa admin användarnamn och lösenord.
[!code-azurecli-interactive[huvudsakliga](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "skapa en Azure-databas för MySQL och brandväggsregel på servernivå.")]

## <a name="clean-up-deployment"></a>Rensa distribution
Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.
[!code-azurecli-interactive[huvudsakliga](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "ta bort resursgruppen.")]

## <a name="script-explanation"></a>Skriptet förklaring
Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| **Kommandot** | **Anteckningar** |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ mysql-server](/cli/azure/mysql/server#create) | Skapar en MySQL-server som är värd för databaserna. |
| [Skapa AZ mysql serverbrandvägg](/cli/azure/mysql/server/firewall-rule#create) | Skapar en brandväggsregel för att tillåta åtkomst till servern och databaserna under den från det angivna IP-adressintervallet. |
| [ta bort grupp AZ](/cli/azure/group#delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI dokumentationen](/cli/azure/overview).
- Försök ytterligare skript: [Azure CLI-exempel för Azure-databas för MySQL](../sample-scripts-azure-cli.md)
