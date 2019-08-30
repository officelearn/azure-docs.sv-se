---
title: 'Konvertera till CSV: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen konvertera till CSV i Azure Machine Learning-tjänsten för att konvertera en data uppsättning till ett CSV-format som kan laddas ned, exporteras eller delas med R-eller Python-skript moduler.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 2b8f235014efe440e518f1f9c4d0d454f6918955
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128902"
---
# <a name="convert-to-csv-module"></a>Konvertera till CSV-modul

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att konvertera en data uppsättning till ett CSV-format som kan laddas ned, exporteras eller delas med R-eller Python-skript moduler.

### <a name="more-about-the-csv-format"></a>Mer om CSV-formatet 

CSV-formatet, som står för "kommaavgränsade värden", är ett fil format som används av många externa Machine Learning-verktyg. CSV är ett vanligt utbytes format när du arbetar med språk med öppen källkod, till exempel R eller python.

Även om du utför det mesta av ditt arbete i Azure Machine Learning, finns det tillfällen då du kan hitta det praktiskt att konvertera data uppsättningen till CSV och använda den i externa verktyg. Exempel:

+ Hämta CSV-filen för att öppna den med Excel, eller importera den till en Relations databas.  
+ Spara CSV-filen i moln lagringen och Anslut till den från Power BI för att skapa visualiseringar.  
+ Använd CSV-formatet för att förbereda data för användning i R och python. Högerklicka bara på utdata från modulen för att generera den kod som behövs för att komma åt data direkt från python eller en Jupyter Notebook. 

När du konverterar en data uppsättning till CSV sparas filen i din Azure ML-arbetsyta. Du kan använda ett Azure Storage-verktyg för att öppna och använda filen direkt, eller så kan du högerklicka på utdata och ladda ned CSV-filen till datorn eller använda den i R-eller python-kod.  

## <a name="how-to-configure-convert-to-csv"></a>Så här konfigurerar du Convert to CSV

1.  Lägg till modulen [konvertera till CSV](./convert-to-csv.md) i experimentet. Du hittar den här modulen i gruppen för **data format konvertering** i gränssnittet. 

2. Anslut den till en modul som matar ut en data uppsättning.   
  
3.  Kör experimentet.

### <a name="results"></a>Resultat
  

Dubbelklicka på utdata från [konvertera till CSV](./convert-to-csv.md)och välj ett av dessa alternativ.  

 + **Resultat data uppsättning – > nedladdning**: Öppnar omedelbart en kopia av data i CSV-format som du kan spara till en lokal mapp. Om du inte anger någon mapp används ett standard fil namn och CSV-filen sparas i biblioteket för lokala hämtningar.


 + **Resultat data uppsättning – > Spara som data uppsättning**: Sparar CSV-filen till Azure ML-arbetsytan som en separat data uppsättning.

 + **Generera kod för data åtkomst**: Azure ML genererar två kod uppsättningar som du kan använda för att komma åt data, antingen med hjälp av python eller med R. För att få åtkomst till data kopierar du kodfragmentet till ditt program. (*Generera data åtkomst kod kommer snart.* )

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 