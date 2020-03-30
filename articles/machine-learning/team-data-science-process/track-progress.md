---
title: Spåra framsteg för teamdatavetenskapliga processprojekt
description: Hur datavetenskap gruppchefer, gruppledare och projektleads kan spåra utvecklingen av ett datavetenskapligt projekt.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864204"
---
# <a name="track-the-progress-of-data-science-projects"></a>Följa utvecklingen av datavetenskapliga projekt

Datascience gruppchefer, gruppleads och projektledare projekt kan spåra utvecklingen av sina projekt.  Chefer vill veta vilket arbete som har gjorts, vem som utförde arbetet och vilket arbete som återstår.   Att hantera förväntningar är en viktig framgångsfaktor.

## <a name="azure-devops-dashboards"></a>Azure DevOps-instrumentpaneler

Om du använder Azure DevOps kan du skapa instrumentpaneler för att spåra aktiviteter och arbetsobjekt som är associerade med ett visst Agile-projekt. Mer information om instrumentpaneler finns i [Instrumentpaneler, rapporter och widgetar](/azure/devops/report/dashboards/).

Instruktioner om hur du skapar och anpassar instrumentpaneler och widgetar i Azure DevOps finns i följande snabbstarter:

- [Lägga till och hantera instrumentpaneler](/azure/devops/report/dashboards/dashboards)
- [Lägga till widgetar på en instrumentpanel](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Exempel på instrumentpanel

Här är ett enkelt exempel instrumentpanel som spårar sprint aktiviteter i en Agile data science-projekt, inklusive antalet åtaganden till associerade databaser. 

- **Nedräkningspanelen** visar antalet dagar som återstår i den aktuella sprinten. 

- De två **kodpanelerna** visar antalet åtaganden i de två projektdatabaserna under de senaste sju dagarna. 

- **Arbetsobjekt för TDSP-kundprojekt** visar resultatet av en fråga för alla arbetsobjekt och deras status. 

- Ett **ackumulerat flödesschema** (CFD) visar antalet stängda och aktiva arbetsobjekt.

- Burndown **diagrammet** visar arbete fortfarande att slutföra mot återstående tid i sprint.

- **Bränningsdiagrammet** visar slutfört arbete jämfört med den totala mängden arbete i sprinten.

![Instrumentpanel](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Nästa steg

[Genomgångar som kör Team Data Science Process](walkthroughs.md) visar genomgångar som visar alla processsteg. De länkade scenarierna illustrerar hur du hanterar molnet och lokala resurser till intelligenta program. 
