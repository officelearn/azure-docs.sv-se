---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131271"
---
Använd följande procedur för att visa och uppdatera den körningsversion som för närvarande används av en funktionsapp.

1. I [Azure-portalen](https://portal.azure.com) bläddrar du till din funktionsapp.

1. Under **Konfigurerade funktioner** väljer du **Inställningar för funktionsapp**.

    ![Välja inställningar för funktionsapp](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. På fliken **Inställningar för funktionsapp** letar du upp **Körningsversion**. Observera den specifika körningsversionen och den begärda högre versionen. I exemplet nedan är versionen inställd på `~2`.

   ![Välja inställningar för funktionsapp](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Om du vill fästa funktionsappen till version 1.x-körningen väljer du **~ 1** under **Körningsversion**. Den här växeln är inaktiverad när du har funktioner i appen.

1. När du ändrar körningsversionen går du tillbaka till fliken **Översikt** och väljer **Starta om** för att starta om appen.  Funktionsappen startar om och körs på version 1.x-körningen, och version 1.x-mallarna används när du skapar funktioner.