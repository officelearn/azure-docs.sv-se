---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597956"
---
## <a name="attack-scenario"></a>Attack scenario

Brute force-attacker brukar rikta hanterings portar som ett sätt att få åtkomst till en virtuell dator. Om det lyckas kan en angripare ta kontroll över den virtuella datorn och upprätta en fäste i din miljö.

Ett sätt att minska exponeringen för en brute force-attack är att begränsa hur lång tid en port är öppen. Hanterings portar behöver inte vara öppna hela tiden. De behöver bara vara öppna när du är ansluten till den virtuella datorn, till exempel för att utföra hanterings-eller underhålls uppgifter. När just-in-Time är aktiverat använder Security Center [nätverks säkerhets gruppen](../articles/virtual-network/security-overview.md#security-rules) (NSG) och Azure brand Väggs regler som begränsar åtkomsten till hanterings portar så att de inte kan nås av angripare.

![Just-in-Time-scenario](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hur fungerar JIT-åtkomst?

När just-in-Time är aktiverat Security Center låser inkommande trafik till dina virtuella Azure-datorer genom att skapa en NSG-regel. Du väljer portarna på den virtuella datorn till vilken inkommande trafik ska låsas. Dessa portar styrs av just-in-Time-lösningen.

När en användare begär åtkomst till en virtuell dator kontrollerar Security Center att användaren har [rollbaserade Access Control (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) behörigheter för den virtuella datorn. Om begäran godkänns konfigurerar Security Center automatiskt nätverks säkerhets grupper (NSG: er) och Azure-brandvägg för att tillåta inkommande trafik till de valda portarna och begärda käll-IP-adresser eller intervall, för den angivna tiden. När tiden har gått ut Security Center återställer NSG: er till sina tidigare tillstånd. De anslutningar som redan är etablerade avbryts dock inte.

 > [!NOTE]
 > Om en begäran om JIT-åtkomst har godkänts för en virtuell dator bakom en Azure-brandvägg ändrar Security Center automatiskt både NSG-och brand Väggs princip reglerna. För den tid som har angetts tillåter reglerna inkommande trafik till de valda portarna och begärda käll-IP-adresser eller intervall. När tiden är över, Security Center återställer brand Väggs-och NSG-reglerna till sina tidigare tillstånd.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Behörigheter som krävs för att konfigurera och använda JIT

| Så här gör du så att en användare kan: | Behörigheter att ange|
| --- | --- |
| Konfigurera eller redigera en JIT-princip för en virtuell dator | *Tilldela följande åtgärder till rollen:*  <ul><li>I omfånget för en prenumeration eller resurs grupp som är associerad med den virtuella datorn:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> I omfånget för en prenumeration eller resurs grupp av virtuell dator: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Begär JIT-åtkomst till en virtuell dator | *Tilldela följande åtgärder till användaren:*  <ul><li>I omfånget för en prenumeration eller resurs grupp som är associerad med den virtuella datorn:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>I omfånget för en prenumeration eller resurs grupp som är associerad med den virtuella datorn:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  I omfånget för en prenumeration eller resurs grupp eller virtuell dator:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  I omfånget för en prenumeration eller resurs grupp eller virtuell dator:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|