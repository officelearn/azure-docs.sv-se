---
title: Hantera iterationer av experiment i Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Så här att hantera iterationer av experiment i Azure Machine Learning Studio. Du kan granska tidigare körningar för dina experiment när som helst för att utmana, gå tillbaka till, och slutligen antingen bekräfta eller förfina föregående antaganden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 503a93064b81e0db35c0477a11f2ee525680c854
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488455"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Hantera iterationer av experiment i Azure Machine Learning Studio
Utveckla en förutsägande analysmodell är en iterativ process - allteftersom du ändrar de olika funktioner och parametrar av experimentet resultaten att Konvergera tills du är nöjd att du har en tränad, effektiv modell. Nyckeln till den här processen för att spåra olika iterationer av dina experiment parametrar och konfigurationer.



Du kan granska tidigare körningar för dina experiment när som helst för att utmana, gå tillbaka till, och slutligen antingen bekräfta eller förfina föregående antaganden. När du kör ett experiment, sparar en historik över kör, bland annat datauppsättning, modulen kommunikationsportar och parametrar i Machine Learning Studio. Denna historik samlar även in resultat, runtime information, till exempel start- och stopptider, loggmeddelanden och jobbstatus. Du kan gå tillbaka till någon av dessa körs när som helst granska kronologisk ordning experiment och mellanliggande resultat. Du kan även använda en tidigare körning av experimentet för att starta i en ny fas i förfrågan och identifiering i sökvägen till att skapa enkla eller komplexa även ensemble modellering lösningar.

> [!NOTE]
> När du visar en tidigare körning av ett experiment som versionen av experimentet är låst och kan inte redigeras. Du kan dock spara en kopia av den genom att klicka på **Spara som** och ange ett nytt namn för kopian. Machine Learning Studio öppnas den nya kopian som du kan redigera och köra. Den här kopian av experimentet är tillgängliga i den **EXPERIMENT** lista tillsammans med alla andra experiment.
> 
> 

## <a name="viewing-the-prior-run"></a>Visa den tidigare körningen
När du har en öppen för experiment som du har kört minst en gång kan du visa den föregående körningen av experimentet genom att klicka på **tidigare kör** i egenskapsfönstret.

Anta exempelvis att du skapar ett experiment och kör versioner av paketet på 11:23 11:42 och 11:55. Om du öppnar den senaste körningen av experimentet (11:55) och klicka på **tidigare kör**, den version som du körde på 11:42 öppnas.

## <a name="viewing-the-run-history"></a>Visa Körningshistorik
Du kan visa alla tidigare körningar av ett experiment genom att klicka på **visa Körningshistorik** i en öppen experiment.

Anta exempelvis att du skapar ett experiment med den [linjär Regression] [ linear-regression] modulen och du vill se effekten av att ändra värdet för **inlärningsfrekvensen** på din Experimentera resultat. Du har kört experimentet flera gånger med olika värden för den här parametern på följande sätt:

| Learning värde för Bandbreddshastighet | Starttid för körning |
| --- | --- |
| 0,1 |9/11/2014 4:18:58 pm |
| 0.2 |9/11/2014 4:24:33 pm |
| 0.4 |9/11/2014 4:28:36 pm |
| 0,5 |9/11/2014 4:33:31 pm |

Om du klickar på **visa KÖRNINGSHISTORIK**, visas en lista över alla körningar:

![Exempel på Kör historik][runhistory]

Klicka på någon av dessa körningar att visa en ögonblicksbild av experimentet när du körde den. Konfiguration, parametervärden, kommentarer och resultat bevaras för att ge dig en fullständig post för den körningen av experimentet.

> [!TIP]
> Om du vill dokumentera din iterationer av experimentet, du kan ändra rubriken varje gång du kör den, kan du uppdatera den **sammanfattning** av experiment i egenskaperna för fönstret, och du kan lägga till eller uppdatera kommentarer på enskilda moduler som att registrera din ändringar. Rubrik, Sammanfattning och modulen kommentarer sparas med varje körning av experiment.
> 
> 

Listan över experiment i den **EXPERIMENT** fliken i Machine Learning Studio visas alltid den senaste versionen av ett experiment. Om du öppnar en tidigare körning av experiment (med hjälp av **tidigare kör** eller **visa KÖRNINGSHISTORIK**), du kan återgå till utkastet genom att klicka på **visa KÖRNINGSHISTORIK** och välja den iteration som har en **tillstånd** av **redigeras**.

## <a name="iterating-on-a-previous-run"></a>Iteration av en tidigare körning
När du klickar på **tidigare kör** eller **visa KÖRNINGSHISTORIK** och öppna en tidigare körning kan du visa en färdig experiment i skrivskyddat läge.

Om du vill börja en iterationer av experimentet som börjar med det sättet som du har konfigurerat för en tidigare körning kan du göra detta genom att öppna körningen och klicka på **Spara som**. Detta skapar ett nytt experiment med en ny rubrik, en tom körningshistorik, och alla komponenter och parametervärdena för föregående kör. Det här nya experimentet visas i den **EXPERIMENTEN** flik i Machine Learning Studio-startsidan och du kan ändra och köra den initierar en ny körningshistorik för den här iterationer av experimentet. 

Anta exempelvis att du har experimentet körningshistorik som visas i föregående avsnitt. Du vill se vad som händer när du ställer in den **inlärningsfrekvensen** parametern 0.4 och prova olika värden för den **antalet utbildning epoker** parametern.

1. Klicka på **visa KÖRNINGSHISTORIK** och öppna iterationer av experimentet som du körde klockan 4:28:36 (där du anger värdet för parametern till 0.4).
2. Klicka på **Spara som**.
3. Ange ett nytt namn och klicka på den **OK** bockmarkeringen. En ny kopia av experimentet har skapats.
4. Ändra den **antalet utbildning epoker** parametern.
5. Klicka på **kör**.

Du kan nu fortsätta att ändra och köra den här versionen av ditt experiment, att skapa en ny körningshistorik för att registrera ditt arbete.

<!-- Images -->
[runhistory]:./media/manage-experiment-iterations/viewrunhistory.jpg


<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
