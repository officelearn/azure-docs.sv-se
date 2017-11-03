---
title: "Kundens godkännande steget i teamet datavetenskap processen livscykel – Azure | Microsoft Docs"
description: "Mål, uppgifter och leveranser för kundens godkännande steg i dina datavetenskap projekt."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 953f090f775da5e48b2f4ed36550a5624ae4596b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="customer-acceptance"></a>Kundgodkännande

Det här avsnittet beskriver mål, uppgifter, och leveranser som är associerade med den **kundens godkännande steget** processens Team datavetenskap. Denna process tillhandahåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs viktiga steg som projekt vanligtvis köra, ofta upprepade gånger:

* **Så här fungerar för företag**
* **Datainsamling och förstå**
* **Modeling**
* **Distribution**
* **Kundens godkännande**

Här är en bild av den **Team datavetenskap Process livscykel**. 

![TDSP Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Målet
* **Slutför projektet leveranser**: Kontrollera att pipeline, modellen och deras distribution i en produktionsmiljö är uppfyller kundens mål.

## <a name="how-to-do-it"></a>Hur du gör det.
Det finns två huvudsakliga uppgifter som beskrivs i det här steget:

* **Systemvalidering**: Bekräfta distribuerade modell och pipeline uppfyller kundens behov.
* **Projektet hand av**: till den enhet som ska köra den i produktion.

Kunden bör verifiera att systemet uppfyller sina affärsbehov och svaren frågor med acceptabel Precision distribuera systemet till produktion för används av sina klientprogram. All dokumentation är slutförd och granskas. En hand av projektet till entitet som ansvarar för åtgärder har slutförts. Den här entiteten kan exempelvis vara en IT- eller kundens datavetenskap team eller en agent på kundens som ansvarar för att köra systemet i produktion. 

## <a name="artifacts"></a>Artefakter
Den huvudsakliga artefakt som skapas i det här sista steget är det **avsluta rapport av projekt för kunden**. Det här är den tekniska rapport som innehåller alla uppgifter i projektet som användbart att lära dig och använda systemet. En [avsluta rapporten](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) mall tillhandahålls av TDSP som kan användas som de är eller anpassas för specifika klienten behöver. 


## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för Team av vetenskapliga data:

* [1. Så här fungerar för företag](lifecycle-business-understanding.md)
* [2. Datainsamling och förstå](lifecycle-data.md)
* [3. Modeling](lifecycle-modeling.md)
* [4. Distribution](lifecycle-deployment.md)
* [5. Kundens godkännande](lifecycle-acceptance.md)

Fullständig genomgång för slutpunkt-till-slutpunkt som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) avsnittet. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel körs steg i Team av vetenskapliga data som använder Azure Machine Learning Studio finns i [med Azure ML](http://aka.ms/datascienceprocess) utbildningsvägar.