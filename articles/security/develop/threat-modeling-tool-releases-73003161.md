---
title: Microsoft Threat Modeling Tool release 2020/03/22 - Azure
description: Dokumentera viktig information för verktyget för hotmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146867"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Hot Modellering Tool uppdatering release 7.3.00316.1 - 03/22/2020

Version 7.3.00316.1 av Microsoft Threat Modeling Tool (TMT) släpptes den 22 mars 2020 och innehåller följande ändringar:

- Förbättrad användbarhet
- Felkorrigeringar
- Funktionen Ny diagramläsare

## <a name="notable-bug-fixes"></a>Anmärkningsvärda buggfixar

### <a name="exporting-the-threat-list-to-csv"></a>Exportera hotlistan till CSV

Funktionen Exportera till CSV valde inkonsekvent vilka fält från hotlistan som skulle exporteras. Nu exporteras alla fält från hotlistan till CSV-filen. 

### <a name="ux-bugs"></a>UX buggar

- Hjälpmenyerna i det primära arbetsflödet (skapa/öppna/analysera) och mallredigeraren har nu konsekventa menyalternativ.
- Sökfältet i stencilfönstret har nu en standardmarkör och lämpliga etiketter har lagts till.

## <a name="new-features"></a>Nya funktioner

### <a name="diagramreader-feature-has-been-added"></a>Funktionen DiagramLäsare har lagts till

En ny DiagramReader-funktion har lagts till i huvudmenyn medan en modell är öppen. Den här funktionen konverterar den grafiska representationen av modellen till en textbaserad berättelse. 

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds:
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) eller senare
- .NET-version krävs:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) eller senare
- Ytterligare krav:
  - En internetanslutning för att få uppdateringar av verktyget samt mallar

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentation för hotmodelleringsverktyget finns på [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)och innehåller information [om hur du använder verktyget](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Nästa steg

Hämta den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
