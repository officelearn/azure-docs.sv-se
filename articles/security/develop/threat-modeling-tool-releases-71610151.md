---
title: Microsoft Threat Modeling Tool release 2019-10-16 - Azure
description: Dokumentera viktig information för verktyget för hotmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552057"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool, uppdaterad version 7.1.61015.1 – 2019-10-16

Version 7.1.61015.1 av Microsoft Threat Modeling Tool (TMT) släpptes den 16 oktober 2019 och innehåller följande ändringar:

- Förbättrad användbarhet
- Felkorrigeringar
- Nya stenciler för Azure Logic Apps och Azure Data Explorer

## <a name="notable-bug-fixes"></a>Anmärkningsvärda buggfixar

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Förbättrad bakåtkompatibilitet med filer som skapats i "Threat Modeling Tool 2016"

Flera buggar relaterade till öppnandet eller visningen av hotmodellfiler som skapats i "Threat Modeling Tool 2016" har åtgärdats.

## <a name="feature-enhancements"></a>Funktionsförbättringar

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Nya stenciler för Azure Logic Apps och Azure Data Explorer

Nya stenciler för Azure Logic Apps och Azure Data Explorer har lagts till i Azure Stencil tillsammans med tillhörande hot och mildrande åtgärder.

![Azure Logic Apps och Azure Data Explorer Stenciler](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Kända problem

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Fel relaterade till prioritetsvärden utanför de förväntade intervallen

Vissa kunder har rapporterat att de fått följande felmeddelande när de öppnar filer som skapats i mallarna "Threat Modeling Tool 2016" eller anpassade:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Denna fråga är under utredning

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
