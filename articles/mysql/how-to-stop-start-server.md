---
title: Stoppa/starta – Azure Portal-Azure Database for MySQL Server
description: I den här artikeln beskrivs hur du stoppar/startar åtgärder i Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 95be6aa576d9d059ce419443f8c7e32af5ff397a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826198"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Stoppa/starta en Azure Database for MySQL

> [!IMPORTANT]
> Stoppa/starta-funktionen för Azure Database for MySQL är för närvarande en offentlig för hands version.

Den här artikeln innehåller steg-för-steg-anvisningar för att stoppa och starta den enskilda servern.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

-   Du måste ha en Azure Database for MySQL enskild server.

> [!NOTE]
> Se begränsningen för att använda [stoppa/starta](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Stoppa/starta Azure Database for MySQL med Azure Portal

### <a name="stop-a-running-server"></a>Stoppa en server som körs

1.  I [Azure Portal](https://portal.azure.com/)väljer du den MySQL-server som du vill stoppa.

2.  På sidan **Översikt** klickar du på knappen **stopp** i verktygsfältet.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL stoppa Server":::

    > [!NOTE]
    > När servern har stoppats är de andra hanterings åtgärderna inte tillgängliga för den enskilda servern.

### <a name="start-a-stopped-server"></a>Starta en stoppad Server

1.  I [Azure Portal](https://portal.azure.com/)väljer du den enda server som du vill starta.

2.  På sidan **Översikt** klickar du på knappen **Start** i verktygsfältet.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL stoppa Server":::

    > [!NOTE]
    > När servern har startats är alla hanterings åtgärder nu tillgängliga för den enskilda servern.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Stoppa/starta Azure Database for MySQL med CLI

### <a name="stop-a-running-server"></a>Stoppa en server som körs

1.  I [Azure Portal](https://portal.azure.com/)väljer du den MySQL-server som du vill stoppa.

2.  På sidan **Översikt** klickar du på knappen **stopp** i verktygsfältet.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > När servern har stoppats är de andra hanterings åtgärderna inte tillgängliga för den enskilda servern.

### <a name="start-a-stopped-server"></a>Starta en stoppad Server

1.  I [Azure Portal](https://portal.azure.com/)väljer du den enda server som du vill starta.

2.  På sidan **Översikt** klickar du på knappen **Start** i verktygsfältet.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > När servern har startats är alla hanterings åtgärder nu tillgängliga för den enskilda servern.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).
