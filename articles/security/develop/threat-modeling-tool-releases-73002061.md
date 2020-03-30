---
title: Microsoft Threat Modeling Tool release 2020/02/11 - Azure
description: Dokumentera viktig information för verktyget för hotmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624844"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Hot Modeling Tool uppdatering release 7.3.00206.1 - 2020-02-11

Version 7.3.00206.1 av Microsoft Threat Modeling Tool (TMT) släpptes den 11 februari 2020 och innehåller följande ändringar:

- Felkorrigeringar

## <a name="notable-bug-fixes"></a>Anmärkningsvärda buggfixar

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Fel relaterade till prioritetsvärden utanför de förväntade intervallen

Vissa kunder hade rapporterat att de fick följande felmeddelande när de öppnade filer som skapats i mallarna "Threat Modeling Tool 2016" eller anpassade:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Det här problemet har lösts i den här versionen.

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version krävs
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav
  - En Internetanslutning krävs för att ta emot uppdateringar av verktyget och mallar.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentation för hotmodelleringsverktyget finns på [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)och innehåller information [om hur du använder verktyget](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Nästa steg

Hämta den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
