---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187370"
---
#### <a name="to-enter-maintenance-mode"></a>Så här går du in i underhållsläge
1. I den seriella konsolmenyn väljer du alternativ 1, **Logga in med full åtkomst**.
2. Skriv lösenordet. Standardlösenordet är **Password1**.
3. Skriv i kommandotolken
   
     `Enter-HcsMaintenanceMode`
4. Du kommer att se ett varningsmeddelande som talar om att underhållsläge kommer att störa alla I/O-begäranden och bryta anslutningen till den klassiska Azure-portalen, och du kommer att uppmanas att bekräfta. Skriv **Y** för att gå in i underhållsläge.
   
    Båda styrenheterna startas om. När omstarten är klar visas ett annat meddelande som anger att enheten är i underhållsläge.

