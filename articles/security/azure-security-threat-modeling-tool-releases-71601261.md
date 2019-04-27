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
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: c96b924294286be57de90dae7e6534b5ed9306ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586239"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, uppdaterad version 7.1.60126.1 – 2019-01-29

Version 7.1.60126.1 av Microsoft Threat Modeling Tool släpptes januari 29 2019 och innehåller följande ändringar:

- Versionen som krävs av .NET har ökat till [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Versionen som krävs av Windows har ökat till [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) på grund av .NET-beroendet.
- Visa/Dölj för modellen valideringsfunktionen har lagts till verktygets Alternativ-menyn.
- Flera länkar i egenskaperna för hot har uppdaterats.
- Mindre UX ändras till verktygets-startsidan.
- Threat Modeling Tool nu ärver TLS-inställningarna för värdens operativsystem och stöds i miljöer som kräver TLS 1.2 eller högre.

## <a name="feature-changes"></a>Funktionen ändringar

### <a name="model-validation-option"></a>Modellen verifieringsalternativ

Baserat på feedback från kunder, har ett alternativ lagts till för att aktivera eller inaktivera Modellvalidering av. Tidigare, om mallen används en enda enkelriktade dataflödet mellan två objekt, du kan ha fått ett felmeddelande visas i ramen meddelanden om: ObjectsName kräver minst en 'Valfritt'. Genom att inaktivera Modellvalidering hindrar dessa varningar inte ska visas i vyn.

Alternativet att växla modellen verifiering och inaktivera finns i filen -> Inställningar -> Alternativ-menyn. Standardvärdet för den här inställningen är inaktiverad.

![Modellen verifieringsalternativ](./media/azure-security-threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version som krävs
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav
  - Internetanslutning krävs för att ta emot uppdateringar av verktyget samt mallar.

## <a name="known-issues"></a>Kända problem

### <a name="unsupported-systems"></a>System som inte stöds

#### <a name="issue"></a>Problem

Användare av Windows 10-system som är det går inte att installera .NET 4.7.1 eller senare, till exempel Windows 10 Enterprise LTSB (version 1507), kommer inte att öppna verktyget när du har uppgraderat. Dessa äldre versioner av Windows är inte längre plattformar som stöds för Threat Modeling Tool och bör inte installera den senaste uppdateringen.

#### <a name="workaround"></a>Lösning

Användare av Windows 10 Enterprise LTSB (version 1507) som har installerat den senaste uppdateringen kan återgå till den tidigare versionen av Threat Modeling Tool genom avinstallera dialogrutan i appar och funktioner.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentation för Threat Modeling Tool finns på [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool), och innehåller information [om hur du använder verktyget](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Nästa steg

Ladda ned den senaste versionen av den [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
