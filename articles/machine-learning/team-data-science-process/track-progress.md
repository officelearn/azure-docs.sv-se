---
title: Spåra förloppet för process projekt för team data vetenskap
description: Hur data vetenskaps grupps chefer, grupp ledare och projekt ledare kan följa förloppet för ett data vetenskaps projekt.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/26/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 28bd3e558294c000ba65a1c60fe227bbae7e82dd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244066"
---
# <a name="track-the-progress-of-data-science-projects"></a>Spåra förloppet för data vetenskaps projekt

Data vetenskaps grupps chefer, grupp ledare och projekt ledare kan följa förloppet för sina projekt, till exempel vad det är som har utförts, vem som gjorde arbetet och vilket arbete som är kvar. 

## <a name="azure-devops-dashboards"></a>Azure DevOps-instrumentpaneler

Om du använder Azure DevOps kan du bygga instrument paneler för att spåra de aktiviteter och arbets objekt som är associerade med ett angivet Agile-projekt. Mer information om instrument paneler finns i [instrument paneler, rapporter och widgetar](/azure/devops/report/dashboards/).

Instruktioner för hur du skapar och anpassar instrument paneler och widgetar i Azure DevOps finns i följande snabb starter:

- [Lägga till och hantera instrument paneler](/azure/devops/report/dashboards/dashboards)
- [Lägga till widgetar i en instrument panel](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Exempel instrument panel

Här är ett enkelt exempel på en instrument panel som spårar Sprint aktiviteter i ett flexibelt data vetenskaps projekt, inklusive antalet incheckningar till tillhör ande databaser. 

- **Nedräknings** panelen visar antalet dagar som finns kvar i aktuell Sprint. 

- De två **kod panelerna** visar antalet incheckningar i de två projekt databaserna under de senaste sju dagarna. 

- **Arbets objekt för TDSP kund projekt** visar resultatet av en fråga för alla arbets objekt och deras status. 

- Ett **ackumulerat flödes diagram** (CFD) visar antalet stängda och aktiva arbets objekt.

- **Burndown-diagrammet** visar att arbete fortfarande slutförs mot den återstående tiden i sprinten.

- **Burnup-diagrammet** visar slutfört arbete jämfört med den totala mängden arbete i Sprint.

![Instrumentpanel](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Nästa steg

Genom gångar som används för att [köra team data science-processen](walkthroughs.md) visas genom gång som demonstrerar alla steg i processen för olika scenarier, med länkar och miniatyr beskrivningar. De länkade scenarierna illustrerar hur du kombinerar molnet och lokala verktyg och tjänster i arbets flöden eller pipelines för att skapa intelligenta program. 
