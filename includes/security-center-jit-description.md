---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597956"
---
## <a name="attack-scenario"></a>Attack scenario

Brute force-attacker riktar sig ofta till hanteringsportar som ett sätt att få åtkomst till en virtuell dator. Om det lyckas kan en angripare ta kontroll över den virtuella datorn och upprätta ett fotfäste i din miljö.

Ett sätt att minska exponeringen för en brute force-attack är att begränsa den tid som en port är öppen. Hanteringsportar behöver inte alltid vara öppna. De behöver bara vara öppna när du är ansluten till den virtuella datorn, till exempel för att utföra hanterings- eller underhållsuppgifter. När just-in-time är aktiverat använder Security Center regler för [nätverkssäkerhetsgrupp](../articles/virtual-network/security-overview.md#security-rules) (NSG) och Azure-brandväggen, som begränsar åtkomsten till hanteringsportar så att de inte kan riktas till angripare.

![Just-in-time-scenario](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hur fungerar JIT-åtkomst?

När just-in-time är aktiverat låser Security Center inkommande trafik till dina virtuella Azure-datorer genom att skapa en NSG-regel. Du väljer de portar på den virtuella datorn som inkommande trafik ska vara låst. Dessa portar styrs av just-in-time-lösningen.

När en användare begär åtkomst till en virtuell dator kontrollerar Security Center att användaren har [RBAC-behörighet (Role-Based Access Control)](../articles/role-based-access-control/role-assignments-portal.md) för den virtuella datorn. Om begäran godkänns konfigurerar Security Center automatiskt NSG och Azure-brandväggen för att tillåta inkommande trafik till de valda portarna och begärda käll-IP-adresser eller -intervall, under den tid som angavs. När tiden har gått ut återställer Security Center NSG:erna till sina tidigare tillstånd. De anslutningar som redan är etablerade avbryts dock inte.

 > [!NOTE]
 > Om en JIT-åtkomstbegäran godkänns för en virtuell dator bakom en Azure-brandvägg ändrar Security Center automatiskt både NSG- och brandväggsprincipreglerna. För den tid som angavs tillåter reglerna inkommande trafik till de valda portarna och begärda käll-IP-adresser eller intervall. När tiden är över återställer Security Center brandväggen och NSG-reglerna till sina tidigare tillstånd.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Behörigheter som behövs för att konfigurera och använda JIT

| Så här aktiverar du en användare att: | Behörigheter att ange|
| --- | --- |
| Konfigurera eller redigera en JIT-princip för en virtuell dator | *Tilldela dessa åtgärder till rollen:*  <ul><li>På omfattningen av en prenumeration eller resursgrupp som är associerad med den virtuella datorn:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> På omfattningen av en prenumeration eller resursgrupp av vm: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Begär JIT-åtkomst till en virtuell dator | *Tilldela dessa åtgärder till användaren:*  <ul><li>På omfattningen av en prenumeration eller resursgrupp som är associerad med den virtuella datorn:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>På omfattningen av en prenumeration eller resursgrupp som är associerad med den virtuella datorn:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  På omfattningen av en prenumeration eller resursgrupp eller virtuell dator:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  På omfattningen av en prenumeration eller resursgrupp eller virtuell dator:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|