---
title: Skapa och hantera Azure Database för PostgreSQL brandväggsregler med hjälp av Azure CLI
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for PostgreSQL brandväggsregler med hjälp av Azure CLI-kommandoraden.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 05/4/2018
ms.openlocfilehash: f5133b5da055710208390bfe7fd5d6d7d85696df
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965355"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Skapa och hantera Azure Database för PostgreSQL brandväggsregler med hjälp av Azure CLI
Brandväggsregler på servernivå kan administratörer hantera åtkomst till en Azure Database for PostgreSQL-Server från en specifik IP-adress eller IP-adressintervall. Med praktiska Azure CLI-kommandon kan du skapa, uppdatera, ta bort, lista, och visa brandväggsregler för att hantera servern. En översikt över Azure Database för PostgreSQL brandväggsregler, se [Azure Database for PostgreSQL-Server brandväggsregler](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- Installera [Azure CLI](/cli/azure/install-azure-cli) -kommandoradsverktyget eller Använd Azure Cloud Shell i webbläsaren.
- En [Azure Database for PostgreSQL-server och databas](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurera brandväggsregler för Azure Database för PostgreSQL
Den [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) kommandon används för att konfigurera brandväggens regler.

## <a name="list-firewall-rules"></a>Lista brandväggsregler 
Om du vill visa befintliga brandväggsregler för servern, kör den [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_list) kommando.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Utdata visar brandväggsregler, om sådant finns, som standard i JSON-format. Du kan använda växeln `--output table` för ett mer lättläst tabellformat som utdata.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Skapa brandväggsregel
Du skapar en ny brandväggsregel på servern måste köra den [az postgres server firewall-rule skapa](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) kommando. 

Genom att ange 0.0.0.0 som den `--start-ip-address` och 255.255.255.255 som den `--end-ip-address` intervallet, i följande exempel gör att alla IP-adresser för åtkomst till servern **mydemoserver.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Om du vill tillåta åtkomst till en enda IP-adress måste ange samma adress i den `--start-ip-address` och `--end-ip-address`, som i det här exemplet.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Ange IP-adressen 0.0.0.0 som den första IP- och slut-IP, som i följande exempel för att tillåta program från Azure-IP-adresser för att ansluta till din Azure Database for PostgreSQL-server.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

Vid en lyckad distribution visas information om brandväggsregeln som du har skapat, som standard i JSON-format i kommandoutdata. Om det uppstår ett fel, visar utdata ett felmeddelande i stället.

## <a name="update-firewall-rule"></a>Uppdatera brandväggsregel 
Uppdatera en befintlig brandväggsregel på servern med [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_update) kommando. Ange namnet på befintlig brandväggsregeln som indata och start-IP- och IP-attribut som ska uppdateras.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
Vid en lyckad distribution visas information om brandväggsregeln som du har uppdaterat som standard i JSON-format i kommandoutdata. Om det uppstår ett fel, visar utdata ett felmeddelande i stället.
> [!NOTE]
> Om brandväggsregeln inte finns skapas den med kommandot update.

## <a name="show-firewall-rule-details"></a>Visa brandväggen Regelinformation
Du kan också visa information om en befintlig brandväggsregel på servernivå genom att köra [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_show) kommando.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Vid en lyckad distribution visas information om brandväggsregeln som du har angett som standard i JSON-format i kommandoutdata. Om det uppstår ett fel, visar utdata ett felmeddelande i stället.

## <a name="delete-firewall-rule"></a>Ta bort brandväggsregel
Om du vill återkalla åtkomst för ett IP-adressintervall till servern att ta bort en befintlig brandväggsregel genom att köra den [az postgres server firewall-rule ta bort](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_delete) kommando. Ange namnet på befintlig brandväggsregeln.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Vid en lyckad distribution finns det inga utdata. Vid fel returneras felmeddelandetext.

## <a name="next-steps"></a>Nästa steg
- På samma sätt du kan använda en webbläsare på [skapa och hantera Azure Database för PostgreSQL brandväggsregler med hjälp av Azure-portalen](howto-manage-firewall-using-portal.md).
- Läser mer om [Azure Database for PostgreSQL-Server brandväggsregler](concepts-firewall-rules.md).
- Hjälp med att ansluta till en Azure Database for PostgreSQL-server finns i [anslutningsbibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).
