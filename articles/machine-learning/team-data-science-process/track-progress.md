---
title: Körningen av dataforskningsprojekt – Azure Machine Learning | Microsoft Docs
description: Hur datavetare kan spåra förloppet för ett datavetenskapsprojekt.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 32390b05d2ec258a68ed4f53135399675105a7e9
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302093"
---
# <a name="track-progress-of-data-science-projects"></a>Spåra förloppet för dataforskningsprojekt

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

- **Längst ned till vänster**: CFD mängden arbete i en viss status, som visar godkända grå utförts i blått och gjort i grönt.
- **Överst till höger**: burndown-diagram arbetet kvar för att slutföra jämfört med återstående tid).
- **Nedre högra hörnet**: burnup diagrammets det arbete som har slutförts och den totala mängden arbete.

![instrumentpanel](./media/track-progress/dashboard.png)

En beskrivning av hur du skapar dessa diagram finns i snabbstarter och självstudier på [instrumentpaneler](https://docs.microsoft.com/azure/devops/report/dashboards/).
 
## <a name="next-steps"></a>Nästa steg

Genomgångar som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De visas och som är kopplad till miniatyrbilder beskrivningarna i den [exempel genomgångar](walkthroughs.md) artikeln. De visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 
