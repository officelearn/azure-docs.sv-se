---
title: Microsoft Threat Modeling Tool version 02/11/2020 – Azure
description: Dokumentera viktig information för verktyget Threat Modeling
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624844"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool uppdaterings version 7.3.00206.1 – 02/11/2020

Version 7.3.00206.1 av Microsoft Threat Modeling Tool (TMT) släpptes den 11 2020 februari och innehåller följande ändringar:

- Felkorrigeringar

## <a name="notable-bug-fixes"></a>Viktiga fel korrigeringar

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Fel relaterade till prioritets värden utanför förväntade intervall

Vissa kunder hade rapporterat följande fel meddelande när de öppnade filer som skapades i "Threat Modeling Tool 2016" eller anpassade mallar:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Det här problemet har lösts i den här versionen.

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - [Uppdatering av Microsoft Windows 10-årsdag](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version krävs
  - [.NET-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav
  - En Internet anslutning krävs för att ta emot uppdateringar av verktyget och mallar.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentationen för Threat Modeling Tool finns på [docs.Microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)och innehåller information [om hur du använder verktyget](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Nästa steg

Ladda ned den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
