---
title: "Azure CLI-skript – Skapa en Azure Database for PostgreSQL"
description: "Azure CLI-skriptexempel – Skapar en Azure Database for PostgreSQL-server och konfigurerar en brandväggsregel på servernivå."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: a04d1fd71f17c5ceef175b457c75d609b827c405
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Skapa en Azure Database for PostgreSQL-server och konfigurera en brandväggsregel med hjälp av Azure CLI
Det här CLI-skriptexemplet skapar en Azure Database for PostgreSQL-server och konfigurerar en brandväggsregel på servernivå. När skriptet har körts utan problem kan PostgreSQL-servern nås från alla Azure-tjänster och den konfigurerade IP-adressen.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att köra CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kontrollera versionen genom att köra `az --version`. [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli) innehåller information om hur du installerar eller uppgraderar din version av Azure CLI.

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=18-19 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Anteckningar** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [az postgres server firewall create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) | Skapar en brandväggsregel som ger åtkomst till servern och dess databaser från det angivna IP-adressintervallet. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure/overview)
- Prova ytterligare skript: [Azure CLI-exempel för Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
