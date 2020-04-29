---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "66416056"
---
* För omvandlingen krävs att den virtuella datorn startas om, så du bör schemalägga migreringen av dina virtuella datorer under en redan inställd underhållsperiod. 

* Omvandlingen kan inte ångras. 

* Tänk på att alla användare med rollen [virtuell dator deltagare](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) inte kommer att kunna ändra storleken på den virtuella datorn (eftersom de kan förkonverteras). Detta beror på att virtuella datorer med hanterade diskar kräver att användaren har behörigheten Microsoft. Compute/disks/Write på OS-diskarna.

* Testa omvandlingen först. Migrera först en virtuell testdator innan du gör en migrering i produktion.

* Under omvandlingen frigör du den virtuella datorn. Den virtuella datorn får en ny ip-adress när den startas efter omvandlingen. Om det behövs kan du [tilldela datorn en statisk ip-adress](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Granska den lägsta versionen av Azure VM-agenten som krävs för att stödja konverterings processen. Information om hur du kontrollerar och uppdaterar agent versionen finns i [lägsta versions stöd för VM-agenter i Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)