---
title: Körningen av datavetenskap projekt - Azure Machine Learning | Microsoft Docs
description: Hur en data-forskare kan spåra förloppet för ett projekt för vetenskapliga data.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev
ms.openlocfilehash: fe0c1b4917439221643bf481fdd21828f857e1c4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="track-progress-of-data-science-projects"></a>Spåra förloppet för datavetenskap projekt

Datavetenskap gruppchefer, team leads och projektet leads måste följa förloppet för sina grupprojekt, arbetet som har gjorts på dem och av vem och finns kvar på att göra-listor. 

## <a name="vsts-dashboards"></a>VSTS instrumentpaneler
Om du använder Visual Studio Team Services VSTS () kan du skapa instrumentpaneler för att spåra aktiviteter och arbetsobjekt som är associerad med ett givet flexibel projekt. 

Mer information om hur du skapar och anpassar instrumentpaneler och widgetar i Visual Studio Team Services finns i följande uppsättningar med instruktioner:

- [Lägga till och hantera instrumentpaneler](https://docs.microsoft.com/vsts/report/dashboards/dashboards)
- [Lägga till widgetar i en instrumentpanel](https://docs.microsoft.com/vsts/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Exempel instrumentpanelen

Här är ett enkelt exempel instrumentpanel som är utformat för att spåra sprint aktiviteter för en flexibel datavetenskap projektet, samt antalet incheckningar till associerade databaser. Den **vänster överkant** panelen visas:

- den aktuella sprint nedräkning 
- Antal incheckningar för varje databas under de senaste 7 dagarna
- arbetsobjekt för specifika användare. 

Panelerna återstående visa kumulativa Flödesdiagram (CFD), burndown och burnup för ett projekt:

- **Längst ned till vänster**: CFD mängden arbete i en viss status, visar godkända i grått utförts i blått och utförs i grönt.
- **De främsta höger**: burndown diagram arbete kvar för att slutföra jämfört med återstående tid).
- **Nedre högra hörnet**: burnup diagram det arbete som har slutförts jämfört med den totala mängden arbete.

![instrumentpanel](./media/track-progress/dashboard.png)

En beskrivning av hur du skapar dessa diagram, finns i Snabbstart och självstudier på [instrumentpaneler](https://docs.microsoft.com/vsts/report/dashboards/).
 
## <a name="next-steps"></a>Nästa steg

Genomgång som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) artikel. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 
