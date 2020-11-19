---
title: Huvud versions uppgradering – Azure Portal-Azure Database for MySQL-enskild server
description: Den här artikeln beskriver hur du kan uppgradera huvud versionen för Azure Database for MySQL-en server med Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 4dd4729589e429cb1b028b183fdfd144617d1d1b
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920652"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Huvud versions uppgradering i Azure Database for MySQL enskild server med hjälp av Azure Portal

> [!IMPORTANT]
> Huvud versions uppgradering för Azure Database för MySQL Single Server finns i en offentlig för hands version.

Den här artikeln beskriver hur du kan uppgradera din MySQL-huvudversion på plats i Azure Database for MySQL enskild server.

Den här funktionen gör det möjligt för kunder att utföra uppgraderingar på plats av sina MySQL 5,6-servrar till MySQL 5,7 med ett klick på knappen utan någon data förflyttning eller behovet av eventuella anslutnings strängar för program.

> [!Note]
> * Huvud versions uppgradering är bara tillgänglig för uppgradering av högre version från MySQL 5,6 till MySQL 5,7<br>
> * Det finns inte stöd för att uppgradera större versioner på replik servern ännu.

## <a name="prerequisites"></a>Krav
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for MySQL enskild server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Utför större versions uppgradering från MySQL 5,6 till MySQL 5,7

Följ de här stegen för att utföra större versions uppgradering för din Azure Database of MySQL 5,6-Server

> [!IMPORTANT]
> Vi rekommenderar att du först uppgraderar till en återställd kopia av servern i stället för att uppgradera produktion direkt. Se [hur du utför en tidpunkts återställning](howto-restore-server-portal.md#point-in-time-restore).

1. I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL 5,6-Server.

2. På sidan **Översikt** klickar du på knappen **Uppgradera** i verktygsfältet.

3. I avsnittet **Uppgradera** väljer du **OK** för att uppgradera Azure Database for MySQL 5,6 server till 5,7 Server.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL-översikt – uppgradering":::

4. Ett meddelande bekräftar att uppgraderingen har slutförts.

## <a name="next-steps"></a>Nästa steg

Läs mer om [principer för Azure Database for MySQL versioner](concepts-version-policy.md).