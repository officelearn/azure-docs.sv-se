---
title: Bearbeta textfiler med fast längd med mappnings data flöden i Azure Data Factory
description: Lär dig hur du bearbetar textfiler med fast längd i Azure Data Factory att använda mappnings data flöden.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210681"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>Bearbeta textfiler med fast längd med Data Factory mappnings data flöden

Data Factory mappning av data flöden stöder omvandlings data från textfiler med fast bredd. Du definierar en data uppsättning för en textfil utan avgränsare och konfigurerar sedan del Strängs delningar baserat på ordnings position.

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Gå till **+ ny pipeline** för att starta en ny pipeline

2. Lägg till en data flödes aktivitet som används för att bearbeta filer med fast bredd

  ![Pipeline för fast bredd](media/data-flow/fwpipe.png)

3. I data flödes aktiviteten väljer du nytt data flöde för mappning

4. Lägg till en omvandling för käll omvandling, härledd kolumn, markering och mottagare

  ![Data flöde med fast bredd](media/data-flow/fw2.png)

5. Konfigurera käll omvandlingen så att den använder en ny data uppsättning som är av typen avgränsad text

6. Ange ingen kolumn avgränsare och inga rubriker

Vi håller helt enkelt att ange start punkter och längd för fältet för fil innehållet:

```
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
1234567813572468
```

7. På fliken projektion i käll omvandlingen bör du se en sträng kolumn med namnet "Column_1"

8. Skapa en ny kolumn nu i den härledda kolumnen

9. Vi ger kolumnerna enkla namn som col1

10. I uttrycks verktyget skriver du sedan:

  ```substring(Column_1,1,4)```

  ![härledd kolumn](media/data-flow/fwderivedcol1.png)

10. Upprepa detta för alla kolumner du behöver parsa

12. Klicka på fliken Granska för att se de nya kolumner som ska skapas

  ![allmänt](media/data-flow/fwinspect.png)

13. Använd Välj transformering för att ta bort alla kolumner som du inte behöver för omvandling

  ![Välj omvandling](media/data-flow/fwselect.png)

14. Använd slutligen Sink för att mata ut data till en mapp:

  ![mottagare med fast bredd](media/data-flow/fwsink.png)

  Så här kommer resultatet att se ut:

  ![utdata med fast bredd](media/data-flow/fxdoutput.png)

  Data med fast bredd delas nu med fyra tecken och tilldelas till Col1, Col2, Col3, Col4,... Baserat på ovanstående exempel är det att dela data i 4 kolumner

## <a name="next-steps"></a>Nästa steg

* Skapa resten av data flödes logiken med hjälp av [omvandling](concepts-data-flow-overview.md) av data flödes omvandlingar
