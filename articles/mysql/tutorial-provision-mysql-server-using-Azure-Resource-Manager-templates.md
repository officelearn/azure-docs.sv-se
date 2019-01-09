---
title: 'Självstudier: Etablera en Azure Database for MySQL-server med Azure Resource Manager-mallar'
description: Den här självstudien beskriver hur du etablerar och automatiserar Azure Database for MySQL-serverdistributioner med Azure Resource Manager-mallar.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/21/2018
ms.custom: mvc
ms.openlocfilehash: 45a4a43ae95b42174f368122f89831a356410f2b
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54004085"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-templates"></a>Självstudier: Etablera en Azure Database for MySQL-server med Azure Resource Manager-mallar

[Azure Database for MySQL REST API](https://docs.microsoft.com/en-us/rest/api/mysql/) låter DevOps-tekniker automatisera och integrera etablering, konfiguration och drift för hanterade MySQL-servrar och databaser i Azure.  API:et låter dig skapa, uppräkna, hantera och ta bort MySQL-servrar och databaser på Azure Database for MySQL-tjänsten.

Azure Resource Manager-mallar utnyttjar underliggande REST API för att deklarera och programmera de Azure-resurser som krävs för distributioner i skala, vilket överensstämmer konceptet infrastruktur som en kod. Mallen parameteriserar Azure-resursens namn, SKU, nätverk, brandväggskonfiguration och inställningar, vilket låter den skapas en gång och användas flera gånger.  Azure Resource Manager-mallar kan enkelt skapas med [Azure-portalen](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) eller [Visual Studio Code](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI). De möjliggör programpaketering, standardisering och distributionsautomatisering som kan integreras i DevOps CI/CD-pipelinen.  Om du till exempel snabbt vill distribuera en Webbapp med en Azure Database for MySQL-serverdel så kan du utföra distributionen från början till slut med den här [Snabbstartsmallen](https://azure.microsoft.com/en-us/resources/templates/101-webapp-managed-mysql/) från GitHub-galleriet.

I den här självstudien så kommer du att använda Azure Resource Manager-mallar och andra verktyg för att lära dig:

> [!div class="checklist"]
> * Skapa en Azure Database for MySQL-server med VNet-tjänstslutpunkt med Azure Resource Manager-mallar
> * Använda [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) till att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Skapa en Azure Database for MySQL-server med VNet-tjänstslutpunkt med en Azure Resource Manager-mall

Om du vill hämta JSON-mallen för en Azure Database for MySQL-server, går du till Microsoft.DBforMySQL-serverns mallreferens(https://docs.microsoft.com/en-us/azure/templates/microsoft.dbformysql/servers). Nedan visas JSON-exempelmallen som kan användas för att skapa en ny server som kör Azure Database for MySQL med VNet-tjänstslutpunkt.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
I den här begäran är de värden som måste anpassas:
+   `name` – Ange namnet på din MySQL-server (utan domännamn).
+   `location` – Ange en giltig Azure-datacenterregion för din MySQL-server. Till exempel westus2.
+   `properties/version` – Ange den MySQL-serverversion som ska distribueras. Till exempel 5.6 eller 5.7.
+   `properties/administratorLogin` – Ange MySQL administratörsinloggningen till servern. Inloggningsnamnet för administratören får inte vara azure_superuser, admin, administrator, root, guest eller public.
+   `properties/administratorLoginPassword` – Ange lösenordet för den MySQL-administratörsanvändare som anges ovan.
+   `properties/sslEnforcement` – Ange aktiverat/inaktiverat för att aktivera/inaktivera sslEnforcement.
+   `storageProfile/storageMB` – Ange den högsta allokerade lagringsstorleken som krävs för servern i megabyte. Till exempel 5120.
+   `storageProfile/backupRetentionDays` – Ange den önskade kvarhållningsperioden för säkerhetskopior i dagar. Till exempel 7. 
+   `storageProfile/geoRedundantBackup` – Ange aktiverat/inaktiverat beroende på kraven för geohaveriberedskap.
+   `sku/tier` – Ange nivån Basic, GeneralPurpose eller MemoryOptimized för distributionen.
+   `sku/capacity` – Ange vCore-kapacitet. Möjliga värden är 2, 4, 8, 16, 32 eller 64.
+   `sku/family` – Ange Gen4 eller Gen5 för att välja maskinvarugeneration för serverdistributionen.
+   `sku/name` – Ange TierPrefix_family_capacity. Till exempel B_Gen4_1, GP_Gen5_16, MO_Gen5_32. Se dokumentationen [prissättningsnivåer](./concepts-pricing-tiers.md) för att se giltiga värden per region och nivå.
+   `resources/properties/virtualNetworkSubnetId` – Ange Azure-identifieraren för undernätet i det virtuella nätverket där Azure MySQL-servern ska placeras. 
+   `tags(optional)` – Ange valfria taggar är nyckelvärdepar som du använder för att kategorisera resurser för fakturering osv.

Om du vill skapa en Azure Resource Manager-mall för att automatisera Azure Database for MySQL-distributioner för din organisation så är rekommendationen att börja från exemplet [Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) i Azure-snabbstartsgalleriet i GitHub först och bygga på den. 

Om du är nybörjare på Azure Resource Manager-mallar och vill prova så kan du starta genom att följa dessa steg:
+   Klona eller hämta exemplet [Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) från Azure-snabbstartsgalleriet.  
+   Ändra azuredeploy.parameters.json om du vill uppdatera parametervärdena baserat på dina inställningar och spara filen. 
+   Använd Azure CLI för att skapa Azure MySQL-servern med hjälp av följande kommandon

Du kan använda Azure Cloud Shell i webbläsaren eller installera Azure CLI på din egen dator för att köra kodblocken i den här självstudien.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l “West US2”
az group deployment create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen
För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna **fullyQualifiedDomainName** och **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Anslut till servern med mysql
Använd [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) till att upprätta en anslutning till din Azure Database for MySQL-server. I det här exemplet är kommandot:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Skapa en tom databas
När du är ansluten till servern ska du skapa en tom databas.
```sql
mysql> CREATE DATABASE mysampledb;
```

Kör följande kommando från prompten för att växla anslutningen till databasen du skapade nyss:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen
Nu när du vet hur du ansluter till Azure Database for MySQL-databasen kan du utföra några grundläggande uppgifter.

Skapa först en tabell och läs in lite data till den. Vi skapar en tabell som innehåller lagerinformation.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Läs in data till tabellerna
Nu när du har en tabell kan du infoga lite data i den. Kör följande fråga i den öppna kommandotolken så at du löser in några datarader.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du två rader med exempeldata i tabellen du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Ställ frågor mot och uppdatera data i tabellerna
Kör följande frågor för att hämta information från databastabellen.
```sql
SELECT * FROM inventory;
```

Du kan även uppdatera data i tabellerna.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Raden uppdateras när du hämtar data.
```sql
SELECT * FROM inventory;
```

## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du lärda dig att:
> [!div class="checklist"]
> * Skapa en Azure Database for MySQL-server med VNet-tjänstslutpunkt med en Azure Resource Manager-mall
> * Använda [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) till att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data

> [Så ansluter du program till Azure Database for MySQL](./howto-connection-string.md)
