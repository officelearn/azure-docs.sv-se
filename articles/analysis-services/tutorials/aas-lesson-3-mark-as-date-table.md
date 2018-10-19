---
title: 'Azure Analysis Services självstudiekurs 3: Markera som datumtabell | Microsoft Docs'
description: Beskriver hur du markerar en datumtabell i Azure Analysis Services-självstudiekursprojektet.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9cbbf8c5ea05915293c785028bdd0a47ba081036
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426030"
---
# <a name="mark-as-date-table"></a>Markera som datumtabell

I ”lektion 2: Hämta data” importerade du en dimensionstabell med namnet DimDate. I din modell heter den här tabellen DimDate, och den kan också kallas *datumtabell* eftersom den innehåller information om datum och tid.  
  
När du använder funktioner för DAX-tidsinformation, till exempel när du skapar mått senare, måste du ange egenskaper som innehåller en *datumtabell* och en unik *datumkolumn*-identifierare i tabellen.
  
Under den här lektionen markerar du DimDate-tabellen som *datumtabell* och datumkolumnen (i datumtabellen) som *datumkolumn* (unik identifierare).  

Innan du markerar datumtabellen och datumkolumnen är det dags att göra några ändringar så att det blir enklare att förstå din modell. Observera att det finns en kolumn med namnet **FullDateAlternateKey** i tabellen DimDate. Den här kolumnen innehåller en rad för varje dag i varje kalenderår som ingår i tabellen. Du använder ofta den här kolumnen i måttformler och i rapporter. Men FullDateAlternateKey är inte så bra identifierare för den här kolumnen. Du döper om den till **Datum** så att den blir enklare att identifiera och ta med i formler. Om det går är det en god idé att byta namn på objekt som tabeller och kolumner så att de blir enklare att identifiera i SSDT och klientrapporteringsprogram som Power BI och Excel. 
  
Uppskattad tidsåtgång för den här lektionen: **Tre minuter**  
  
## <a name="prerequisites"></a>Förutsättningar  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna under den här lektionen bör du ha slutfört föregående lektion: [Lektion 2: Hämta data](../tutorials/aas-lesson-2-get-data.md). 

### <a name="to-rename-the-fulldatealternatekey-column"></a>Byta namn på kolumnen FullDateAlternateKey

1.  Klicka på tabellen **DimDate** i modelldesignern.

2.  Dubbelklicka på rubriken för kolumnen **FullDateAlternateKey** och byt sedan namn till **Datum**.

  
### <a name="to-set-mark-as-date-table"></a>Markera som datumtabell  
  
1.  Välj kolumnen **Datum** och kontrollera sedan att **Datum** är markerat under **Datatyp** i fönstret **Egenskaper**.  
  
2.  Klicka på **Tabell**-menyn, klicka på **Datum** och klicka sedan på **Markera som datumtabell**.  
  
3.  I dialogrutan **Markera som datumtabell** väljer du kolumnen **Datum** som unik identifierare i listrutan **Datum**. Den är vanligtvis markerad som standard. Klicka på **OK**. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>Nästa steg
[Lektion 4: Skapa relationer](../tutorials/aas-lesson-4-create-relationships.md).
  
