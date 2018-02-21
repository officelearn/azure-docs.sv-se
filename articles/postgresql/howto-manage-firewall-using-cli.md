---
title: "Skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure CLI | Microsoft Docs"
description: "Den här artikeln beskriver hur du skapar och hanterar Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure CLI-kommandoraden."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/12/2018
ms.openlocfilehash: 4fbb0adabac3cefa0b889279eed9dfd03fe1b1f5
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure CLI
Brandväggsregler på servernivå kan administratörer hantera åtkomst till en Azure-databas för PostgreSQL-Server från en specifik IP-adress eller intervall av IP-adresser. Med hjälp av lämplig Azure CLI-kommandona, kan du skapa, uppdatera, ta bort, lista, och visa brandväggsregler för att hantera servern. En översikt över Azure-databas för PostgreSQL brandväggsregler, se [Azure-databas för PostgreSQL serverbrandväggsreglerna](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att gå igenom den här instruktioner:
- Installera [Azure CLI 2.0](/cli/azure/install-azure-cli) -kommandoradsverktyget eller Använd Azure Cloud-gränssnittet i webbläsaren.
- En [Azure-databas för PostgreSQL-servern och databasen](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurera brandväggsregler för Azure-databas för PostgreSQL
Den [az postgres-brandväggsregel](/cli/azure/postgres/server/firewall-rule) kommandon som används för att konfigurera brandväggens regler.

## <a name="list-firewall-rules"></a>Lista brandväggsregler 
Om du vill visa en lista med befintliga brandväggsregler för servern, kör den [az postgres brandväggsregel serverlista](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_list) kommando.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Utdata visar brandväggsregler, om sådant finns, som standard i JSON-format. Du kan använda växeln `--output table` för ett mer lättläst tabellformat som utdata.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Skapa brandväggsregel
Om du vill skapa en ny brandväggsregel på servern, kör den [az postgres-brandväggsregel skapa](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) kommando. 

Genom att ange 0.0.0.0 som den `--start-ip-address` och 255.255.255.255 som den `--end-ip-address` intervallet, i följande exempel tillåts alla IP-adresser för åtkomst till servern **mydemoserver.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Om du vill tillåta åtkomst till en enda IP-adress, ange samma adress i den `--start-ip-address` och `--end-ip-address`, som i det här exemplet.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Kommandoutdata visas vid lyckades, information om brandväggsregeln som du har skapat, som standard i JSON-format. Om det finns ett fel, visar utdata ett felmeddelande i stället.

## <a name="update-firewall-rule"></a>Uppdatera brandväggsregel 
Uppdatera en befintlig brandväggsregel på servern med [az postgres server-brandväggsregel uppdateringen](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_update) kommando. Ange namnet på befintlig brandväggsregel som indata och starta IP- och IP-attribut som ska uppdateras.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
Kommandoutdata visas vid lyckades, information om brandväggsregeln som du har uppdaterat som standard i JSON-format. Om det finns ett fel, visar utdata ett felmeddelande i stället.
> [!NOTE]
> Om brandväggsregeln inte finns, hämtar den har skapats av kommandot update.

## <a name="show-firewall-rule-details"></a>Visa brandväggen regeldetaljer
Du kan också visa information om en befintlig brandväggsregel på servernivå genom att köra [az postgres server-brandväggsregel visa](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_show) kommando.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Kommandoutdata visas vid lyckades, information om brandväggsregeln som du har angett som standard i JSON-format. Om det finns ett fel, visar utdata ett felmeddelande i stället.

## <a name="delete-firewall-rule"></a>Ta bort brandväggsregel
Om du vill återkalla åtkomst för ett IP-adressintervall till servern, ta bort en befintlig brandväggsregel genom att köra den [az postgres-brandväggsregel ta bort](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_delete) kommando. Ange namnet på befintlig brandväggsregel.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Det är klart, inga utdata. Vid fel returneras felmeddelandetext.

## <a name="next-steps"></a>Nästa steg
- På liknande sätt kan du använda en webbläsare [skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure portal](howto-manage-firewall-using-portal.md).
- Mer information om [Azure-databas för PostgreSQL serverbrandväggsreglerna](concepts-firewall-rules.md).
- Hjälp med att ansluta till en Azure-databas för PostgreSQL-servern finns [anslutningsbibliotek för Azure-databas för PostgreSQL](concepts-connection-libraries.md).
