---
title: 'Inom Neuralt nätverk: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen Multiclass Neuralt nätverk i Azure Machine Learning-tjänsten för att skapa en modell för neurala nätverk som kan användas för att förutsäga ett mål som har flera värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7bef225c001ebd9bbb9a45c8bcc301cfb49edb6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029392"
---
# <a name="multiclass-neural-network-module"></a>Inom Neural Network-modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en modell för neurala nätverk som kan användas för att förutsäga ett mål som har flera värden. 

Till exempel kan neurala nätverk för den här typen användas i komplexa datorn vision aktiviteter, till exempel siffra eller bokstav, dokumentklassificering och mönsterigenkänning.

Klassificering med hjälp av neurala nätverk är en metod för övervakad inlärning och därför kräver ett *taggade datauppsättning* som innehåller en kolumn för etiketten.

Du kan träna modellen genom att ange modellen och taggade datauppsättningen som indata till [träna modell](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya inkommande exempel.  

## <a name="about-neural-networks"></a>Om neurala nätverk

Ett neurala nätverk är en uppsättning sammankopplade lager. Indata är det första lagret och är anslutna till ett lager för utdata med en Acyklisk graf som består av viktad kanter och noder.

Du kan infoga flera dolda lager mellan indata- och lager. De flesta förutsägande uppgifter kan utföras enkelt med bara en eller några dolda lager. Senaste forskning har dock visas att djupa neurala nätverk (DNN) med många lager kan vara effektiva i komplexa uppgifter som bild eller taligenkänning. Efterföljande lagren används för att modellera ökande nivåer av semantiska djup.

Relationen mellan indata och utdata är vet tränar det neurala nätverket på indata. Riktning för diagrammet fortsätter från indata via det dolda lagret och i utdata-lagret. Alla noder i ett lager är anslutna med viktad kanter till noder i nästa lager.

För att beräkna resultatet av nätverk för en viss indata beräknas ett värde vid varje nod i de dolda lager och utdata-lagret. Värdet anges genom att beräkna viktad summan av värdena för noder från det föregående lagret. En funktion för aktivering tillämpas sedan på den viktad summan.

## <a name="configure-multiclass-neural-network"></a>Konfigurera inom Neuralt nätverk

1. Lägg till den **MultiClass Neural Network** modulen i experimentet i gränssnittet. Du hittar den här modulen under **Maskininlärning**, **initiera**i den **klassificering** kategori.

2. **Skapa trainer läge**: Använd det här alternativet om du vill ange hur modellen till tränas:

    - **Enkel parametern**: Välj det här alternativet om du redan vet hur du vill konfigurera modellen.

    

3. **Dolda lager-specifikationen**: Välj typ av nätverksarkitektur att skapa.

    - **Anslutas fullständigt fallet**: Välj det här alternativet för att skapa en modell med hjälp av standard neuralt nätverk-arkitekturen. För multiklass-baserad neuralt nätverk modeller är standardvärdena på följande sätt:

        - Ett dolda lager
        - Utdata-lagret är anslutet till det dolda lagret.
        - Det dolda lagret anslutas fullständigt till inkommande lagret.
        - Antalet noder i det inkommande lagret bestäms av hur många av funktionerna i träningsdata.
        - Antalet noder i det dolda lagret kan ställas in av användaren. Standardvärdet är 100.
        - Antalet noder i utdata-lagret beror på antal klasser.
  
   

5. **Antal dolda noder**: Det här alternativet kan du anpassa antalet dolda noder i standard-arkitektur. Ange antalet noder som är dolda. Standardvärdet är ett dolt lager med 100 noder.

6. **Inlärningsfrekvensen**: Definiera storleken på de steg som togs vid varje iteration innan korrigering. Ett större värde för learning hastighet kan orsaka modellen att Konvergera snabbare, men det kan överskridande lokala minimi.

7. **Antal learning iterationer**: Ange det maximala antalet gånger som algoritmen som ska bearbetas i träningsfall.

8. **Inledande learning viktas diameter**: Ange noden vikterna i början av processen learning.

9. **Dynamiska**: Ange en vikt och tillämpa under learning till noder från föregående iterationer.
  
11. **Blanda exempel**: Välj det här alternativet blanda fall mellan iterationer.

    Om du avmarkerar det här alternativet kan bearbetas fall i exakt samma ordning varje gång du kör experimentet.

12. **Slumpmässigt nummer seed**: Ange ett värde som används som startvärde, om du vill säkerställa repeterbarhet över körningar av samma experimentet.

14. Ansluta en datauppsättning för träning och en av de [utbildningsmoduler](module-reference.md): 

    - Om du ställer in **skapande trainer läge** till **enda Parameter**, använda [Träningsmodell](train-model.md).  
  

## <a name="results"></a>Resultat

När utbildning har slutförts:

- För att visa en sammanfattning av modellens parametrar, tillsammans med funktionen vikterna som registrerats från utbildnings- och andra parametrar för det neurala nätverket, högerklicka på utdata från [Träningsmodell](./train-model.md) och välj **visualisera**.  

- Spara en ögonblicksbild av den tränade modellen genom att högerklicka på den **Trained model** utdata och välj **Spara som Trained Model**. Den här modellen uppdateras inte på efterföljande körningar av samma experimentet.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 