---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "67187685"
---
> [!WARNING]
> När du aktiverar diagnostik för en befintlig roll inaktive ras alla tillägg som du redan har angett när paketet distribueras. Exempel på dessa är:
>
> * Diagnostik för Microsoft Monitoring Agent
> * Microsoft Azure säkerhets övervakning
> * Microsoft Antimalware                 
> * Microsoft Monitoring Agent
> * Microsoft Service Profiler-agent      
> * Windows Azure-domännätverk        
> * Windows Azure-diagnostik-tillägg   
> * Windows Azure fjärr skrivbords tillägg
> * Windows Azure logg insamlare
>
> Du kan återställa dina tillägg via Azure Portal eller PowerShell när du har distribuerat den uppdaterade rollen.
>
