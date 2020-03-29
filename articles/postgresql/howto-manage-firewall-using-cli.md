---
title: Hantera brandväggsregler - Azure CLI - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du skapar och hanterar brandväggsregler i Azure Database for PostgreSQL – Single Server med hjälp av Azure CLI-kommandoraden.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4af0fb288961689fb051bab8091c838f793cfcc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765655"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Skapa och hantera brandväggsregler i Azure Database for PostgreSQL - Single Server med Azure CLI
Brandväggsregler på servernivå kan användas för att hantera åtkomst till en Azure-databas för PostgreSQL-server från en specifik IP-adress eller ett visst intervall med IP-adresser. Med hjälp av praktiska Azure CLI-kommandon kan du skapa, uppdatera, ta bort, lista och visa brandväggsregler för att hantera servern. En översikt över Azure Database for PostgreSQL-brandväggsregler finns i [Azure Database for PostgreSQL Server-brandväggsregler](concepts-firewall-rules.md).

Virtuella nätverksregler (Virtual Network) kan också användas för att skydda åtkomsten till servern. Läs mer om [hur du skapar och hanterar slutpunkter och regler för tjänsten För virtuella nätverk med hjälp av Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- Installera Azure CLI-kommandoradsverktyget eller använd Azure Cloud Shell i webbläsaren. [Azure CLI](/cli/azure/install-azure-cli)
- En [Azure-databas för PostgreSQL-server och databas](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurera brandväggsregler för Azure Database för PostgreSQL
Kommandona [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) används för att konfigurera brandväggsregler.

## <a name="list-firewall-rules"></a>Lista brandväggsregler 
Om du vill visa de befintliga reglerna för serverbrandväggen kör du kommandot [az postgres server firewall-rule list.](/cli/azure/postgres/server/firewall-rule)
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Utdata visar eventuella brandväggsregler som standard i JSON-format. Du kan använda `--output table` växeln för ett mer läsbart tabellformat som utdata.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Skapa brandväggsregel
Om du vill skapa en ny brandväggsregel på servern kör du kommandot [az postgres server firewall-rule create.](/cli/azure/postgres/server/firewall-rule) 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Om du vill tillåta att program från Azure IP-adresser ansluter till din Azure-databas för PostgreSQL-server anger du IP-adressen 0.0.0.0 som Start-IP- och slut-IP, som i det här exemplet.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

När kommandot har gått ut visas information om brandväggsregeln som du har skapat som standard i JSON-format. Om det uppstår ett fel visas ett felmeddelande i stället för utdata.

## <a name="update-firewall-rule"></a>Uppdatera brandväggsregel 
Uppdatera en befintlig brandväggsregel på servern med kommandot [az postgres server firewall-rule update.](/cli/azure/postgres/server/firewall-rule) Ange namnet på den befintliga brandväggsregeln som indata och start-IP- och slut-IP-attribut som ska uppdateras.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
När kommandot har gått ut visas information om brandväggsregeln som du har uppdaterat, som standard i JSON-format. Om det uppstår ett fel visas ett felmeddelande i stället för utdata.
> [!NOTE]
> Om brandväggsregeln inte finns skapas den av uppdateringskommandot.

## <a name="show-firewall-rule-details"></a>Visa information om brandväggsregel
Du kan också visa information om en befintlig brandväggsregel på servernivå genom att köra kommandot [az postgres server firewall-rule show.](/cli/azure/postgres/server/firewall-rule)
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
När kommandot har gått ut visas information om den brandväggsregel som du har angett, som standard i JSON-format. Om det uppstår ett fel visas ett felmeddelande i stället för utdata.

## <a name="delete-firewall-rule"></a>Ta bort brandväggsregel
Om du vill återkalla åtkomsten för ett IP-intervall till servern tar du bort en befintlig brandväggsregel genom att köra kommandot [az postgres server firewall-rule delete.](/cli/azure/postgres/server/firewall-rule) Ange namnet på den befintliga brandväggsregeln.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Vid framgång, det finns ingen produktion. Vid fel returneras felmeddelandets text.

## <a name="next-steps"></a>Nästa steg
- På samma sätt kan du använda en webbläsare för att [skapa och hantera Azure Database for PostgreSQL-brandväggsregler med hjälp av Azure-portalen](howto-manage-firewall-using-portal.md).
- Förstå mer om [Azure Database for PostgreSQL Server-brandväggsregler](concepts-firewall-rules.md).
- Ytterligare säker åtkomst till servern genom [att skapa och hantera slutpunkter och regler för tjänsten För virtuella nätverk med hjälp av Azure CLI](howto-manage-vnet-using-cli.md).
- Mer information om hur du ansluter till en Azure-databas för PostgreSQL-server finns i [Anslutningsbibliotek för Azure Database för PostgreSQL](concepts-connection-libraries.md).
