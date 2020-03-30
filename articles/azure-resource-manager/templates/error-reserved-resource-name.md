---
title: Fel i reserverat resursnamn
description: Beskriver hur du löser fel när du tillhandahåller ett resursnamn som innehåller ett reserverat ord.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477634"
---
# <a name="resolve-reserved-resource-name-errors"></a>Lösa fel på reserverade resursnamn

I den här artikeln beskrivs felet som uppstår när du distribuerar en resurs som innehåller ett reserverat ord i dess namn.

## <a name="symptom"></a>Symptom

När du distribuerar en resurs som är tillgänglig via en offentlig slutpunkt kan följande felmeddelande visas:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Orsak

Resurser som har en offentlig slutpunkt kan inte använda reserverade ord eller varumärken i namnet.

Följande ord är reserverade:

* ÅTKOMST
* Azure
* Bing
* Bizspark
* Biztalk
* Cortana
* Directx
* Dotnet
* Dynamics
* EXCEL
* Exchange
* Forefront
* Groove
* HOLOLENS (
* HYPERV (HYPERV)
* Kinect
* Lync
* MSDN
* O365
* Office
* OFFICE365
* ONEDRIVE (PÅ ETT SÄTT)
* Onenote
* Outlook
* Powerpoint
* Sharepoint
* Skype
* Visio
* Visualstudio

Följande ord kan inte användas som antingen ett helt ord eller en delsträng i namnet:

* Logga in
* Microsoft
* WINDOWS
* Xbox

## <a name="solution"></a>Lösning

Ange ett namn som inte använder något av de reserverade orden.