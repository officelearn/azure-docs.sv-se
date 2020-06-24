---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5c3277394350036b8863185e83b7cfcd4975be1c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081529"
---
Du kan registrera och avregistrera från en Notification Hub via den backend-tjänst som du har skapat. 

En avisering visas när en åtgärd anges och appen är i förgrunden. Annars visas meddelanden i meddelande centret.

> [!NOTE]
> Du skulle vanligt vis utföra registrerings åtgärderna (och avregistreringen) under den lämpligaste punkten i program livs cykeln (eller som en del av din första körnings upplevelse kanske) utan att behöva registrera dig/avregistrera indata. Det här exemplet kräver dock explicit indata från användaren för att den här funktionen ska kunna utforskas och testas enklare.
