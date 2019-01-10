---
title: I den här artikeln får du lära dig hur du uppdaterar Azure Stack-erbjudanden och planer | Microsoft Docs
description: Den här artikeln beskriver hur du visa och ändra befintliga Azure Stack-erbjudanden och planer.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 0a7cf008dd2d2656153d7f1d70f71b9832be5d81
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157810"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack-tilläggsplaner

Som operatör Azure Stack kan du skapa tilläggsplaner för att ändra en [Basplan](azure-stack-create-plan.md) när du vill erbjuda ytterligare tjänster eller utöka *datorn*, *storage*, eller *nätverk* kvoter utöver Basplan första erbjudande. Tilläggsplaner ändra den grundläggande planen och är valfria tillägg som användare kan välja att prenumerera på.

Det finns tillfällen när du kombinerar allt i ett enda abonnemang är optimala. Andra gånger kanske du har en plan och erbjudande sedan ytterligare tjänster med hjälp av tilläggsplaner. Du kan till exempel bestämma att erbjuda IaaS-tjänster som en del av en Basplan med alla PaaS-tjänster som behandlas som tilläggsplaner.

Ett annat skäl att använda tilläggsplaner är att hjälpa användarna Tänk också på deras användning. Om du vill göra det, kan du börja med en grundläggande plan som innehåller relativt små kvoter (beroende på vilka tjänster som krävs). Sedan, när användare kommer åt kapacitet måste de skulle bli aviserad om som redan har förbrukat allokeringen av resurser baserat på deras tilldelade plan. Därifrån kan användarna välja ett tillägg-abonnemang som innehåller ytterligare resurser.

> [!NOTE]
> När du inte vill använda en tilläggsplan för att utöka en kvot kan du också välja att [redigera den ursprungliga konfigurationen av kvoten för](azure-stack-quota-types.md#edit-a-quota).

När en användare lägger till en tilläggsplanen till en befintlig prenumeration på erbjudandet, kan de ytterligare resurserna ta upp till en timme visas.

## <a name="create-an-add-on-plan"></a>Skapa en tilläggsplan

Tilläggsplaner skapas genom att ändra ett befintligt erbjudande:

1. Logga in på Azure Stack-administratörsportalen som en administratör för ett moln.
2. Följ samma steg som används för att [skapar en ny grundläggande plan](azure-stack-create-plan.md) att skapa en ny plan som erbjuder tjänster som inte har tidigare erbjudit. I det här exemplet, Key Vault (**Microsoft.KeyVault**) tjänster kommer att inkluderas i den nya planen.
3. I administratörsportalen, klickar du på **erbjuder** och sedan välja erbjudandet uppdateras med en tilläggsplan.

   ![Skapa tilläggsplan](media/create-add-on-plan/1.PNG)

4. Rulla till slutet av egenskaperna erbjudandet och välj **tilläggsplaner**. Klicka på **Lägg till**.

    ![Skapa tilläggsplan](media/create-add-on-plan/2.PNG)

5. Välj planen som du lägger till. I det här exemplet planen kallas **Key vault plan**. När du har valt planen klickar du på **Välj** att lägga till planen i erbjudandet. Du bör få ett meddelande om att planen har lagts till i erbjudandet har.

    ![Skapa tilläggsplan](media/create-add-on-plan/3.PNG)

6. Granska listan över tilläggsplaner som ingår i erbjudandet att verifiera att den nya tilläggsplanen visas.

    ![Skapa tilläggsplan](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett erbjudande](azure-stack-create-offer.md)