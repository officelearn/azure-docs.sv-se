---
title: 'Konvertera till CSV: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder konvertera till CSV-modulen i Azure Machine Learning-tjänsten för att konvertera en datauppsättning till en CSV-format som kan hämtas, exporteras eller delas med R eller Python skriptmoduler.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8b8b6758cc2df7a092ce36e9507f84ac534d0e3d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028732"
---
# <a name="convert-to-csv-module"></a>Konvertera till CSV-modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen om du vill konvertera en datauppsättning till en CSV-format som kan hämtas, exporteras eller delas med R eller Python skriptmoduler.

### <a name="more-about-the-csv-format"></a>Mer om CSV-format 

CSV-format som står för ”fil med kommaavgränsade värden”, är ett filformat som används av många externa machine learning-verktyg. CSV är ett vanligt format när du arbetar med öppen källkod språk som R eller Python.

Även om merparten av arbetet i Azure Machine Learning gör finns det gånger när det kan vara praktiskt att konvertera datauppsättningen till CSV kan använda med externa verktyg. Exempel:

+ Hämta CSV-fil för att öppna den med Excel eller importera den till en relationsdatabas.  
+ Spara CSV-filen molnlagring och ansluta till den från Power BI för att skapa visualiseringar.  
+ Använd CSV-format för att förbereda data för användning i R och Python. Högerklicka bara på utdata från modulen att generera kod som behövs för att komma åt data direkt från Python eller en Jupyter-anteckningsbok. 

När du konverterar en datauppsättning till CSV, spara filen i din Azure ML-arbetsyta. Du kan använda ett Azure storage-verktyg för att öppna och använda den direkt eller högerklickar du på modulen utdata och hämta CSV-filen till datorn eller använda det i R eller Python-kod.  

## <a name="how-to-configure-convert-to-csv"></a>Så här konfigurerar du konvertera till CSV

1.  Lägg till den [konvertera till CSV](./convert-to-csv.md) modulen i experimentet. Du hittar den här modulen i den **Format datakonvertering** i gränssnittet. 

2. Anslut den till alla moduler som visar en datauppsättning.   
  
3.  Kör experimentet.

### <a name="results"></a>Resultat
  

Dubbelklicka på utdata från [konvertera till CSV](./convert-to-csv.md), och välj ett av dessa alternativ.  

 + **Resultatet datauppsättning -> ladda ned**: Öppnas omedelbart en kopia av data i CSV-format som du kan spara till en lokal mapp. Om du inte anger en mapp, ett standardnamn tillämpas och CSV-fil som sparas i lokalt **hämtar** biblioteket.


 + **Resultatet datauppsättning -> Spara som datamängd**: Sparar CSV-filen till Azure ML-arbetsyta som en separat datauppsättning.

 + **Generera Dataåtkomstkoden**: Azure ML genererar två uppsättningar med kod som du kan komma åt data, antingen med hjälp av Python eller med hjälp av R. Du kommer åt data genom att kopiera kodfragmentet i ditt program. (*Generera Dataåtkomstkoden kommer snart.* )

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 