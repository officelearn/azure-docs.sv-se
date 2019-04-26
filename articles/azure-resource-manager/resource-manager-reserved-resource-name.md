---
title: Reserverade Azure-resurs namngivningsfel | Microsoft Docs
description: Beskriver hur du löser fel när du anger ett resursnamn som innehåller ett reserverat ord.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 922389b7c6c1bb7ad1d9b8f6ec35ccc1c5656723
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390859"
---
# <a name="resolve-reserved-resource-name-errors"></a>Lösa reserverade resursfel namn

Den här artikeln beskriver de fel som uppstår när du distribuerar en resurs som innehåller ett reserverat ord i namnet.

## <a name="symptom"></a>Symtom

När du distribuerar en resurs som är tillgänglig via en offentlig slutpunkt kan felmeddelande följande visas:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Orsak

Resurser som har en offentlig slutpunkt kan inte använda reserverade ord eller varumärken i namnet.

Följande är reserverade:

* ÅTKOMST
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

Följande kan inte användas som hela ord eller en understräng i namnet:

* LOGGA IN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Lösning

Ange ett namn som inte använder ett reserverat ord.