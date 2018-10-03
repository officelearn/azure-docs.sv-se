---
title: MySQL som är värd för servrar i Azure Stack | Microsoft Docs
description: Hur du lägger till MySQL-instanser för etablering via MySQL-Resursprovider för nätverkskort
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 72824e5afb9f8d77c2f7d3bd01e6ff2035e95a95
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237272"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Lägg till värdservrar för MySQL-resursprovider

Du kan ha en MySQL-instans på en virtuell dator (VM) i [Azure Stack](azure-stack-poc.md), eller på en virtuell dator utanför Azure Stack-miljön, så länge MySQL-resursprovider kan ansluta till instansen.

> [!NOTE]
> MySQL-databaser ska skapas på MySQL resource provider-servern. MySQL-resursprovider ska skapas i standard-providerprenumeration medan MySQL värdservrar ska skapas i en fakturerbar, användarprenumeration. Resource provider-servern bör inte användas till att värden användardatabaser.

MySQL-version 5.6, 5.7 och 8.0 kan användas för värdbaserade servrar. MySQL RP stöder inte caching_sha2_password autentisering; som kommer att läggas till i nästa version. MySQL 8.0 servrar måste konfigureras för att använda mysql_native_password. MariaDB stöds också.

## <a name="connect-to-a-mysql-hosting-server"></a>Ansluta till en värd MySQL-server

Kontrollera att du har autentiseringsuppgifter för ett konto med systemadministratörsprivilegier. Följ dessa steg för att lägga till en värdservern:

1. Logga in på portalen för Azure Stack-operator som en tjänst.
2. Välj **Alla tjänster**.
3. Under den **ADMINISTRATIONSRESURSER** kategori väljer **MySQL-servrar som är värd för** > **+ Lägg till**. Då öppnas det **lägga till en värd för MySQL-Server** dialogrutan som visas i följande skärmbild.

   ![Konfigurera en värdservern](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Ange anslutningsinformation för din MySQL-Server-instans.

   * För **MySQL som är värd för servernamnet**, ange det fullständigt kvalificerade domännamnet (FQDN) eller en giltig IPv4-adress. Använd inte det korta namnet för virtuell dator.
   * En standardinstans för MySQL är inte anges, så du måste ange den **storlek av som är värd för Server i GB**. Ange en storlek som ligger nära kapaciteten för databasservern.
   * Behålla standardinställningen för **prenumeration**.
   * För **resursgrupp**, skapa en ny eller använda en befintlig grupp.

   > [!NOTE]
   > Om MySQL-instans kan användas av klienten och Azure Resource Manager-administratör, kan du placera det kontrolleras av resursprovidern. Men, MySQL-instans **måste** allokeras exklusivt till resursprovidern.

5. Välj **SKU: er** att öppna den **skapa SKU** dialogrutan.

   ![Skapa en MySQL-SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   SKU: N **namn** bör återspegla egenskaperna för SKU: N så att användare kan distribuera sina databaser till lämplig SKU.

6. Välj **OK** skapa SKU: N.
> [!NOTE]
> SKU: er kan ta upp till en timme att bli synliga i portalen. Du kan inte skapa en databas tills SKU: N har distribuerats och körs.

7. Under **lägga till en värd för MySQL-Server**väljer **skapa**.

När du lägger till servrar, kan du tilldela dem till en ny eller befintlig SKU skilja Tjänsterbjudanden. Du kan till exempel ha en enterprise MySQL-instans som ger ökad databasen och automatisk säkerhetskopiering. Du kan reservera den här servern för höga prestanda för olika avdelningar i organisationen.

## <a name="security-considerations-for-mysql"></a>Säkerhetsöverväganden för MySQL

Följande information gäller för RP och MySQL som är värd för servrar:

* Kontrollera att alla värdservrar är konfigurerade för kommunikation med hjälp av TLS 1.2. Se [konfigurera MySQL om du vill använda krypterade anslutningar](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Använda [Transparent datakryptering](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* MySQL RP stöder inte caching_sha2_password autentisering.

## <a name="increase-backend-database-capacity"></a>Öka kapaciteten för backend-databas

Du kan öka kapaciteten för backend-databas genom att distribuera flera MySQL-servrar i Azure Stack-portalen. Lägg till dessa servrar till en ny eller befintlig SKU. Om du lägger till en server till en befintlig SKU, se till att de server-egenskaperna är samma som de andra servrarna i SKU.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Gör MySQL database-servrar som är tillgängliga för dina användare

Skapa planer och erbjudanden för att göra MySQL database-servrar som är tillgängliga för användare. Lägga till tjänsten Microsoft.MySqlAdapter i planen och skapa en ny kvot. MySQL tillåter inte begränsar storleken på databaserna.

## <a name="next-steps"></a>Nästa steg

[Skapa en MySQL-databas](azure-stack-mysql-resource-provider-databases.md)