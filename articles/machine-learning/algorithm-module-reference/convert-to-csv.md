---
title: 'Konvertera till CSV: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Konvertera till CSV i Azure Machine Learning för att konvertera en datauppsättning till ett CSV-format som kan hämtas, exporteras eller delas med R- eller Python-skriptmoduler.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477688"
---
# <a name="convert-to-csv-module"></a>Konvertera till CSV-modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att konvertera en datauppsättning till ett CSV-format som kan hämtas, exporteras eller delas med R- eller Python-skriptmoduler.

### <a name="more-about-the-csv-format"></a>Mer om CSV-formatet 

CSV-formatet, som står för "kommaavgränsade värden", är ett filformat som används av många externa verktygsinlärningsverktyg. CSV är ett vanligt utbytesformat när du arbetar med språk med öppen källkod som R eller Python.

Även om du gör det mesta av ditt arbete i Azure Machine Learning finns det tillfällen då du kan ha praktiskt att konvertera datauppsättningen till CSV för användning i externa verktyg. Ett exempel:

+ Hämta CSV-filen för att öppna den med Excel eller importera den till en relationsdatabas.  
+ Spara CSV-filen i molnlagring och anslut till den från Power BI för att skapa visualiseringar.  
+ Använd CSV-formatet för att förbereda data för användning i R och Python. 

När du konverterar en datauppsättning till CSV sparas csv:en på din Azure ML-arbetsyta. Du kan använda ett Azure-lagringsverktyg för att öppna och använda filen direkt. Du kan också komma åt CSV i designern genom att välja modulen **Konvertera till CSV** och sedan välja histogramikonen under fliken **Utdata** på den högra panelen för att visa utdata. Du kan hämta CSV från mappen Resultat till en lokal katalog.  

## <a name="how-to-configure-convert-to-csv"></a>Konfigurera Konvertera till CSV


1.  Lägg till modulen Konvertera till CSV i pipelinen. Du hittar den här modulen i gruppen **Dataomvandling** i designern. 

2. Anslut den till alla moduler som matar ut en datauppsättning.   
  
3.  Skicka pipelinen.

### <a name="results"></a>Resultat
  

Välj fliken **Utdata** på den högra panelen **konvertera till CSV**och välj på en av dessa ikoner under **portutgångarna**.  

+ **Registrera datauppsättning:** Välj ikonen och spara CSV-filen tillbaka till Azure ML-arbetsytan som en separat datauppsättning. Du hittar datauppsättningen som en modul i modulträdet under avsnittet **Mina datauppsättningar.**

 + **Visa utdata:** Välj ögonikonen och följ instruktionerna för att bläddra i **mappen Results_dataset** och hämta filen data.csv.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 