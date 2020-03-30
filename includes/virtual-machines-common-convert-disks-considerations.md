---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "66416056"
---
* För omvandlingen krävs att den virtuella datorn startas om, så du bör schemalägga migreringen av dina virtuella datorer under en redan inställd underhållsperiod. 

* Omvandlingen kan inte ångras. 

* Tänk på att alla användare med rollen [Deltagare i virtuella datorer](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) inte kan ändra den virtuella datorns storlek (eftersom de kan före konverteringen). Detta beror på att virtuella datorer med hanterade diskar kräver att användaren har behörigheten Microsoft.Compute/disk/write på os-diskarna.

* Testa omvandlingen först. Migrera först en virtuell testdator innan du gör en migrering i produktion.

* Under omvandlingen frigör du den virtuella datorn. Den virtuella datorn får en ny ip-adress när den startas efter omvandlingen. Om det behövs kan du [tilldela datorn en statisk ip-adress](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Granska den minsta versionen av Azure VM-agenten som krävs för att stödja konverteringsprocessen. Information om hur du kontrollerar och uppdaterar agentversionen finns i [Stöd för minsta version för VM-agenter i Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)