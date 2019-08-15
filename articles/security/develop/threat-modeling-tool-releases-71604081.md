---
title: Threat Modeling Tool-versioner – Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Dokumentera viktig information för verktyget Threat Modeling
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 6992ac58ebf8976714e5a478dd7b886efab2a4b7
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934788"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool uppdaterings version 7.1.60408.1 – 4/9/2019

Version 7.1.60408.1 av Microsoft Threat Modeling Tool (TMT) lanserades den 9 2019 april och innehåller följande ändringar:

- Nya stenciler för Azure Key Vault och Azure Traffic Manager
- TMT-versions numret visas nu på Start skärmen
- Support länkar har uppdaterats
- Felkorrigeringar

## <a name="feature-changes"></a>Funktions ändringar

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nya stenciler för Azure Key Vault och Azure Traffic Manager

![Azure Key Vault stencil](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Nya stenciler och hot för Azure Key Vault och Azure-Traffic Manager har lagts till i uppsättningen Azure-stencil. När du öppnar modeller som baseras på Azures stencil uppsättning uppmanas användarna att uppdatera mallen som är associerad med modellen. Att uppdatera en modell som baseras på Azures stencil uppsättning kan också initieras manuellt med hjälp av kommandot "Använd mall" i menyn "Arkiv" och tillämpa den senaste Azure Cloud Services. TB7-filen igen.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>TMT-versions numret visas nu på Start skärmen

Klient versionen av Threat Modeling Tool visas nu på Start skärmen i programmet för enkel åtkomst.

![Azure Key Vault stencil](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Support länkar har uppdaterats

Alla support länkar i verktyget har uppdaterats för att dirigera användare till [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) i stället för ett MSDN-forum.

## <a name="system-requirements"></a>Systemkraven

- Operativsystem som stöds
  - [Uppdatering av Microsoft Windows 10-årsdag](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version krävs
  - [.NET-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav
  - En Internet anslutning krävs för att ta emot uppdateringar av verktyget och mallar.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentationen för Threat Modeling Tool finns på [docs.Microsoft.com](threat-modeling-tool.md)och innehåller information [om hur du använder verktyget](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Nästa steg

Ladda ned den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
