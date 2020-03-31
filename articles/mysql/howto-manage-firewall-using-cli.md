---
title: Hantera brandväggsregler - Azure CLI - Azure Database för MySQL
description: I den här artikeln beskrivs hur du skapar och hanterar Azure Database for MySQL-brandväggsregler med hjälp av Azure CLI-kommandoraden.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 11aa4a80deba4df14c239e69910ea38bac1b9c55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063521"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Skapa och hantera Azure Database for MySQL-brandväggsregler med hjälp av Azure CLI
Brandväggsregler på servernivå kan användas för att hantera åtkomst till en Azure-databas för MySQL-server från en specifik IP-adress eller ett intervall med IP-adresser. Med hjälp av praktiska Azure CLI-kommandon kan du skapa, uppdatera, ta bort, lista och visa brandväggsregler för att hantera servern. En översikt över Azure Database for MySQL-brandväggar finns i [Azure Database for MySQL-serverbrandväggsregler](./concepts-firewall-rules.md).

Virtuella nätverksregler (Virtual Network) kan också användas för att skydda åtkomsten till servern. Läs mer om [hur du skapar och hanterar slutpunkter och regler för tjänsten För virtuella nätverk med hjälp av Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Krav
* [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* En [Azure-databas för MySQL-server och databas](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Kommandon för brandväggsregel:
Kommandot **az mysql-server firewall-rule** används från Azure CLI för att skapa, ta bort, lista, visa och uppdatera brandväggsregler.

Kommandon:
- **skapa**: Skapa en Azure MySQL-serverbrandväggsregel.
- **ta bort**: Ta bort en Azure MySQL-serverbrandväggsregel.
- **lista**: Lista Azure MySQL-serverns brandväggsregler.
- **visa**: Visa information om en Azure MySQL-serverbrandväggsregel.
- **uppdatering:** Uppdatera en Azure MySQL-serverbrandväggsregel.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Logga in på Azure och lista din Azure-databas för MySQL-servrar
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

5. Lista Azure-databaser för MySQL-servrar för din prenumeration och resursgrupp om du är osäker på namnen. Använd kommandot [az mysql server list.](/cli/azure/mysql/server#az-mysql-server-list)

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Observera namnattributet i listan, som du måste ange mysql-servern som ska fungera. Om det behövs bekräftar du informationen för servern och använder namnattributet för att säkerställa att den är korrekt. Använd [az mysql server visa](/cli/azure/mysql/server#az-mysql-server-show) kommandot.

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Lista brandväggsregler för Azure Database för MySQL Server 
Med hjälp av servernamnet och resursgruppens namn anger du de befintliga serverbrandväggsreglerna på servern. Använd kommandot [az mysql server firewall list.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list)  Observera att servernamnsattributet anges i **växeln --server** och inte i växeln **--name.** 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Utdata visar eventuella regler i JSON-format (som standard). Du kan använda tabellväxeln **--output** för att mata ut resultaten i ett mer läsbart tabellformat.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Skapa en brandväggsregel för Azure Database för MySQL Server
Skapa en ny brandväggsregel på servern med hjälp av Azure MySQL-servernamnet och resursgruppens namn. Använd [az mysql server brandvägg skapa](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) kommandot. Ange ett namn för regeln, samt start-IP- och slut-IP-adressen (för att ge åtkomst till ett intervall av IP-adresser) för regeln.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Om du vill tillåta åtkomst för en enda IP-adress anger du samma IP-adress som Start IP och End IP, som i det här exemplet.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Om du vill tillåta att program från Azure IP-adresser ansluter till din Azure-databas för MySQL-server anger du IP-adressen 0.0.0.0 som Start-IP- och slut-IP, som i det här exemplet.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

När du lyckas visar var och en kommandoutdata information om brandväggsregeln som du har skapat i JSON-format (som standard). Om det är fel visar utdata felmeddelandetext i stället.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Uppdatera en brandväggsregel på Azure Database för MySQL-server 
Uppdatera en befintlig brandväggsregel på servern med hjälp av Azure MySQL-servernamnet och resursgruppens namn. Använd kommandot [az mysql server firewall update.](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) Ange namnet på den befintliga brandväggsregeln som indata, samt start-IP- och slut-IP-attribut som ska uppdateras.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
När kommandot har gått ut visas information om brandväggsregeln som du har uppdaterat i JSON-format (som standard). Om det är fel visar utdata felmeddelandetext i stället.

> [!NOTE]
> Om brandväggsregeln inte finns skapas regeln av uppdateringskommandot.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Visa information om brandväggsregel i Azure Database för MySQL Server
Visa den befintliga brandväggsregelinformationen från servern med servernamnet Azure MySQL och resursgruppens namn. Använd [az mysql server brandvägg visa](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) kommandot. Ange namnet på den befintliga brandväggsregeln som indata.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
När kommandot har gått ut visas information om den brandväggsregel som du har angett i JSON-format (som standard). Om det är fel visar utdata felmeddelandetext i stället.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Ta bort en brandväggsregel för Azure Database för MySQL Server
Ta bort en befintlig brandväggsregel från servern med hjälp av Azure MySQL-servernamnet och resursgruppens namn. Använd az [mysql server brandväggen ta bort](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) kommandot. Ange namnet på den befintliga brandväggsregeln.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Vid framgång, det finns ingen produktion. Vid fel visas sms-texten.

## <a name="next-steps"></a>Nästa steg
- Förstå mer om [Azure Database for MySQL Server-brandväggsregler](./concepts-firewall-rules.md).
- [Skapa och hantera Azure Database for MySQL-brandväggsregler med hjälp av Azure-portalen](./howto-manage-firewall-using-portal.md).
- Ytterligare säker åtkomst till servern genom [att skapa och hantera slutpunkter och regler för tjänsten För virtuella nätverk med hjälp av Azure CLI](howto-manage-vnet-using-cli.md).