---
title: Microsoft Threat Modeling Tool release 2019-04-09
titleSuffix: Azure
description: Dokumentera viktig information för verktyget för hotmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269718"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Hot Modeling Tool uppdatering release 7.1.60408.1 - 4/9/2019

Version 7.1.60408.1 av Microsoft Threat Modeling Tool (TMT) släpptes den 9 april 2019 och innehåller följande ändringar:

- Nya stenciler för Azure Key Vault och Azure Traffic Manager
- TMT-versionsnumret visas nu på startskärmen
- Supportlänkar har uppdaterats
- Felkorrigeringar

## <a name="feature-changes"></a>Funktionsändringar

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nya stenciler för Azure Key Vault och Azure Traffic Manager

![Azure Key Vault Stencil](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Nya stenciler och hot för Azure Key Vault och Azure Traffic Manager har lagts till i Azure-stenciluppsättningen. När du öppnar modeller baserade på Azure-stenciluppsättningen uppmanas användarna att uppdatera mallen som är associerad med modellen. Uppdatering av en modell baserat på Azure-stenciluppsättningen kan också initieras manuellt med kommandot "Använd mall" på "Arkiv"-menyn och tillämpa den senaste Azure Cloud Services.tb7-filen.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>TMT-versionsnumret visas nu på startskärmen

Klientversionen av threat modeling tool visas nu på startskärmen för tillämpningen av för enkel åtkomst.

![Azure Key Vault Stencil](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Supportlänkar har uppdaterats

Alla supportlänkar i verktyget har uppdaterats för att dirigera användare till [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) snarare än ett MSDN-forum.

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version krävs
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav
  - En Internetanslutning krävs för att ta emot uppdateringar av verktyget och mallar.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentation för hotmodelleringsverktyget finns på [docs.microsoft.com](threat-modeling-tool.md)och innehåller information [om hur du använder verktyget](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Nästa steg

Hämta den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
