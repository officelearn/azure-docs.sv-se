---
title: Spårning av dataforskningsprojekt - Team Data Science Process
description: Hur data science gruppchefer team lånet och projektet leads kan följa förloppet för ett datavetenskapsprojekt.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7745a53084f4477f7b736ea9d130ffd3eed771f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252581"
---
# <a name="tracking-the-progress-of-data-science-projects"></a>Spårning av dataforskningsprojekt

Data science gruppchefer gruppledare och projekt leads behovet av att följa förloppet för sina projekt, arbetet som har gjorts på dem och av vem och kvar på att göra-listor. 

## <a name="azure-devops-dashboards"></a>Azure DevOps-instrumentpaneler
Om du använder Azure DevOps, kan du skapa instrumentpaneler för att spåra aktiviteter och arbetsobjekt som är associerade med ett visst flexibel projekt. 

Mer information om hur du skapar och anpassar instrumentpaneler och widgetar på Azure DevOps finns i följande uppsättningar med instruktioner:

- [Lägga till och hantera instrumentpaneler](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards)
- [Lägga till widgetar i en instrumentpanel](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Instrumentpanelen för exemplet

Här är ett enkelt exempel instrumentpanel som har utformats för att spåra sprint aktiviteter för en flexibel datavetenskapsprojekt, samt antal incheckningar till tillhörande databaser. Den **vänster överkant** panelen visar:

- den aktuella sprint nedräkning 
- Antal incheckningar för varje databas under de senaste 7 dagarna
- arbetsobjekt för specifika användare. 

Panelerna återstående visar den ackumulerade Flödesdiagram (CFD) och burndown burnup för ett projekt:

- **Längst ned till vänster**:  CFD mängden arbete i en viss status, som visar godkända grå utförts i blått och gjort i grönt.
- **Överst till höger**: burndown-diagram arbetet kvar för att slutföra jämfört med återstående tid).
- **Nedre högra hörnet**: burnup diagrammets det arbete som har slutförts och den totala mängden arbete.

![instrumentpanel](./media/track-progress/dashboard.png)

En beskrivning av hur du skapar dessa diagram finns i snabbstarter och självstudier på [instrumentpaneler](https://docs.microsoft.com/azure/devops/report/dashboards/).
 
## <a name="next-steps"></a>Nästa steg

Genomgångar som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De visas och som är kopplad till miniatyrbilder beskrivningarna i den [exempel genomgångar](walkthroughs.md) artikeln. De visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 
