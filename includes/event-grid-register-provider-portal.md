---
title: ta med fil
description: ta med fil
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 108bf3f50b863428a63e745c1dac470d4caaf623
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869822"
---
## <a name="enable-event-grid-resource-provider"></a>Aktivera Event Grid-resursprovider

Om du inte har använt Event Grid förut i din Azure-prenumeration kan du behöva registrera Event Grid-resursprovidern.

På Azure Portal:

1. Välj **Prenumerationer**.
1. Välj den prenumeration som du använder för Event Grid.
1. Under **Inställningar** väljer du **Resursprovidrar**.
1. Hitta **Microsoft.EventGrid**.
1. Om du inte är registrerad väljer du **Registrera**. 

Det kan ta en stund att slutföra registreringen. Välj **Uppdatera** för att uppdatera statusen. När **Status** är **Registrerad** är du redo att fortsätta.