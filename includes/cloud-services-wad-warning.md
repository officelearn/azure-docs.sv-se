---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279287"
---
> [!WARNING]
> När du aktiverar diagnostik för en befintlig roll inaktiveras alla tillägg som du redan har angett när paketet distribueras. Exempel på dessa är:
>
> * Diagnostik av Microsoft-övervakningsagent
> * Microsoft Azure-säkerhetsövervakning
> * Microsoft-programvara mot skadlig kod                 
> * Microsoft Monitoring Agent
> * Profileragent för Microsoft-tjänst      
> * Windows Azure-domäntillägg        
> * Tillägg för Windows Azure-diagnostik   
> * Windows Azure-tillägg för fjärrskrivbord
> * Windows Azure-logginsamlare
>
> Du kan återställa dina tillägg via Azure-portalen eller PowerShell när du har distribuerat den uppdaterade rollen.
>
