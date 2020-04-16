---
title: Bearbeta textfiler med fast längd med mappningsdataflöden i Azure Data Factory
description: Lär dig hur du bearbetar textfiler med fast längd i Azure Data Factory med hjälp av mappningsdataflöden.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d629a9031f032a77efc953311a45b55996568191
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414380"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Bearbeta textfiler med fast längd med hjälp av datafabriksmappningsdataflöden

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Genom att använda mappningsdataflöden i Microsoft Azure Data Factory kan du omvandla data från textfiler med fast bredd. I följande uppgift definierar vi en datauppsättning för en textfil utan avgränsare och ställer sedan in delsträngsdelningar baserat på ordningsposition.

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Välj **+Ny pipeline** om du vill skapa en ny pipeline.

2. Lägg till en dataflödesaktivitet som ska användas för bearbetning av filer med fast bredd:

    ![Pipeline med fast bredd](media/data-flow/fwpipe.png)

3. Välj **Nytt mappningsdataflöde**i dataflödesaktiviteten .

4. Lägg till en käll, härledd kolumn, markera och sink-omformning:

    ![Dataflöde med fast bredd](media/data-flow/fw2.png)

5. Konfigurera källomvandlingen så att den använder en ny datauppsättning, som kommer att vara av typen Avgränsad text.

6. Ange inga kolumnavgränsare eller rubriker.

   Nu ska vi ställa in startpunkter och längder för innehållet i den här filen:

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

7. På fliken **Projektion** i källomvandlingen bör du se en strängkolumn med *namnet Column_1*.

8. Skapa en ny kolumn i kolumnen Härledd.

9. Vi ger kolumnerna enkla namn som *col1*.

10. Skriv följande i uttrycksverktyget:

    ```substring(Column_1,1,4)```

    ![härledd kolumn](media/data-flow/fwderivedcol1.png)

11. Upprepa steg 10 för alla kolumner som du behöver tolka.

12. Välj fliken **Kontrollera** om du vill visa de nya kolumnerna som ska genereras:

    ![Inspektera](media/data-flow/fwinspect.png)

13. Använd select transform för att ta bort någon av de kolumner som du inte behöver för omformning:

    ![välj omformning](media/data-flow/fwselect.png)

14. Använd Sink för att mata ut data till en mapp:

    ![fast bredd diskbänk](media/data-flow/fwsink.png)

    Så här ser utdata ut:

    ![utdata med fast bredd](media/data-flow/fxdoutput.png)

  Data med fast bredd delas nu, med fyra tecken vardera och tilldelas Col1, Col2, Col3, Col4 och så vidare. Baserat på föregående exempel delas data upp i fyra kolumner.

## <a name="next-steps"></a>Nästa steg

* Skapa resten av dataflödeslogiken med hjälp av mappning av [dataflödensomvandlingar](concepts-data-flow-overview.md).
