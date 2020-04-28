---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279287"
---
> [!WARNING]
> När du aktiverar diagnostik för en befintlig roll inaktive ras alla tillägg som du redan har angett när paketet distribueras. Exempel på dessa är:
>
> * Diagnostik för Microsoft Monitoring Agent
> * Microsoft Azure säkerhets övervakning
> * Microsoft-programvara mot skadlig kod                 
> * Microsoft Monitoring Agent
> * Microsoft Service Profiler-agent      
> * Windows Azure-domännätverk        
> * Windows Azure-diagnostik-tillägg   
> * Windows Azure fjärr skrivbords tillägg
> * Windows Azure logg insamlare
>
> Du kan återställa dina tillägg via Azure Portal eller PowerShell när du har distribuerat den uppdaterade rollen.
>
