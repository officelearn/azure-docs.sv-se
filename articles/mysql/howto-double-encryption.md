---
title: Infrastruktur dubbel kryptering – Azure Portal – Azure Database for MySQL
description: Lär dig hur du konfigurerar och hanterar infrastruktur Double Encryption för din Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: d3076f2591718931bdab4dba9510d25fe07b2d02
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118768"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Infrastruktur dubbel kryptering för Azure Database for MySQL

Lär dig hur du använder den här inställningen för att konfigurera och hantera infrastruktur dubbel kryptering för din Azure Database for MySQL.

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Skapa en Azure Database for MySQL-server med Double Infrastructure Encryption – Portal

Följ dessa steg om du vill skapa en Azure Database for MySQL-server med Double Infrastructure Encryption från Azure Portal:

1. Välj **skapa en resurs** (+) i det övre vänstra hörnet i portalen.

2. Välj **databaser**  >  **Azure Database for MySQL**. Du kan också ange **MySQL** i sökrutan för att hitta tjänsten.

   ![Alternativet Azure-databas för MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Ange grundläggande information om servern. Välj **ytterligare inställningar** och aktivera kryss rutan **infrastruktur med dubbla kryptering** för att ange parametern.

    ![Azure Database for MySQL val](./media/howto-double-encryption/infrastructure-encryption-selected.png)

4. Välj **Granska + skapa** för att etablera servern.

    ![Azure Database for MySQL Sammanfattning](./media/howto-double-encryption/infrastructure-encryption-summary.png)

5. När servern har skapats kan du verifiera infrastrukturens dubbla kryptering genom att kontrol lera statusen i bladet **data krypterings** Server.

    ![Azure Database for MySQL validering](./media/howto-double-encryption/infrastructure-encryption-validation.png)

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Skapa en Azure Database for MySQL-server med dubbel kryptering med infrastruktur – CLI

Följ dessa steg om du vill skapa en Azure Database for MySQL-server med Double Infrastructure Encryption från CLI:

I det här exemplet skapas en resurs grupp med namnet `myresourcegroup` på `westus` platsen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
I följande exempel skapas en MySQL 5.7-server i USA, västra som heter `mydemoserver` i din resursgrupp `myresourcegroup` med serveradministratörsinloggningen `myadmin`. Det här är **4:e generationens server för ** **generell användning** med 2 **virtuella kärnor**. Detta kommer också att aktivera infrastruktur dubbel kryptering för servern som skapats. Ersätt `<server_admin_password>` med ditt eget värde.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Nästa steg

 Mer information om data kryptering finns i [Azure Database for MySQL data infrastruktur Double Encryption](concepts-Infrastructure-double-encryption.md).
