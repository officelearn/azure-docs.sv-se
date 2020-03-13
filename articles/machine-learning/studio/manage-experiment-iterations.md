---
title: Hantera experimentupprepningar
titleSuffix: ML Studio (classic) - Azure
description: Hantera experiment upprepningar i Azure Machine Learning Studio (klassisk). Du kan granska tidigare körningar för dina experiment när som helst för att utmana, gå tillbaka till, och slutligen antingen bekräfta eller förfina föregående antaganden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 5c8a278f09fdb3b605020e4c2fcf7aa2776906e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204315"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Hantera experiment upprepningar i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Utveckla en förutsägande analysmodell är en iterativ process - allteftersom du ändrar de olika funktioner och parametrar av experimentet resultaten att Konvergera tills du är nöjd att du har en tränad, effektiv modell. Nyckeln till den här processen för att spåra olika iterationer av dina experiment parametrar och konfigurationer.



Du kan granska tidigare körningar för dina experiment när som helst för att utmana, gå tillbaka till, och slutligen antingen bekräfta eller förfina föregående antaganden. När du kör ett experiment behåller Machine Learning Studio (klassisk) en historik över körningen, inklusive data uppsättning, modul och port anslutningar och-parametrar. Denna historik samlar även in resultat, runtime information, till exempel start- och stopptider, loggmeddelanden och jobbstatus. Du kan gå tillbaka till någon av dessa körs när som helst granska kronologisk ordning experiment och mellanliggande resultat. Du kan även använda en tidigare körning av experimentet för att starta i en ny fas i förfrågan och identifiering i sökvägen till att skapa enkla eller komplexa även ensemble modellering lösningar.

> [!NOTE]
> När du visar en tidigare körning av ett experiment som versionen av experimentet är låst och kan inte redigeras. Du kan dock spara en kopia av den genom att klicka på **Spara som** och ange ett nytt namn för kopian. Machine Learning Studio (klassisk) öppnar den nya kopian, som du sedan kan redigera och köra. Den här kopian av ditt experiment är tillgänglig i **experiment** listan tillsammans med alla andra experiment.
> 
> 

## <a name="viewing-the-prior-run"></a>Visa den tidigare körningen
När du har ett experiment öppet som du har kört minst en gång kan du Visa föregående körning av experimentet genom att klicka på **föregående körning** i rutan Egenskaper.

Anta exempelvis att du skapar ett experiment och kör versioner av paketet på 11:23 11:42 och 11:55. Om du öppnar den senaste körningen av experimentet (11:55) och klickar på **föregående körning**öppnas den version du körde vid 11:42.

## <a name="viewing-the-run-history"></a>Visa Körningshistorik
Du kan visa alla tidigare körningar av ett experiment genom att klicka på **Visa körnings historik** i ett öppet experiment.

Anta till exempel att du skapar ett experiment med modulen [linjär regression][linear-regression] och att du vill se hur du ändrar värdet för **inlärnings frekvensen** för dina experiment resultat. Du har kört experimentet flera gånger med olika värden för den här parametern på följande sätt:

| Learning värde för Bandbreddshastighet | Starttid för körning |
| --- | --- |
| 0,1 |9/11/2014 4:18:58 pm |
| 0.2 |9/11/2014 4:24:33 pm |
| 0,4 |9/11/2014 4:28:36 pm |
| 0,5 |9/11/2014 4:33:31 pm |

Om du klickar på **Visa körnings historik**visas en lista över alla dessa körningar:

![Exempel på Kör historik](./media/manage-experiment-iterations/viewrunhistory.jpg)

Klicka på någon av dessa körningar att visa en ögonblicksbild av experimentet när du körde den. Konfiguration, parametervärden, kommentarer och resultat bevaras för att ge dig en fullständig post för den körningen av experimentet.

> [!TIP]
> Om du vill dokumentera dina iterationer av experimentet kan du ändra titeln varje gång du kör det, så att du kan uppdatera **sammanfattningen** av experimentet i fönstret Egenskaper och du kan lägga till eller uppdatera kommentarer om enskilda moduler för att registrera dina ändringar. Rubrik, Sammanfattning och modulen kommentarer sparas med varje körning av experiment.
> 
> 

I listan över experiment på fliken **experiment** i Machine Learning Studio (klassisk) visas alltid den senaste versionen av ett experiment. Om du öppnar en tidigare körning av experimentet (med tidigare **Kör** eller **Visa körnings historik**) kan du gå tillbaka till utkastet genom att klicka på **Visa körnings historik** och välja den iteration som har **statusen** **redigerbar**.

## <a name="iterating-on-a-previous-run"></a>Iteration av en tidigare körning
När du klickar på **föregående körning** eller **Visa körnings historik** och öppnar en tidigare körning kan du visa ett klart experiment i skrivskyddat läge.

Om du vill påbörja en upprepning av experimentet som börjar med det sätt som du konfigurerade det för en tidigare körning, kan du göra det genom att öppna körningen och klicka på **Spara som**. Detta skapar ett nytt experiment med en ny rubrik, en tom körningshistorik, och alla komponenter och parametervärdena för föregående kör. Det nya experimentet visas på fliken **experiment** på start sidan Machine Learning Studio (klassisk), och du kan ändra och köra det och påbörja en ny körnings historik för den här iterationen av experimentet. 

Anta exempelvis att du har experimentet körningshistorik som visas i föregående avsnitt. Du vill veta vad som händer när du ställer in **Learning Rate** -parametern på 0,4 och provar olika värden för **antalet epoker** -parametrar.

1. Klicka på **Visa körnings historik** och öppna upprepningen av experimentet som du körde vid 4:28:36 PM (där du angav parametervärdet till 0,4).
2. Klicka på **Spara som**.
3. Ange en ny rubrik och klicka på **OK** -bocken. En ny kopia av experimentet har skapats.
4. Ändra **antalet epoker** -parametrar.
5. Klicka på **Kör**.

Du kan nu fortsätta att ändra och köra den här versionen av ditt experiment, att skapa en ny körningshistorik för att registrera ditt arbete.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
