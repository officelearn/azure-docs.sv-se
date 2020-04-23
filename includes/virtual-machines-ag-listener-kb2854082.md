---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187291"
---
Sedan, om några servrar i klustret kör Windows Server 2008 R2 eller Windows Server 2012, måste du kontrol lera att snabb korrigerings [KB2854082](https://support.microsoft.com/kb/2854082) är installerad på alla lokala servrar eller virtuella Azure-datorer som ingår i klustret. En server eller virtuell dator som finns i klustret, men inte i tillgänglighets gruppen, bör även ha den här snabb korrigeringen installerad.

I fjärrskrivbordssessionen för varje klusternod laddar du ned [KB2854082](https://support.microsoft.com/kb/2854082) till en lokal katalog. Installera sedan snabb korrigeringen på varje klusternod i tur och ordning. Om kluster tjänsten för närvarande körs på klusternoden startas servern om i slutet av installationen av snabb korrigeringen.

> [!WARNING]
> Om du stoppar kluster tjänsten eller startar om servern påverkas kvorumkonfigurationen för klustret och tillgänglighets gruppen, och det kan göra att klustret blir offline. För att upprätthålla hög tillgänglighet för klustret under installationen ser du till att:
> 
> * Klustret är i optimalt kvorum. 
> * Innan du installerar snabb korrigeringen på någon nod är alla klusternoder online.
> * Innan du installerar snabb korrigeringen på en annan nod i klustret kan du låta installationen av snabb korrigeringen köras till att slutföras på en nod, inklusive att starta om servern helt.
> 
> 

