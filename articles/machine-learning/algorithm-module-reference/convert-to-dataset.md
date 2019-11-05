---
title: 'Konvertera till data uppsättning: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen konvertera till data uppsättning i Azure Machine Learning-tjänsten för att konvertera data indata till det interna data uppsättnings formatet som används av Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: b19d812d45b625531583aff726571c7da59f97f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516258"
---
# <a name="convert-to-dataset"></a>Konvertera till data uppsättning

Den här artikeln beskriver hur du använder modulen [konvertera till data uppsättning](convert-to-dataset.md) i Azure Machine Learning designer (för hands version) för att konvertera data för en pipeline till det interna format som används av designern.
  
Det krävs inte konvertering i de flesta fall eftersom Azure Machine Learning implicit konverterar data till det ursprungliga data uppsättnings formatet när en åtgärd utförs på data. 

Att spara data i data uppsättnings format rekommenderas dock om du har utfört någon typ av normalisering eller rensning av en uppsättning data och du vill se till att ändringarna används i ytterligare pipeliner.  
  
> [!NOTE]
> [Konvertera till data uppsättning](convert-to-dataset.md) ändrar endast data formatet. En ny kopia av data sparas inte i arbets ytan. Om du vill spara data uppsättningen dubbelklickar du på utdataporten, väljer **Spara som data uppsättning**och anger ett nytt namn.  
  
## <a name="how-to-use-convert-to-dataset"></a>Använda konvertera till data uppsättning  

Vi rekommenderar att du använder modulen [Redigera metadata](edit-metadata.md) för att förbereda data uppsättningen innan [du använder konvertera till data uppsättning](convert-to-dataset.md).  Du kan lägga till eller ändra kolumn namn, justera data typer och så vidare.

1.  Lägg till modulen [konvertera till data uppsättning](convert-to-dataset.md) i din pipeline. Du hittar den här modulen i kategorin **data omvandling** i designern. 

2. Anslut den till en modul som matar ut en data uppsättning.   

    Så länge data är [tabell](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py), kan du konvertera dem till en data uppsättning. Detta inkluderar data som läses in med [Importera data](import-data.md), data som skapats med hjälp av [ange data manuellt](enter-data-manually.md)eller data uppsättningar som omvandlas med hjälp av [tillämpa omvandling](apply-transformation.md).

3.  I list rutan **åtgärd** anger du om du vill göra några rensningar av data innan du sparar data uppsättningen:  
  
    - **Ingen**: Använd datan som de är.  
  
    - **SetMissingValue**: Ange ett värde som saknas i data uppsättningen. Standard plats hållaren är frågetecknet (?), men du kan använda alternativet **anpassat värde som saknas** för att ange ett annat värde. Om du till exempel skriver "taxi" för **anpassat saknat värde**, kommer alla "taxi" i data uppsättningen att ändras till saknat värde.
  
    - **ReplaceValues**: Använd det här alternativet för att ange ett enda exakt värde som ska ersättas med andra exakta värden.  Du kan ersätta saknade värden eller anpassade värden genom att ange **ersättnings** Metod:- **saknas**: Välj det här alternativet om du vill ersätta saknade värden i data uppsättningen. För **nytt värde**anger du det värde som du vill ersätta de saknade värdena med.
            - **anpassad**: Välj det här alternativet om du vill ersätta anpassade värden i indata-datauppsättningen. För **anpassat värde**anger du det värde som du vill söka efter. Anta till exempel att dina data innehåller strängen `obs` används som plats hållare för värden som saknas, och skriv `obs`. För **nytt värde**anger du det nya värdet som ersätter den ursprungliga strängen med.
  
    Observera att **ReplaceValues** -åtgärden endast gäller för exakta matchningar. Dessa strängar kommer till exempel inte att påverkas: `obs.``obsolete`.  
 
  
5.  Kör pipelinen eller högerklicka på modulen [konvertera till data uppsättning](convert-to-dataset.md) och välj **Kör vald**.  

## <a name="results"></a>Resultat

+  Om du vill spara den resulterande data uppsättningen med ett nytt namn högerklickar du på utdata från [konvertera till data uppsättning](convert-to-dataset.md) och väljer **Spara som data uppsättning**.  
  
## <a name="technical-notes"></a>Tekniska anteckningar  

Det här avsnittet innehåller implementerings information, tips och svar på vanliga frågor.

-   Alla moduler som tar en data uppsättning som indata kan också ta data i CSV-eller TSV. Innan en kod körs utförs förbehandling av indata, vilket motsvarar att köra modulen [konvertera till data uppsättning](convert-to-dataset.md) i indata.  
  
-   Det går inte att konvertera från SVMLight-formatet till data uppsättningen.  
  
-   När du anger en anpassad ersättnings åtgärd gäller åtgärden Sök och Ersätt för fullständiga värden. partiella matchningar är inte tillåtna. Du kan till exempel ersätta en 3 med en-1 eller med 33, men du kan inte ersätta en 3 med tvåsiffriga tal som 35.  
  
-   För anpassade ersättnings åtgärder, kommer ersättningen att Miss varnas tyst om du använder som ersättning för alla bokstäver som inte överensstämmer med den aktuella data typen för kolumnen.  

  
## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 
