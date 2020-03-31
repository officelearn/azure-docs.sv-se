---
title: 'Ta bort dubblettrader: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Ta bort dubblettrader i Azure Machine Learning för att ta bort potentiella dubbletter från en datauppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 490d3305abcbcd906a0f727d736db8cab7e4287e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456029"
---
# <a name="remove-duplicate-rows-module"></a>Ta bort modulen Dubblettrader

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen om du vill ta bort potentiella dubbletter från en datauppsättning.

Anta till exempel att dina data ser ut så här och representerar flera poster för patienter. 

| PatientID | Initialer| Kön|Ålder|Erkände|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Feb|
|4| F.M.| M| 23| Feb|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Det här exemplet har tydligt flera kolumner med potentiellt dubblettdata. Om de faktiskt är dubbletter beror på din kunskap om data. 

+ Du kanske till exempel vet att många patienter har samma namn. Du skulle inte eliminera dubbletter med hjälp av några namnkolumner, bara **ID-kolumnen.** På så sätt filtreras bara raderna med dubbla ID-värden ut, oavsett om patienterna har samma namn eller inte.

+ Du kan också välja att tillåta dubbletter i ID-fältet och använda någon annan kombination av filer för att hitta unika poster, till exempel förnamn, efternamn, ålder och kön.  

Om du vill ange villkor för om en rad ska dupliceras eller inte anger du en enskild kolumn eller en uppsättning kolumner som ska användas som **nycklar**. Två rader betraktas endast som dubbletter när värdena i **alla** nyckelkolumner är lika. Om någon rad har saknat värde för **nycklar**betraktas de inte som dubblettrader. Om kön och ålder till exempel anges som Nycklar i tabellen ovan är rad 6 och 7 inte dubblettrader med tanke på att de saknar värde i Ålder.

När du kör modulen skapas en kandidatdatauppsättning och en uppsättning rader som inte har några dubbletter över den uppsättning kolumner som du har angett.

> [!IMPORTANT]
> Källdatauppsättningen ändras inte. Den här modulen skapar en ny datauppsättning som filtreras för att utesluta dubbletter, baserat på de villkor du anger.

## <a name="how-to-use-remove-duplicate-rows"></a>Så här använder du Ta bort dubblettrader

1. Lägg till modulen i pipelinen. Du hittar modulen **Ta bort dubblettrader** under **Dataomvandling**, **Manipulation**.  

2. Anslut den datauppsättning som du vill söka efter dubblettrader.

3. Klicka **Properties** på **Starta kolumnväljare**under **Filteruttryck för nyckelkolumner under Filteruttryck**för nyckelkolumn , för att välja kolumner som ska användas för att identifiera dubbletter.

    I det här sammanhanget betyder **key** inte en unik identifierare. Alla kolumner som du väljer med kolumnväljaren betecknas som **nyckelkolumner**. Alla omarkerade kolumner betraktas som icke-nyckelkolumner. Kombinationen av kolumner som du väljer som nycklar avgör posternas unika egenskaper. (Tänk på det som ett SQL-uttryck som använder flera kopplingar mellan utjämningsbara personer.)

    Exempel:

    + "Jag vill se till att ID:er är unika": Välj bara ID-kolumnen.
    + "Jag vill se till att kombinationen av förnamn, efternamn och ID är unik": Markera alla tre kolumnerna.

4. Använd kryssrutan **Behåll den första dubblettraden** för att ange vilken rad som ska returneras när dubbletter hittas:

    + Om du väljer det här alternativet returneras den första raden och andra ignoreras. 
    + Om du avmarkerar det här alternativet sparas den sista dubblettraden i resultatet och andra ignoreras. 

5. Skicka pipelinen.

6. Om du vill granska resultaten högerklickar du på modulen och väljer **Visualisera**. 

> [!TIP]
> Om resultaten är svåra att förstå, eller om du vill utesluta vissa kolumner från övervägande, kan du ta bort kolumner med hjälp av modulen [Välj kolumner i datauppsättning.](./select-columns-in-dataset.md)

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 