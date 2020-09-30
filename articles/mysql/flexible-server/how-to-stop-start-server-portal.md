---
title: Stoppa/starta – Azure Portal-Azure Database for MySQL flexibel Server
description: I den här artikeln beskrivs hur du stoppar/startar åtgärder i Azure Database for MySQL via Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: e3e08ae9bbf00e1c9a44e6ba913cac1d842928b7
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567521"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Stoppa/starta en Azure Database for MySQL – flexibel Server (för hands version)

> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här artikeln innehåller steg-för-steg-anvisningar för att stoppa och starta den flexibla servern.

## <a name="prerequisites"></a>Krav

För att slutföra den här instruktions guiden behöver du:

-   Du måste ha en Azure Database for MySQL flexibel Server.

## <a name="stop-a-running-server"></a>Stoppa en server som körs

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill stoppa.

2.  På sidan **Översikt** klickar du på knappen **stopp** i verktygsfältet.
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="Stoppa flexibel Server."::: 

3.  Bekräfta att servern stoppas genom att klicka på **Ja** .

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="Stoppa flexibel Server."::: 

> [!NOTE]
> När servern har stoppats är de andra hanterings åtgärderna inte tillgängliga för den flexibla servern.

## <a name="start-a-stopped-server"></a>Starta en stoppad Server

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill starta.

2.  På sidan **Översikt** klickar du på knappen **Start** i verktygsfältet.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="Stoppa flexibel Server.":::  

> [!NOTE]
> När servern har startats är alla hanterings åtgärder nu tillgängliga för den flexibla servern.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [nätverk i Azure Database for MySQL flexibel Server](./concepts-networking.md)
- [Skapa och hantera Azure Database for MySQL flexibla Server virtuella nätverk med Azure Portal](./how-to-manage-virtual-network-portal.md).

