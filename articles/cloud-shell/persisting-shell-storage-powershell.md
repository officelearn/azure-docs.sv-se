---
title: Spara filer i PowerShell Azure Cloud Shell (förhandsversion) | Microsoft Docs
description: Genomgång av hur Azure Cloud Shell kvarstår filer.
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: b87c4a408393e4ae341898e8cfa23e9acbcb4fc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154406"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Så här fungerar PowerShell Azure Cloud Shell (förhandsgranskning)
PowerShell i molnet Shell (förhandsgranskning) kvarstår filer via metoden följande: 
* Montera din angivna Azure-filresursen som `clouddrive` i din `$Home` katalogen för filresursen direkt interaktion.

## <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` Azure-filresurser
Den `Get-CloudDrive` kommando hämtar Azure resursen filinformationen monterad av den `clouddrive` i molnet-gränssnittet. <br>
![Kör Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-clouddrive"></a>Demontera `clouddrive`
Du kan demontera en Azure-filresurs som är kopplad till molnet Shell när som helst. Om Azure-filresursen har tagits bort, uppmanas du att skapa och montera en ny Azure filresurs vid nästa session.

Den `Dismount-CloudDrive` kommando demonterar en Azure-filresursen från det aktuella lagringskontot. Demontera den `clouddrive` avslutar den aktuella sessionen. Användaren uppmanas att skapa och montera en ny Azure filresurs under nästa.
![Kör Dismount CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Nästa steg
[Snabbstart för PowerShell](quickstart-powershell.md) <br>
[Lär dig mer om Azure-filer](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Lär dig mer om lagring taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>