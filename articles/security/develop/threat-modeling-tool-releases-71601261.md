---
title: Microsoft Threat Modeling Tool release 2019-01-29-23
titleSuffix: Azure
description: Dokumentera viktig information för verktyget för hotmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 7d0be8d7243331264c10a407e3d78370ea798928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269776"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, uppdaterad version 7.1.60126.1 – 2019-01-29

Version 7.1.60126.1 av Microsoft Threat Modeling Tool släpptes den 29 januari 2019 och innehåller följande ändringar:

- Den minsta version av .NET har höjts till [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Den minsta nödvändiga versionen av Windows har utökats till [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) på grund av .NET-beroendet.
- En växlingsfunktion för modellvalsning har lagts till på verktygets Alternativ-meny.
- Flera länkar i hotegenskaperna har uppdaterats.
- Mindre UX-ändringar på verktygets startskärm.
- Hotmodelleringsverktyget ärver nu TLS-inställningarna för värdoperativsystemet och stöds i miljöer som kräver TLS 1.2 eller senare.

## <a name="feature-changes"></a>Funktionsändringar

### <a name="model-validation-option"></a>Alternativ för modellvalidering

Baserat på feedback från kunder har ett alternativ lagts till i verktyget för att aktivera eller inaktivera modellvalideringen. Tidigare, om mallen använde ett enda enkelriktat dataflöde mellan två objekt, kan du ha fått ett felmeddelande i meddelanderamen som anger: ObjectsName kräver minst en "Alla". Om du inaktiverar modellvalidering förhindrar du att dessa varningar visas i vyn.

Alternativet att växla modellvalidering på och av finns på menyn Fil->Inställningar >Alternativ. Standardvärdet för den här inställningen är Inaktiverat.

![Alternativ för modellvalidering](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version krävs
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav
  - En Internetanslutning krävs för att ta emot uppdateringar av verktyget och mallar.

## <a name="known-issues"></a>Kända problem

### <a name="unsupported-systems"></a>System som inte stöds

#### <a name="issue"></a>Problem

Användare av Windows 10-system som inte kan installera .NET 4.7.1 eller senare, till exempel Windows 10 Enterprise LTSB (version 1507), kan inte öppna verktyget efter uppgraderingen. Dessa äldre versioner av Windows stöds inte längre plattformar för hotmodelleringsverktyget och bör inte installera den senaste uppdateringen.

#### <a name="workaround"></a>Lösning

Användare av Windows 10 Enterprise LTSB (version 1507) som har installerat den senaste uppdateringen kan återgå till den tidigare versionen av threat modeling tool via avinstallationsdialogrutan i Appar & funktioner.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentation för hotmodelleringsverktyget finns på [docs.microsoft.com](threat-modeling-tool.md)och innehåller information [om hur du använder verktyget](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Nästa steg

Hämta den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
