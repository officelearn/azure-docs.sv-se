---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187685"
---
> [!WARNING]
> När du aktiverar diagnostik för en befintlig roll har några tillägg som du redan har inaktiverats när paketet har distribuerats. Exempel på dessa är:
>
> * Microsoft Monitoring Agent-diagnostik
> * Microsoft Azure Security Monitoring
> * Microsoft-programvara mot skadlig kod                 
> * Microsoft Monitoring Agent
> * Microsoft Service Profiler-Agent      
> * Tillägget för Windows Azure-domän        
> * Windows Azure Diagnostics-tillägg   
> * Windows Azure Remote Desktop-tillägg
> * Windows Azure-Logginsamlare
>
> Du kan återställa dina tillägg via Azure portal eller PowerShell när du har distribuerat den uppdaterade rollen.
>
