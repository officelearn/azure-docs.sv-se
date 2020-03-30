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
Om några servrar i klustret kör Windows Server 2008 R2 eller Windows Server 2012 måste du sedan kontrollera att snabbkorrigeringen [KB2854082](https://support.microsoft.com/kb/2854082) är installerad på var och en av de lokala servrarna eller virtuella Azure-datorer som ingår i klustret. Alla servrar eller virtuella datorer som finns i klustret, men inte i tillgänglighetsgruppen, bör också ha den här snabbkorrigeringen installerad.

I fjärrskrivbordssessionen för var och en av klusternoderna hämtar [du KB2854082](https://support.microsoft.com/kb/2854082) till en lokal katalog. Installera sedan snabbkorrigeringen på varje klusternod sekventiellt. Om klustertjänsten körs på klusternoden startas servern om i slutet av snabbkorrigeringsinstallationen.

> [!WARNING]
> Om du stoppar klustertjänsten eller startar om servern påverkas kvorumhälsan för klustret och tillgänglighetsgruppen, och det kan leda till att klustret går offline. Om du vill behålla klustrets höga tillgänglighet under installationen kontrollerar du att:
> 
> * Klustret är i optimal kvorum hälsa. 
> * Innan du installerar snabbkorrigeringen på en nod är alla klusternoder online.
> * Innan du installerar snabbkorrigeringen på någon annan nod i klustret kan snabbkorrigeringsinstallationen slutföras på en nod, inklusive att starta om servern helt.
> 
> 

