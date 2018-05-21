---
title: Azure reserverade resursen namngivningsfel | Microsoft Docs
description: Beskriver hur du löser problem när du anger ett resursnamn som innehåller ett reserverat ord.
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
ms.openlocfilehash: b91a53d17d64afb0a56f745505f10e8cabbc22cc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-reserved-resource-name-errors"></a>Lös reserverad resurs namngivningsfel

Den här artikeln beskriver fel som kan uppstå när du distribuerar en resurs som innehåller ett reserverat ord i namnet.

## <a name="symptom"></a>Symtom

När du distribuerar en resurs som är tillgänglig via en offentlig slutpunkt får följande fel:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Orsak

Resurser som har en offentlig slutpunkt kan inte använda reserverade ord eller varumärken i namnet.

Följande ord är reserverade:

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
* HYPER-V
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE 365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

Följande ord kan inte användas som ett hela ord eller en understräng i namnet:

* LOGGA IN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Lösning

Ange ett namn som inte använder ett reserverat ord.