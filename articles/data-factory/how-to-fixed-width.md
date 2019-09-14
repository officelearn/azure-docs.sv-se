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
ms.openlocfilehash: 9964aaf060c43cc3e9992f515bf272011e795043
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962117"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Bearbeta textfiler med fast längd genom att använda Data Factory mappa data flöden

Genom att använda mappnings data flöden i Microsoft Azure Data Factory kan du omvandla data från textfiler med fast bredd. I följande uppgift definierar vi en data uppsättning för en textfil utan en avgränsare och konfigurerar sedan del Strängs delningar baserat på ordnings position.

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Välj **+ ny pipeline** för att skapa en ny pipeline.

2. Lägg till en data flödes aktivitet som används för att bearbeta filer med fast bredd:

    ![Pipeline för fast bredd](media/data-flow/fwpipe.png)

3. I data flödes aktiviteten väljer du **nytt data flöde för mappning**.

4. Lägg till en omvandling av källa, härledd kolumn, urval och mottagare:

    ![Data flöde med fast bredd](media/data-flow/fw2.png)

5. Konfigurera käll omvandlingen så att den använder en ny data uppsättning, vilket kommer att vara av typen avgränsad text.

6. Ange inte någon kolumn avgränsare eller rubriker.

   Nu ska vi ange start punkter och längd för fältet för filens innehåll:

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

7. På fliken **projektion** i käll omvandlingen bör du se en sträng kolumn med namnet *Column_1*.

8. Skapa en ny kolumn i den härledda kolumnen.

9. Vi ger kolumnerna enkla namn som *col1*.

10. Skriv följande i uttrycks verktyget:

    ```substring(Column_1,1,4)```

    ![Härledd kolumn](media/data-flow/fwderivedcol1.png)

11. Upprepa steg 10 för alla kolumner som du behöver parsa.

12. Välj fliken **Granska** för att se de nya kolumner som ska genereras:

    ![allmänt](media/data-flow/fwinspect.png)

13. Använd Välj transformering för att ta bort alla kolumner som du inte behöver för omvandling:

    ![Välj omvandling](media/data-flow/fwselect.png)

14. Använd Sink för att mata ut data till en mapp:

    ![mottagare med fast bredd](media/data-flow/fwsink.png)

    Så här ser utdata ut:

    ![utdata med fast bredd](media/data-flow/fxdoutput.png)

  Data med fast bredd delas nu med fyra tecken och tilldelas till Col1, Col2, Col3, Col4 och så vidare. Data delas upp i fyra kolumner baserat på föregående exempel.

## <a name="next-steps"></a>Nästa steg

* Skapa resten av data flödes logiken med hjälp av [omvandlingar](concepts-data-flow-overview.md)av data flöden.
