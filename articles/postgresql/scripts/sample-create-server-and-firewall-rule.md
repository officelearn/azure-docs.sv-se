---
title: "Azure CLI Script - skapa en Azure-databas för PostgreSQL | Microsoft Docs"
description: "Azure CLI skriptexempel - skapar en Azure-databas för PostgreSQL-server och konfigurerar en brandväggsregel på servernivå."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: sample
ms.date: 11/03/2017
ms.openlocfilehash: c83c5e86ea99a25ff106e0238f4d294bec100c32
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Skapa en Azure-databas för PostgreSQL-servern och konfigurera en brandväggsregel med hjälp av Azure CLI
Det här exempelskriptet CLI skapar en Azure-databas för PostgreSQL-server och konfigurerar en brandväggsregel på servernivå. När skriptet har körts utan problem, kan PostgreSQL-servern nås från alla Azure-tjänster och den konfigurerade IP-adressen.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver i den här artikeln att du använder Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet Redigera markerade rader om du vill anpassa admin användarnamn och lösenord.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Rensa distribution
Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Skriptet förklaring
Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| **Kommandot** | **Anteckningar** |
|---|---|
| [Skapa AZ grupp](/cli/azure/group#create) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Skapa AZ postgres server](/cli/azure/postgres/server#create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [Skapa AZ postgres serverbrandvägg](/cli/azure/postgres/server/firewall-rule#create) | Skapar en brandväggsregel för att tillåta åtkomst till servern och databaserna under den från det angivna IP-adressintervallet. |
| [ta bort grupp AZ](/cli/azure/group#delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure/overview)
- Försök ytterligare skript: [Azure CLI-exempel för Azure-databas för PostgreSQL](../sample-scripts-azure-cli.md)
