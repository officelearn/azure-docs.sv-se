---
title: 'Konvertera till datauppsättning: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Konvertera till datauppsättning i Azure Machine Learning för att konvertera datainmatning till det interna datauppsättningsformat som används av Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: fd45db779b7a828d247f09cae38f90fc20d12c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456598"
---
# <a name="convert-to-dataset"></a>Konvertera till datamängd

I den här artikeln beskrivs hur du använder modulen Konvertera till datauppsättning i Azure Machine Learning designer (förhandsversion) för att konvertera data för en pipeline till designerns interna format.
  
Konvertering krävs inte i de flesta fall. Azure Machine Learning konverterar implicit data till sitt ursprungliga datauppsättningsformat när en åtgärd utförs på data. 

Vi rekommenderar att du sparar data i datauppsättningsformatet om du har utfört någon form av normalisering eller rengöring på en uppsättning data, och du vill se till att ändringarna används i andra pipelines.  
  
> [!NOTE]
> Konvertera till Datauppsättning ändrar bara formatet på data. En ny kopia av data på arbetsytan sparas inte. Om du vill spara datauppsättningen dubbelklickar du på utdataporten, väljer **Spara som datauppsättning**och anger ett nytt namn.  
  
## <a name="how-to-use-convert-to-dataset"></a>Så här använder du Konvertera till datauppsättning  

Vi rekommenderar att du använder modulen [Redigera metadata](edit-metadata.md) för att förbereda datauppsättningen innan du använder Konvertera till datauppsättning. Du kan lägga till eller ändra kolumnnamn, justera datatyper och göra andra ändringar efter behov.

1.  Lägg till modulen Konvertera till datauppsättning i pipelinen. Du hittar den här modulen i kategorin **Dataomvandling** i designern. 

2. Anslut den till alla moduler som matar ut en datauppsättning.   

    Så länge data är [tabellformiga](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)kan du konvertera dem till en datauppsättning. Detta inkluderar data som läses in via [importdata,](import-data.md)data som skapats via [Returdata manuellt](enter-data-manually.md)eller datauppsättningar som transformerats via [Använd omvandling](apply-transformation.md).

3.  I listrutan **Åtgärd** anger du om du vill göra någon rensning av data innan du sparar datauppsättningen:  
  
    - **Ingen**: Använd data som de är.  
  
    - **SetMissingValue**: Ange ett specifikt värde till ett värde som saknas i datauppsättningen. Standardplatshållaren är frågetecknet (?), men du kan använda alternativet **Anpassat värde som saknas** för att ange ett annat värde. Om du till exempel anger **Tax** for **Custom saknas värde**ändras alla instanser av **Taxi** i datauppsättningen till det saknade värdet.
  
    - **ReplaceValues**: Använd det här alternativet om du vill ange ett exakt värde som ska ersättas med ett annat exakt värde. Du kan ersätta saknade värden eller anpassade värden genom att ange metoden **Ersätt:**

      - **Saknas**: Välj det här alternativet om du vill ersätta saknade värden i indatauppsättningen. För **Nytt värde**anger du värdet som värdena saknas med.
      - **Anpassad:** Välj det här alternativet om du vill ersätta anpassade värden i indatauppsättningen. För **anpassat värde**anger du det värde som du vill söka efter. Om dina data till exempel `obs` innehåller strängen som används som `obs`platshållare för saknade värden anger du . För **Nytt värde**anger du det nya värdet som den ursprungliga strängen ska ersättas med.
  
    Observera att åtgärden **ReplaceValues** endast gäller för exakta matchningar. Dessa strängar påverkas till exempel `obs.`inte: `obsolete`, .  
 
  
5.  Skicka pipelinen.  

## <a name="results"></a>Resultat

+  Om du vill spara den resulterande datauppsättningen med ett nytt namn väljer du på ikonen **Registrera datauppsättning** under fliken **Utdata** på modulens högra panel.  
  
## <a name="technical-notes"></a>Tekniska anmärkningar  

-   Alla moduler som tar en datauppsättning som indata kan också ta data i CSV-filen eller TSV-filen. Innan någon modulkod körs är ingångarna förbearbetade. Förbearbetning motsvarar att köra modulen Konvertera till datauppsättning på indata.  
  
-   Du kan inte konvertera från SVMLight-formatet till en datauppsättning.  
  
-   När du anger en anpassad ersättningsåtgärd gäller sök- och ersättningsåtgärden för fullständiga värden. Partiella matchningar är inte tillåtna. Du kan till exempel ersätta en 3 med en -1 eller med 33, men du kan inte ersätta en 3 i ett tvåsiffrigt tal som 35.  
  
-   För anpassade ersättningsåtgärder misslyckas ersättningen tyst om du använder ett tecken som inte överensstämmer med kolumnens aktuella datatyp som ersättning.  

  
## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
