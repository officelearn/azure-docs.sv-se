---
title: Hantera brandväggsregler - Azure CLI - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du skapar och hanterar Azure Database för MariaDB-brandväggsregler med hjälp av Azure CLI-kommandoraden.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 898b65f07140bca04bd97ff7314b01920b783914
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530639"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Skapa och hantera Azure Database för MariaDB-brandväggsregler med hjälp av Azure CLI
Brandväggsregler på servernivå kan användas för att hantera åtkomst till en Azure-databas för MariaDB Server från en specifik IP-adress eller ett intervall med IP-adresser. Med hjälp av praktiska Azure CLI-kommandon kan du skapa, uppdatera, ta bort, lista och visa brandväggsregler för att hantera servern. En översikt över Azure Database för MariaDB-brandväggar finns i [Azure Database for MariaDB-serverbrandväggsregler](./concepts-firewall-rules.md).

Virtuella nätverksregler (Virtual Network) kan också användas för att skydda åtkomsten till servern. Läs mer om [hur du skapar och hanterar slutpunkter och regler för tjänsten För virtuella nätverk med hjälp av Azure CLI](howto-manage-vnet-cli.md).

## <a name="prerequisites"></a>Krav
* [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* En [Azure-databas för MariaDB-server och databas](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Kommandon för brandväggsregel:
Kommandot **az mariadb-server firewall-rule** används från Azure CLI för att skapa, ta bort, lista, visa och uppdatera brandväggsregler.

Kommandon:
- **skapa**: Skapa en Azure MariaDB-serverbrandväggsregel.
- **ta bort**: Ta bort en Azure MariaDB-serverbrandväggsregel.
- **lista**: Lista Azure MariaDB-serverns brandväggsregler.
- **visa**: Visa information om en Azure MariaDB-serverbrandväggsregel.
- **uppdatering**: Uppdatera en Azure MariaDB-serverbrandväggsregel.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Logga in på Azure och lista din Azure-databas för MariaDB-servrar
Anslut Azure CLI på ett säkert sätt med ditt Azure-konto med kommandot **az login.**

1. Kör följande kommando på kommandoraden:
   ```azurecli
   az login
   ```
   Det här kommandot matar ut en kod som ska användas i nästa steg.

2. Använd en webbläsare för [https://aka.ms/devicelogin](https://aka.ms/devicelogin)att öppna sidan och ange sedan koden.

3. Logga in med dina Azure-autentiseringsuppgifter i prompten.

4. När din inloggning har auktoriserats skrivs en lista över prenumerationer ut i konsolen. Kopiera ID:t för den önskade prenumerationen för att ställa in den aktuella prenumerationen som ska användas. Använd kommandot [az account set.](/cli/azure/account#az-account-set)
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Lista Azure-databaser för MariaDB-servrar för din prenumeration och resursgrupp om du är osäker på namnen. Använd kommandot [az mariadb server list.](/cli/azure/mariadb/server#az-mariadb-server-list)

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Observera namnattributet i listan, som du måste ange vilken MariaDB-server som ska fungera. Om det behövs bekräftar du informationen för servern och använder namnattributet för att säkerställa att den är korrekt. Använd kommandot [az mariadb server show.](/cli/azure/mariadb/server#az-mariadb-server-show)

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Lista brandväggsregler för Azure Database för MariaDB Server 
Med hjälp av servernamnet och resursgruppens namn anger du de befintliga serverbrandväggsreglerna på servern. Använd kommandot [az mariadb-serverbrandväggslista.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list)  Observera att servernamnsattributet anges i **växeln --server** och inte i växeln **--name.** 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Utdata visar eventuella regler i JSON-format (som standard). Du kan använda tabellväxeln **--output** för att mata ut resultaten i ett mer läsbart tabellformat.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Skapa en brandväggsregel för Azure Database för MariaDB Server
Skapa en ny brandväggsregel på servern med hjälp av Servernamnet För Azure MariaDB och resursgruppens namn. Använd kommandot [az mariadb server firewall create.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) Ange ett namn för regeln, samt start-IP- och slut-IP-adressen (för att ge åtkomst till ett intervall av IP-adresser) för regeln.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Om du vill tillåta åtkomst för en enda IP-adress anger du samma IP-adress som Start IP och End IP, som i det här exemplet.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Om du vill tillåta att program från Azure IP-adresser ansluter till din Azure-databas för MariaDB-server anger du IP-adressen 0.0.0.0 som Start-IP- och slut-IP, som i det här exemplet.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

När du lyckas visar var och en kommandoutdata information om brandväggsregeln som du har skapat i JSON-format (som standard). Om det är fel visar utdata felmeddelandetext i stället.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Uppdatera en brandväggsregel på Azure Database för MariaDB-server 
Uppdatera en befintlig brandväggsregel på servern med hjälp av Servernamnet för Azure MariaDB och resursgruppens namn. Använd kommandot [az mariadb server firewall update.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) Ange namnet på den befintliga brandväggsregeln som indata, samt start-IP- och slut-IP-attribut som ska uppdateras.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
När kommandot har gått ut visas information om brandväggsregeln som du har uppdaterat i JSON-format (som standard). Om det är fel visar utdata felmeddelandetext i stället.

> [!NOTE]
> Om brandväggsregeln inte finns skapas regeln av uppdateringskommandot.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Visa information om brandväggsregel på Azure Database för MariaDB Server
Visa den befintliga brandväggsregelinformationen från servern med servernamnet Azure MariaDB och resursgruppens namn. Använd kommandot [az mariadb server firewall show.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) Ange namnet på den befintliga brandväggsregeln som indata.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
När kommandot har gått ut visas information om den brandväggsregel som du har angett i JSON-format (som standard). Om det är fel visar utdata felmeddelandetext i stället.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Ta bort en brandväggsregel på Azure Database för MariaDB Server
Ta bort en befintlig brandväggsregel med servernamnet Azure MariaDB och resursgruppens namn. Använd kommandot [az mariadb server firewall delete.](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) Ange namnet på den befintliga brandväggsregeln.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Vid framgång, det finns ingen produktion. Vid fel visas sms-texten.

## <a name="next-steps"></a>Nästa steg
- Förstå mer om [Azure Database för MariaDB Server-brandväggsregler](./concepts-firewall-rules.md).
- [Skapa och hantera Azure Database för MariaDB-brandväggsregler med Hjälp av Azure-portalen](./howto-manage-firewall-portal.md).
- Ytterligare säker åtkomst till servern genom [att skapa och hantera slutpunkter och regler för tjänsten För virtuella nätverk med hjälp av Azure CLI](howto-manage-vnet-cli.md).
