---
title: Threat Modeling Tool släpper - Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Dokumentera viktig information om threat modeling tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 502c1e8a422eb9e74586e5a6820d5b12ec4ae2a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610614"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool uppdateringsutgåvan 7.1.60408.1 - 4/9/2019

Version 7.1.60408.1 av Microsoft Threat Modeling Tool (TMT) gavs ut 9 2019 i April och innehåller följande ändringar:

- Ny stenciler för Azure Key Vault och Azure Traffic Manager
- Versionsnumret för TMT nu visas på startskärmen
- Supportlänkar har uppdaterats
- Felkorrigeringar

## <a name="feature-changes"></a>Funktionen ändringar

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Ny stenciler för Azure Key Vault och Azure Traffic Manager

![Azure Key Vault Stencil](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Ny stenciler och hot för Azure Key Vault och Azure Traffic Manager har lagts till i Azure stencil uppsättningen. När du öppnar modeller baserat på Azure stencil uppsättningen, uppmanas användarna att uppdatera den mall som är associerad med modellen. Uppdatera en modell som baseras på Azure stencil uppsättningen kan också manuellt initieras med hjälp av kommandot ”Använd mall” i ”Arkiv-menyn och återställa den senaste Azure Cloud Services.tb7-filen.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Versionsnumret för TMT nu visas på startskärmen

Klientversionen av Threat Modeling Tool nu visas på startskärmen för programmet för för enkel åtkomst.

![Azure Key Vault Stencil](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Supportlänkar har uppdaterats

Alla supportlänkar i verktyget har uppdaterats för att dirigera användarna till [ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com) i stället för en MSDN-forum.

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version som krävs
  - [.NET 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav
  - Internetanslutning krävs för att ta emot uppdateringar av verktyget samt mallar.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentation för Threat Modeling Tool finns på [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool), och innehåller information [om hur du använder verktyget](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Nästa steg

Ladda ned den senaste versionen av den [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).