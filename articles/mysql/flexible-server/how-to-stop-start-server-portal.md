---
title: Stoppa/starta – Azure Portal-Azure Database for MySQL flexibel Server
description: I den här artikeln beskrivs hur du stoppar/startar åtgärder i Azure Database for MySQL via Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: b9f406035d32a9af9ba2f5b085bcaca1b51e9d92
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937284"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Stoppa/starta en Azure Database for MySQL – flexibel Server (för hands version)


> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här artikeln innehåller steg-för-steg-anvisningar för att stoppa och starta den flexibla servern.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

-   Du måste ha en Azure Database for MySQL flexibel Server.

## <a name="stop-a-running-server"></a>Stoppa en server som körs

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill stoppa.

2.  På sidan **Översikt** klickar du på knappen **stopp** i verktygsfältet.

> [!NOTE]
> När servern har stoppats är de andra hanterings åtgärderna inte tillgängliga för den flexibla servern.

## <a name="start-a-stopped-server"></a>Starta en stoppad Server

1.  I [Azure Portal](https://portal.azure.com/)väljer du den flexibla server som du vill starta.

2.  På sidan **Översikt** klickar du på knappen **stopp** i verktygsfältet.

> [!NOTE]
> När servern har startats är alla hanterings åtgärder nu tillgängliga för den flexibla servern.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [nätverk i Azure Database for MySQL flexibel Server](./concepts-networking.md)
- [Skapa och hantera Azure Database for MySQL flexibla Server virtuella nätverk med Azure Portal](./how-to-manage-virtual-network-portal.md).

