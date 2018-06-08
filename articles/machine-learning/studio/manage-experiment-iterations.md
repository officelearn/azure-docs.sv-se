---
title: Hantera iterationer av experiment i Machine Learning Studio | Microsoft Docs
description: Hantera iterationer av experiment i Azure Machine Learning Studio
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6a53530f-20d5-40ae-9b49-7b499ccb44b7
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 4dcae0bb3cb89e65079b88f7be68ddf360ce1b8c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834819"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Hantera iterationer av experiment i Azure Machine Learning Studio
Utveckla en prediktiv analysmodell är en återkommande process - när du ändrar de olika funktioner och parametrar av experimentet resultaten att Konvergera tills du är nöjd du har en tränad, effektiv modell. Nyckel till den här processen för att spåra olika iterationer av dina experiment parametrar och konfigurationer.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Du kan granska tidigare körs från dina experiment när som helst för att utmaning, Kontrollera igen, och slutligen antingen bekräfta eller förfina tidigare antaganden. När du kör ett experiment, sparar en historik över kör, inklusive dataset, modul, och kommunikationsportar och parametrar i Machine Learning Studio. Denna historik avbildar också resultat, runtime information, till exempel start- och stopptider, loggmeddelanden och Körstatus. Du kan gå tillbaka till någon av dessa körs när som helst granska kronologisk ordning experiment och mellanliggande resultat. Du kan även använda en tidigare körning av experimentet starta till en ny fas i förfrågan och identifiering i din sökväg till att skapa enkla, komplexa eller även ensemble modellering lösningar.

> [!NOTE]
> När du visar en tidigare körning av ett experiment som versionen av experimentet är låst och kan inte redigeras. Du kan dock spara en kopia av den genom att klicka på **Spara som** och ange ett nytt namn för kopian. Machine Learning Studio öppnas den nya kopian som du kan redigera och köra. Den här kopian av experimentet är tillgängliga i den **EXPERIMENT** listan tillsammans med alla andra försök.
> 
> 

## <a name="viewing-the-prior-run"></a>Visa tidigare kör
När du har en öppen i experiment som du måste köra minst en gång, kan du visa den föregående körningen av experimentet genom att klicka på **tidigare kör** i egenskapsfönstret.

Anta att du skapar ett experiment och kör versioner av det på 11:23 11:42 och 11:55. Om du öppnar den senaste körningen av experimentet (11:55) och klicka på **tidigare kör**, den version som du körde på 11:42 öppnas.

## <a name="viewing-the-run-history"></a>Visa Körningshistoriken
Du kan visa alla tidigare körningar av ett experiment genom att klicka på **visa Körningshistorik** i en öppen experiment.

Anta att du skapar ett experiment med den [linjär Regression] [ linear-regression] modul och du vill se effekten av att ändra värdet för **inlärningsfrekvensen** på dina experiment resultat. Du har kört experimentet flera gånger med olika värden för den här parametern på följande sätt:

| Learning värde | Starttid för körning |
| --- | --- |
| 0,1 |2014-9-11 4:18:58 pm. |
| 0.2 |2014-9-11 4:24:33 pm |
| 0.4 |2014-9-11 4:28:36 pm |
| 0.5 |2014-9-11 4:33:31 pm. |

Om du klickar på **visa KÖRNINGSHISTORIK**, visas en lista över alla körs:

![Exempel kör historik][runhistory]

Klicka på någon av dessa körs ska visa en ögonblicksbild av när du har kört experimentet. Konfiguration, parametervärden, kommentarer och resultat bevaras så att du får en fullständig post för att körningen av experimentet.

> [!TIP]
> För att dokumentera din iterationer av experimentet, du kan ändra titeln varje gång du kör den, kan du uppdatera den **sammanfattning** av experiment i egenskaperna för fönstret, och du kan lägga till eller uppdatera kommentarer om enskilda moduler för att registrera dina ändringar. Rubrik, Sammanfattning och modulen kommentarer sparas med varje körning av experimentet.
> 
> 

Listan över experiment på den **EXPERIMENT** fliken i Machine Learning Studio visas alltid den senaste versionen av ett experiment. Om du öppnar en tidigare körning av experimentet (med hjälp av **tidigare kör** eller **visa KÖRNINGSHISTORIK**), du kan återgå till utkastet genom att klicka på **visa KÖRNINGSHISTORIK** och välja upprepning som har en **tillstånd** av **redigeras**.

## <a name="iterating-on-a-previous-run"></a>Iteration av en tidigare körning
När du klickar på **tidigare kör** eller **visa KÖRNINGSHISTORIK** och öppna en tidigare körning kan du visa en klar experiment i skrivskyddat läge.

Om du vill börja en iterationer av experimentet från och med konfiguration för en tidigare körning kan du göra detta genom att öppna kör och klicka på **Spara som**. Detta skapar ett nytt experiment med ett nytt namn, en tom som kör tidigare, och alla komponenter och parametervärden av föregående kör. Den här nya försöket visas i den **EXPERIMENTEN** fliken i Machine Learning Studio-startsidan, och du kan ändra och köra den initierar en ny kör historik för den här iterationer av experimentet. 

Anta att du har experimentet kör historik som visas i föregående avsnitt. Du vill se vad som händer när du ställer in den **inlärningsfrekvensen** parametern 0,4 och prova olika värden för den **antalet utbildning epoker** parameter.

1. Klicka på **visa KÖRNINGSHISTORIK** och öppna iteration av experimentet som du körde klockan 4:28:36 (där du anger parametervärdet till 0,4).
2. Klicka på **Spara som**.
3. Ange ett nytt namn och klicka på den **OK** markering. En ny kopia av experimentet har skapats.
4. Ändra den **antalet utbildning epoker** parameter.
5. Klicka på **kör**.

Du kan nu fortsätta att ändra och köra den här versionen av experimentet, skapa en ny körningshistoriken för att registrera ditt arbete.

<!-- Images -->
[runhistory]:./media/manage-experiment-iterations/viewrunhistory.jpg


<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
