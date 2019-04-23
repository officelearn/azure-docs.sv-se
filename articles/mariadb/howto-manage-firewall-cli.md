---
title: Skapa och hantera Azure-databas för MariaDB brandväggsregler med hjälp av Azure CLI
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for MariaDB brandväggsregler med hjälp av Azure CLI-kommandoraden.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 562987b953f0a8a20a917e208f43557bd768c0a0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793225"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Skapa och hantera Azure-databas för MariaDB brandväggsregler med hjälp av Azure CLI
Brandväggsregler på servernivå kan användas för att hantera åtkomst till en Azure Database for MariaDB-Server från en specifik IP-adress eller ett intervall med IP-adresser. Med praktiska Azure CLI-kommandon kan du skapa, uppdatera, ta bort, lista, och visa brandväggsregler för att hantera servern. En översikt över Azure-databas för MariaDB brandväggar, se [Azure Database for MariaDB serverbrandväggsregler](./concepts-firewall-rules.md).

Virtuella nätverk (VNet)-regler kan också användas för att skydda åtkomsten till din server. Läs mer om [skapa och hantera Virtual Network service slutpunkter och regler med hjälp av Azure CLI](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
* [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* En [Azure Database for MariaDB-servern och databasen](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Brandväggen regeln kommandon:
Den **az mariadb-serverbrandväggsregel** används med Azure CLI för att skapa, ta bort, lista, visa och uppdatera brandväggsregler.

Kommandon:
- **Skapa**: Skapa en brandväggsregel för Azure MariaDB-server.
- **Ta bort**: Ta bort en brandväggsregel för Azure MariaDB-server.
- **list**: Lista över Azure MariaDB-serverbrandväggsregler.
- **Visa**: Visa information om en server i Azure MariaDB brandväggsregel.
- **update**: Uppdatera en brandväggsregel för Azure MariaDB-server.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Logga in på Azure och lista din Azure-databas för MariaDB-servrar
På ett säkert sätt ansluta Azure CLI med Azure-kontot med hjälp av den **az-inloggning** kommando.

1. Kör följande kommando på kommandoraden:
   ```azurecli
   az login
   ```
   Detta kommando visar en kod som ska användas i nästa steg.

2. Använd en webbläsare för att öppna sidan [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin), och ange koden.

3. Logga in med dina autentiseringsuppgifter för Azure i Kommandotolken.

4. När din inloggning har behörighet, skrivs en lista över prenumerationer i konsolen. Kopiera ID för den önskade prenumerationen för att ställa in den aktuella prenumerationen du använder. Använd den [az-kontogrupper](/cli/azure/account#az-account-set) kommando.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Lista över Azure-databaser för MariaDB-servrar för din prenumeration och resursgrupp grupp om du är osäker på namnen. Använd den [az mariadb-serverlista](/cli/azure/mariadb/server#az-mariadb-server-list) kommando.

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Observera det attributet i en lista som du måste ange MariaDB-server att fungera på. Om det behövs, bekräfta informationen för den här servern och med attributet för att kontrollera att den är korrekt. Använd den [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show) kommando.

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Lista brandväggsregler på Azure Database for MariaDB-Server 
Med hjälp av namnet på servern och resursgruppens namn, en lista över de befintliga brandväggsreglerna för server på servern. Använd den [az mariadb-serverlista brandväggen](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) kommando.  Lägg märke till att attributet server name har angetts i den **--server** växla och inte i den **--name** växla. 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Utdata visar regler, om sådant finns, i JSON-format (som standard). Du kan använda den **--utdatatabellen** växla till matar ut resultaten i ett mer lättläst tabellformat.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Skapa en brandväggsregel i Azure-databas för MariaDB-Server
Med hjälp av Azure MariaDB-servernamnet och resursgruppens namn, skapa en ny brandväggsregel på servern. Använd den [serverbrandväggen för az mariadb skapa](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) kommando. Ange ett namn för regeln, samt start-IP och slut-IP (om du vill ge åtkomst till ett intervall med IP-adresser) för regeln.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Ange samma IP-adress som den första IP- och slut-IP, som i följande exempel för att tillåta åtkomst för en IP-adress.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Ange IP-adressen 0.0.0.0 som den första IP- och slut-IP, som i följande exempel för att tillåta program från Azure-IP-adresser för att ansluta till din Azure Database for MariaDB-server.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

Vid en lyckad distribution skapa varje kommando utdata visar information om brandväggsregeln som du har skapat i JSON-format (som standard). Om det uppstår ett fel, visar utdata felmeddelandetext i stället.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Uppdatera en brandväggsregel på Azure Database for MariaDB-server 
Med Azure MariaDB-servernamnet och resursgruppens namn kan uppdatera en befintlig brandväggsregel på servern. Använd den [az mariadb brandväggen serveruppdateringen](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) kommando. Ange namnet på befintlig brandväggsregeln som indata, samt början IP- och IP-attribut som ska uppdateras.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Vid en lyckad distribution visas utdata från kommandot information om brandväggsregeln som du har uppdaterat i JSON-format (som standard). Om det uppstår ett fel, visar utdata felmeddelandetext i stället.

> [!NOTE]
> Om brandväggsregeln inte finns, skapas regeln genom att kommandot update.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Visa brandväggen Regelinformation i Azure-databas för MariaDB-Server
Med hjälp av Azure MariaDB-servernamnet och resursgruppens namn, visa befintliga brandväggen Regelinformation från servern. Använd den [az mariadb server firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) kommando. Ange namnet på befintlig brandväggsregeln som indata.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Vid en lyckad distribution visas utdata från kommandot information om brandväggsregeln som du har angett i JSON-format (som standard). Om det uppstår ett fel, visar utdata felmeddelandetext i stället.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Ta bort en brandväggsregel i Azure-databas för MariaDB-Server
Med hjälp av Azure MariaDB-servernamnet och resursgruppens namn, ta bort en befintlig brandväggsregel från servern. Använd den [serverbrandväggen för az mariadb ta bort](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) kommando. Ange namnet på befintlig brandväggsregeln.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Vid en lyckad distribution finns det inga utdata. Vid fel visar felmeddelandetext.

## <a name="next-steps"></a>Nästa steg
- Läser mer om [Azure Database for MariaDB serverbrandväggsregler](./concepts-firewall-rules.md).
- [Skapa och hantera Azure-databas för MariaDB brandväggsregler med hjälp av Azure-portalen](./howto-manage-firewall-portal.md).
- Ytterligare säker åtkomst till servern genom att [skapa och hantera Virtual Network service slutpunkter och regler med hjälp av Azure CLI](howto-manage-vnet-cli.md).
