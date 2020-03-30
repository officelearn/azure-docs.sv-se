---
title: Hantera experimentupprepningar
titleSuffix: ML Studio (classic) - Azure
description: Hantera experimentiterationer i Azure Machine Learning Studio (klassisk). Du kan granska tidigare körningar av dina experiment när som helst för att utmana, återkomma och i slutändan antingen bekräfta eller förfina tidigare antaganden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 5c8a278f09fdb3b605020e4c2fcf7aa2776906e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204315"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Hantera experimentiterationer i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Att utveckla en prediktiv analysmodell är en iterativ process - när du ändrar de olika funktionerna och parametrarna för ditt experiment, konvergerar dina resultat tills du är övertygad om att du har en tränad, effektiv modell. Nyckeln till den här processen är att spåra de olika iterationerna av dina experimentparametrar och konfigurationer.



Du kan granska tidigare körningar av dina experiment när som helst för att utmana, återkomma och i slutändan antingen bekräfta eller förfina tidigare antaganden. När du kör ett experiment behåller Machine Learning Studio (klassisk) en historik över körningen, inklusive datauppsättning, modul- och portanslutningar och parametrar. Den här historiken samlar också in resultat, körningsinformation som start- och stopptider, loggmeddelanden och körningsstatus. Du kan se tillbaka på någon av dessa körningar när som helst för att granska kronologi av ditt experiment och mellanliggande resultat. Du kan till och med använda en tidigare körning av experimentet för att starta i en ny fas av förfrågan och upptäckt på din väg till att skapa enkla, komplexa eller till och med ensemblemodelleringslösningar.

> [!NOTE]
> När du visar en tidigare körning av ett experiment är den versionen av experimentet låst och kan inte redigeras. Du kan dock spara en kopia av den genom att klicka på **SPARA SOM** och ange ett nytt namn för kopian. Machine Learning Studio (klassisk) öppnar den nya kopian, som du sedan kan redigera och köra. Den här kopian av experimentet finns i **EXPERIMENTLISTAN** TILLSAMMANS med alla dina andra experiment.
> 
> 

## <a name="viewing-the-prior-run"></a>Visa föregående körning
När du har ett experiment öppet som du har kört minst en gång kan du visa föregående körning av experimentet genom att klicka på **Föregående körning** i egenskapsfönstret.

Anta till exempel att du skapar ett experiment och kör versioner av det klockan 11:23, 11:42 och 11:55. Om du öppnar experimentets sista körning (11:55) och klickar på **Föregående körning**öppnas den version du körde klockan 11:42.

## <a name="viewing-the-run-history"></a>Visa körhistoriken
Du kan visa alla tidigare körningar i ett experiment genom att klicka på **Visa körhistorik** i ett öppet experiment.

Anta till exempel att du skapar ett experiment med modulen [Linjär regressionsmodul][linear-regression] och vill observera effekten av att ändra värdet av **utbildningshastigheten** på experimentresultaten. Du kör experimentet flera gånger med olika värden för den här parametern, enligt följande:

| Värde för inlärningsfrekvens | Kör starttid |
| --- | --- |
| 0,1 |2014-09-11 16:18:58 |
| 0.2 |2014-09-11 16:24:33 |
| 0,4 |2014-09-11 16:28:36 |
| 0,5 |2014-09-11 16:33:31 |

Om du klickar på **VISA KÖRHISTORIK**visas en lista över alla dessa körningar:

![Exempel på körhistorik](./media/manage-experiment-iterations/viewrunhistory.jpg)

Klicka på någon av dessa körningar om du vill visa en ögonblicksbild av experimentet när du körde det. Konfigurationen, parametervärden, kommentarer och resultat bevaras för att ge dig en fullständig post för körningen av experimentet.

> [!TIP]
> Om du vill dokumentera dina iterationer av experimentet kan du ändra titeln varje gång du kör den, du kan uppdatera **sammanfattningen** av experimentet i egenskapsfönstret och du kan lägga till eller uppdatera kommentarer till enskilda moduler för att registrera ändringarna. Rubrik-, sammanfattnings- och modulkommentarer sparas med varje körning av experimentet.
> 
> 

Listan över experiment på fliken **EXPERIMENT** i Machine Learning Studio (klassisk) visar alltid den senaste versionen av ett experiment. Om du öppnar en tidigare körning av experimentet (med **föregående körning** eller **VYKÖRNINGSHISTORIK)** kan du återgå till utkastversionen genom att klicka på **VISA KÖRHISTORIK** och välja den iteration som har **tillståndet** **Redigerbar**.

## <a name="iterating-on-a-previous-run"></a>Iterera på en föregående körning
När du klickar på **Föregående körning** eller **VISA KÖRHISTORIK** och öppnar en tidigare körning kan du visa ett avslutat experiment i skrivskyddat läge.

Om du vill påbörja en iteration av experimentet som börjar med det sätt som du konfigurerade det för en tidigare körning, kan du göra detta genom att öppna körningen och klicka på **SPARA SOM**. Detta skapar ett nytt experiment med en ny titel, en tom körningshistorik och alla komponenter och parametervärden för föregående körning. Det här nya experimentet visas på fliken **EXPERIMENTS** på startsidan för Machine Learning Studio (klassisk) och du kan ändra och köra det, vilket initierar en ny körningshistorik för den här iterationen av experimentet. 

Anta till exempel att du har experimentkörningshistoriken som visas i föregående avsnitt. Du vill observera vad som händer när du ställer in parametern **Utbildningshastighet** till 0,4 och prova olika värden för parametern **Antal träningsepoker.**

1. Klicka på **VISA KÖRHISTORIK** och öppna iterationen av experimentet som du körde klockan 16:28:36 (där du anger parametervärdet till 0,4).
2. Klicka på **SPARA SOM**.
3. Ange en ny rubrik **OK** och klicka på OK-bocken. En ny kopia av experimentet skapas.
4. Ändra parametern **Antal träningspopoker.**
5. Klicka på **KÖR**.

Du kan nu fortsätta att ändra och köra den här versionen av experimentet och skapa en ny körningshistorik för att spela in ditt arbete.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
