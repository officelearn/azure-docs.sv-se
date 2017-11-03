---
title: "Utforma din första Azure-databas för MySQL - databas i Azure CLI | Microsoft Docs"
description: "Den här självstudiekursen beskrivs hur du skapar och hanterar Azure-databas för MySQL-server och databas med hjälp av Azure CLI 2.0 från kommandoraden."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
ms.service: mysql
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 06/13/2017
ms.custom: mvc
ms.openlocfilehash: 0e6a92eeb9711b086359ab2cd1aea87a57f1fc36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Utforma din första Azure-databas för MySQL-databas

Azure MySQL-databas är en relationsdatabastjänst i molnet Microsoft utifrån MySQL Community Edition databasmotorn. I kursen får du använder Azure CLI (command-line-interface) och andra verktyg att lära dig hur du:

> [!div class="checklist"]
> * Skapa en Azure-databas för MySQL
> * Konfigurera server-brandväggen
> * Använd [mysql kommandoradsverktyget](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) att skapa en databas
> * Läs in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

Du kan använda Azure Cloud Shell i webbläsaren eller [installera Azure CLI 2.0]( /cli/azure/install-azure-cli) på din dator för att köra kodblock i den här självstudiekursen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Om du har flera prenumerationer väljer du en lämplig prenumerationen där resursen ligger eller faktureras. Välj en specifik prenumerations-ID under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) med [az gruppen skapa](https://docs.microsoft.com/cli/azure/group#az_group_create) kommando. En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp med namnet `mycliresource` på platsen `westus`.

```azurecli-interactive
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server
Skapa en Azure-databas för MySQL-server med az mysql-servern skapa kommando. En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

I följande exempel skapas en Azure Database för MySQL-server i `westus` i resursgruppen `mycliresource` med namnet `mycliserver`. Servern har en administratörsinloggning med namnet `myadmin` och lösenordet `Password01!`. Servern skapas med prestandanivån **Basic** och **50** beräkningsenheter som delas mellan alla databaser på servern. Du kan skala beräkning och lagring uppåt eller nedåt beroende på behoven i dina appar.

```azurecli-interactive
az mysql server create --resource-group mycliresource --name mycliserver
--location westus --user myadmin --password Password01!
--performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>Konfigurera brandväggsregeln
Skapa en Azure-databas för MySQL servernivå brandväggsregel med az mysql server-brandväggsregeln skapa kommando. En brandväggsregel på servernivå som tillåter ett externt program **mysql** kommandoradsverktyget eller MySQL-arbetsstationen för att ansluta till servern via brandväggen MySQL på Azure-tjänsten. 

I följande exempel skapas en brandväggsregel för en fördefinierad adressintervallet. Det här exemplet visar hela möjliga intervallet av IP-adresser.

```azurecli-interactive
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.
```azurecli-interactive
az mysql server show --resource-group mycliresource --name mycliserver
```

Resultatet är i JSON-format. Anteckna **fullyQualifiedDomainName** och **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-mysql"></a>Ansluta till servern med mysql
Använd [mysql kommandoradsverktyget](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) att upprätta en anslutning till din Azure-databas för MySQL-servern. I det här exemplet är kommandot:
```cmd
mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
```

## <a name="create-a-blank-database"></a>Skapa en tom databas
När du är ansluten till servern kan du skapa en tom databas.
```sql
mysql> CREATE DATABASE mysampledb;
```

Kör följande kommando för att växla anslutningen till den nya databasen i Kommandotolken:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen
Nu när du vet hur du ansluter till Azure-databasen för MySQL-databas, gå vi igenom hur du utför några grundläggande uppgifter.

Vi kan först skapa en tabell och läsa in den med vissa data. Nu ska vi skapa en tabell som innehåller information om maskinvaruinventering.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Läser in data i tabeller
Nu när vi har en tabell kan vi infoga vissa data i den. Kör följande fråga för att infoga vissa rader med data i öppna en kommandotolk-fönster.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du två rader med exempeldata i tabellen som du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Fråga efter och uppdatera data i tabeller
Kör följande fråga för att hämta information från databastabellen.
```sql
SELECT * FROM inventory;
```

Du kan också uppdatera data i tabeller.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Raden uppdateras i enlighet med detta när du hämtar data.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt
Anta att du av misstag har tagit bort den här tabellen. Detta är något du lätt kan återställa från. Azure MySQL-databas kan du gå tillbaka till valfri punkt i tiden i den senaste upp till 35 dagar och en återställningspunkt i tid till en ny server. Du kan använda den här nya servern för att återställa dina data. Följande steg återställa exempelserver till en innan tabellen har lagts till.

För återställningen måste du följande information:

- Återställningspunkt: Välj en i tidpunkt som inträffar innan servern har ändrats. Måste vara större än eller lika med värdet i källdatabasen äldsta säkerhetskopiering.
- Målservern: Ange ett nytt servernamn som du vill återställa till
- Källservern: Ange namnet på den server som du vill återställa från
- Plats: Du kan inte välja regionen, som standard är det samma som källservern

```azurecli-interactive
az mysql server restore --resource-group mycliresource --name mycliserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mycliserver
```

Att återställa servern och [återställa till point-in-time](./howto-restore-server-portal.md) innan tabellen har tagits bort. Återställa en server till en annan tidpunkt skapar en dubblett ny server som den ursprungliga servern från och med punkten tidpunkt du anger under förutsättning att det är inom kvarhållningsperioden för din [tjänstnivån](./concepts-service-tiers.md).

## <a name="next-steps"></a>Nästa steg
I den här kursen har du lärt dig att:
> [!div class="checklist"]
> * Skapa en Azure-databas för MySQL
> * Konfigurera server-brandväggen
> * Använd [mysql kommandoradsverktyget](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) att skapa en databas
> * Läs in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

> [!div class="nextstepaction"]
> [Azure-databas för MySQL - Azure CLI-exempel](./sample-scripts-azure-cli.md)
