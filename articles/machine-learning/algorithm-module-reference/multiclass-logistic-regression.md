---
title: 'Multiclass Logistic Regression: Modul referens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Multiclass Logistic Regression i Azure Machine Learning för att skapa en logistisk regressionsmodell som kan användas för att förutsäga flera värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ca446b0ab67a8a202c1f4d505262660ac55f42db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456173"
---
# <a name="multiclass-logistic-regression-module"></a>Multiclass Logistic Regression modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en logistisk regressionsmodell som kan användas för att förutsäga flera värden.

Klassificering med hjälp av logistisk regression är en övervakad inlärningsmetod och kräver därför en märkt datauppsättning. Du tränar modellen genom att tillhandahålla modellen och den märkta datauppsättningen som en indata till en modul som [Tågmodell](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för nya indataexempel.

Azure Machine Learning innehåller också en [tvåklassig logistisk regressionsmodul,](./two-class-logistic-regression.md) som är lämplig för klassificering av binära eller dikoltomiska variabler.

## <a name="about-multiclass-logistic-regression"></a>Om multiclass logistic regression

Logistisk regression är en välkänd metod i statistik som används för att förutsäga sannolikheten för ett resultat, och är populär för klassificeringsuppgifter. Algoritmen förutsäger sannolikheten för förekomst av en händelse genom att anpassa data till en logistisk funktion. 

Vid logistisk regression med flera klasser kan klassificeraren användas för att förutsäga flera utfall.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurera en logistisk regression med fleraklasser

1. Lägg till modulen **Multiclass Logistic Regression** i pipelinen.

2. Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**

    + **Enkel parameter:** Använd det här alternativet om du vet hur du vill konfigurera modellen och anger en specifik uppsättning värden som argument.

    + **Parameterintervall:** Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra ett parametervep. Välj ett intervall med värden som du vill iterera över, och [Tune Model Hyperparameters itererar](tune-model-hyperparameters.md) över alla möjliga kombinationer av de inställningar du angav för att bestämma de hyperparametrar som ger optimalt resultat.  

3. **Optimeringstolerans**anger du tröskelvärdet för optimerarkonvergens. Om förbättringen mellan iterationer är mindre än tröskelvärdet stannar algoritmen och returnerar den aktuella modellen.

4. **L1-regulariseringsvikt**, **L2-regulariseringsvikt:** Ange ett värde som ska användas för legaliseringsparametrarna L1 och L2. Ett värde som inte är noll rekommenderas för båda.

    Regularization är en metod för att förhindra övermontering genom att straffa modeller med extrema koefficientvärden. Legalisering fungerar genom att lägga till den påföljd som är associerad med koefficientvärden till felet i hypotesen. En korrekt modell med extrema koefficientvärden skulle straffas mer, men en mindre exakt modell med mer konservativa värden skulle straffas mindre.

     L1 och L2 regularization har olika effekter och användningsområden. L1 kan tillämpas på glesa modeller, vilket är användbart när man arbetar med högdimensionella data. Däremot är L2-legalisering att föredra för data som inte är glesa.  Den här algoritmen stöder en linjär kombination av L1- `x = L1` `y = L2`och `ax + by = c` L2-legaliseringsvärden: det vill än, om och , definierar det linjära intervallet för legaliseringstermerna.

     Olika linjära kombinationer av L1 och L2 termer har utformats för logistisk regression modeller, såsom [elastisk netto regularization](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Slumptalsfrö:** Skriv ett heltalsvärde som ska användas som utsände för algoritmen om du vill att resultaten ska vara repeterbara över körningar. Annars används ett systemklockvärde som frö, vilket kan ge något olika resultat i körningar av samma pipeline.

8. Anslut en märkt datauppsättning och en av tågmodulerna:

    + Om du ställer in **Skapa trainer-läge** till **En parameter**använder du modulen [Tågmodell.](./train-model.md)

9. Skicka pipelinen.



## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 