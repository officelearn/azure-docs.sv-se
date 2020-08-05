---
title: Skapa en anslutnings sträng med PowerShell – Azure Database for MySQL
description: Den här snabb starten innehåller ett Azure PowerShell exempel för att skapa en anslutnings sträng för att ansluta till Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/3/2020
ms.openlocfilehash: 9de569e09e1b8bcf29f47077cbe15f0a6c40ddd1
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544950"
---
# <a name="azure-database-for-mysql-generate-a-connection-string-with-powershell"></a>Azure Database for MySQL: skapa en anslutnings sträng med PowerShell

Den här artikeln visar hur du skapar en anslutnings sträng för en Azure Database for MySQL-server. Du kan använda en anslutnings sträng för att ansluta till en Azure Database for MySQL från många olika program.

## <a name="requirements"></a>Krav

I den här artikeln används de resurser som har skapats i följande hand bok som utgångs punkt:

* [Snabb start: skapa en Azure Database for MySQL-server med PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Hämta anslutningssträngen

`Get-AzMySqlConnectionString`Cmdleten används för att skapa en anslutnings sträng för att ansluta program till Azure Database for MySQL. I följande exempel returneras anslutnings strängen för en PHP-klient från **mydemoserver**.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

Giltiga värden för `Client` parametern är:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en Azure Database for MySQL med hjälp av PowerShell](tutorial-design-database-using-powershell.md)
