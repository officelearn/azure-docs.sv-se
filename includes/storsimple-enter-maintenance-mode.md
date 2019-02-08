---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889280"
---
#### <a name="to-enter-maintenance-mode"></a>Ange underhållsläge
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**.
2. Ange lösenordet. Standardlösenordet är **Password1**.
3. I Kommandotolken, skriver du:
   
     `Enter-HcsMaintenanceMode`
4. Du ser ett varningsmeddelande om att underhållsläge ska störa alla i/o-begäranden och Server anslutningen till den klassiska Azure-portalen och du uppmanas att bekräfta. Typ **Y** att ange underhållsläget.
   
    Båda styrenheterna startas om. När omstarten är klar, visas ett annat meddelande som anger att enheten är i underhållsläge.

