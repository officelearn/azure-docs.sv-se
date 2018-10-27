---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9bd2ee708f7d27cff5d07ca7f86d925ca6d2741d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165004"
---
<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>Ange underhållsläge
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**.
2. Ange lösenordet. Standardlösenordet är **Password1**.
3. I Kommandotolken, skriver du:
   
     `Enter-HcsMaintenanceMode`
4. Du ser ett varningsmeddelande om att underhållsläge ska störa alla i/o-begäranden och Server anslutningen till den klassiska Azure-portalen och du uppmanas att bekräfta. Typ **Y** att ange underhållsläget.
   
    Båda styrenheterna startas om. När omstarten är klar, visas ett annat meddelande som anger att enheten är i underhållsläge.

