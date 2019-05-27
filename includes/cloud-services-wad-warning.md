---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125392"
---
> [!WARNING]
> När du aktiverar diagnostik för en befintlig roll har några tillägg som du redan har inaktiverats när paketet har distribuerats. Exempel på dessa är:
>
> * Microsoft Monitoring Agent-diagnostik
> * Microsoft Azure Security Monitoring
> * Microsoft Antimalware                 
> * Microsoft Monitoring Agent
> * Microsoft Service Profiler-Agent      
> * Tillägget för Windows Azure-domän        
> * Windows Azure Diagnostics-tillägg   
> * Windows Azure Remote Desktop-tillägg
> * Windows Azure-Logginsamlare
>
> Du kan återställa dina tillägg via Azure portal eller PowerShell när du har distribuerat den uppdaterade rollen.
>
