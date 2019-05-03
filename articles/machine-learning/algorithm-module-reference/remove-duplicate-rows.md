---
title: 'Ta bort dubblettrader: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen ta bort dubbla rader i Azure Machine Learning-tjänsten för att ta bort möjliga dubbletter från en datauppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: dce90d911085c1f7330a2e0952bb9576c1d765fa
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029302"
---
# <a name="remove-duplicate-rows-module"></a>Ta bort dubbla rader modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Ta bort möjliga dubbletter från en datauppsättning med hjälp av den här modulen.

Anta exempelvis att dina data som returneras liknar följande och motsvarar flera poster för patienterna. 

| PatientID | Initialer| Kön|Ålder|Upptagna|
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

Det här exemplet har tydligt, flera kolumner med potentiellt kopierade data. Om de är faktiskt dubbletter beror på dina kunskaper om data. 

+ Du kan till exempel vet att många patienter har samma namn. Du skulle ta bort dubletter med alla kolumner, endast den **ID** kolumn. På så sätt kan filtreras endast rader med dubbla ID-värden bort, oavsett om patienterna som har samma namn eller inte.

+ Du kan också välja att tillåta dubbletter i ID-fältet och använda en annan kombination av filer för att hitta unika poster, till exempel förnamn, senaste namn, ålder och kön.  

Om du vill ange villkoren för en rad är duplicerat eller inte, du anger en enda kolumn eller en uppsättning kolumner som ska användas som **nycklar**. Två rader anses vara dubbletter endast när värdena i **alla** nyckelkolumner är lika. Om en rad har saknade värden för **nycklar**, inte anses vara dubblettrader. Till exempel om kön och ålder är inställda som nycklar i ovanstående tabell, rad 6 och 7 inte är dubblettrader beroende har saknade värden i ålder.

När du kör modulen, skapar en kandidat datauppsättning och returnerar en uppsättning rader som har inga dubbletter i uppsättningen kolumner som du har angett.

> [!IMPORTANT]
> Datauppsättningen för källan ändras inte, den här modulen skapar en ny datauppsättning som har filtrerats för att undanta dubbletter, utifrån de villkor du anger.

## <a name="how-to-use-remove-duplicate-rows"></a>Hur du använder ta bort duplicerad rader

1. Lägg till modulen i experimentet. Du hittar den **ta bort duplicerad rader** modulen under **Dataomvandling**, **manipulering av**.  

2. Anslut den datauppsättning som du vill kontrollera dubblettrader.

3. I den **egenskaper** fönstret under **nyckeln kolumnuttryck val av filter**, klickar du på **starta kolumnväljaren**, välja kolumner som ska användas i identifiering av dubbletter.

    I det här sammanhanget **nyckel** innebär inte en unik identifierare. Alla kolumner som du väljer med hjälp av kolumnväljaren är tilldelade som **nyckeln kolumner**. Alla omarkerade kolumnerna betraktas som icke-nyckelkolumner. Kombinationen av kolumner som du väljer som nycklar anger unikhet poster. (Se det som en SQL-instruktion som använder flera equalities kopplingar.)

    Exempel:

    + ”Jag vill kontrollera att ID: N är unika”: Välj endast ID-kolumn.
    + ”Jag vill kontrollera att kombinationen av förnamn, efternamn och ID: T är unikt”: Markera alla tre kolumner.

4. Använd den **Behåll första dubbla raden** kryssrutan för att ange vilken rad som returneras när dubbletter hittades:

    + Om den första raden returneras och andra tas bort. 
    + Om du avmarkerar det här alternativet om den sista duplicera raden sparas i resultatet och andra ignoreras. 

5. Kör experimentet.

6. Om du vill granska resultaten, högerklicka på modulen, Välj **resultat datauppsättning**, och klicka på **visualisera**. 

> [!TIP]
> Om resultatet är svåra att förstå, eller om du vill undanta vissa kolumner från räkningen kan du ta bort kolumner med hjälp av den [Välj kolumner i datauppsättning](./select-columns-in-dataset.md) modulen.

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 