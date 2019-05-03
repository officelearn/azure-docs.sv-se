---
title: 'Två Neuralt nätverk: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen Tvåklassförhöjt Neuralt nätverk i Azure Machine Learning-tjänsten för att skapa en modell för neurala nätverk som kan användas för att förutsäga ett mål som har bara två värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7ea852fcd312c6f7b1b716278ed538b7accde5bd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029227"
---
# <a name="two-class-neural-network-module"></a>Två Neural Network-modulen

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en modell för neurala nätverk som kan användas för att förutsäga ett mål som har bara två värden.

Klassificering med hjälp av neurala nätverk är en metod för övervakad inlärning och därför kräver ett *taggade datauppsättning*, som innehåller en kolumn för etiketten. Exempelvis kan du använda den här modellen neuralt nätverk för att förutsäga binära resultat som huruvida en patient har en viss sjukdom, eller om en dator troligen misslyckas inom en angiven tidsperiod.  

När du har definierat modellen tränar den genom att tillhandahålla en taggade datauppsättning och modellen som indata till [träna modell](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för nya indata.

### <a name="more-about-neural-networks"></a>Mer om neurala nätverk

Ett neurala nätverk är en uppsättning sammankopplade lager. Indata är det första lagret och är anslutna till ett lager för utdata med en Acyklisk graf som består av viktad kanter och noder.

Du kan infoga flera dolda lager mellan indata- och lager. De flesta förutsägande uppgifter kan utföras enkelt med bara en eller några dolda lager. Senaste forskning har dock visas att djupa neurala nätverk (DNN) med många lager kan vara effektiva i komplexa uppgifter som bild eller taligenkänning. Efterföljande lagren används för att modellera ökande nivåer av semantiska djup.

Relationen mellan indata och utdata är vet tränar det neurala nätverket på indata. Riktning för diagrammet fortsätter från indata via det dolda lagret och i utdata-lagret. Alla noder i ett lager är anslutna med viktad kanter till noder i nästa lager.

För att beräkna resultatet av nätverk för en viss indata beräknas ett värde vid varje nod i de dolda lager och utdata-lagret. Värdet anges genom att beräkna viktad summan av värdena för noder från det föregående lagret. En funktion för aktivering tillämpas sedan på den viktad summan.
  
## <a name="how-to-configure"></a>Så här konfigurerar du

1.  Lägg till den **Tvåklassförhöjt Neural Network** modulen i experimentet. Du hittar den här modulen under **Maskininlärning**, **initiera**i den **klassificering** kategori.  
  
2.  Ange hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.  
  
    -   **Enkel parametern**: Välj det här alternativet om du redan vet hur du vill konfigurera modellen.  

3.  För **dolda lager-specifikationen**, Välj typ av nätverksarkitektur att skapa.  
  
    -   **Anslutas fullständigt fallet**: Använder standard neuralt nätverksarkitektur, som definierats för två neurala nätverk på följande sätt:
  
        -   Har ett dolda lager.
  
        -   Utdata-lagret är anslutet till det dolda lagret och det dolda lagret anslutas fullständigt till inkommande lagret.
  
        -   Antalet noder i det inkommande lagret är lika med antalet funktioner i träningsdata.
  
        -   Antalet noder i det dolda lagret har angetts av användaren. Standardvärdet är 100.
  
        -   Antalet noder som är lika med antalet klasser. För ett tvåklassförhöjt neurala nätverk innebär det att alla indata måste mappas till en av två noder i utdata-lagret.

5.  För **inlärningsfrekvensen**, definiera storleken för de steg som togs vid varje iteration innan korrigering. Ett större värde för learning hastighet kan orsaka modellen att Konvergera snabbare, men det kan överskridande lokala minimi.

6.  För **antalet learning iterationer**, ange det maximala antalet gånger som algoritmen som ska bearbetas i träningsfall.

7.  För **inledande learning viktas diameter**, ange noden vikterna i början av processen learning.

8.  För **dynamiska**, ange en vikt och tillämpa under learning till noder från föregående iterationer  

10. Välj den **blanda exempel** alternativet blanda fall mellan iterationer. Om du avmarkerar det här alternativet kan bearbetas fall i exakt samma ordning varje gång du kör experimentet.
  
11. För **nummer slumptal**, skriver ett värde som ska användas som startvärde.
  
     Ange ett startvärde är värdet användbart när du vill säkerställa repeterbarhet över körningar av samma experimentet.  I annat fall används ett systemklockan värdet som startvärde, vilket kan orsaka lite olika resultat varje gång du kör experimentet.
  
13. Lägg till en taggade datauppsättning till arbetsytan för experimentet och koppla ett av de [utbildningsmoduler](module-reference.md).  
  
    -   Om du ställer in **skapande trainer läge** till **enda Parameter**, använda den [Träningsmodell](train-model.md) modulen.  
  
14. Kör experimentet.

## <a name="results"></a>Resultat

När utbildning har slutförts:

+ För att visa en sammanfattning av modellens parametrar, tillsammans med funktionen vikterna som registrerats från utbildnings- och andra parametrar för det neurala nätverket, högerklicka på utdata från [Träningsmodell](./train-model.md), och välj **visualisera**.  

+ Spara en ögonblicksbild av den tränade modellen genom att högerklicka på den **Trained model** utdata och välj **Spara som Trained Model**. Den här modellen uppdateras inte på efterföljande körningar av samma experimentet.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 
