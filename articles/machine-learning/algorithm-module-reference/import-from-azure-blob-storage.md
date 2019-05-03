---
title: 'Import från Azure Blob Storage: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Läs det här avsnittet beskrivs hur du använder Import från Azure Blob Storage-modulen i Azure Machine Learning-tjänsten för att läsa data från Azure blob storage, så att du kan använda data i en machine learning-experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029692"
---
# <a name="import-from-azure-blob-storage-module"></a>Importera från Azure Blob Storage-modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att läsa data från Azure blob storage, så att du kan använda data i en machine learning-experiment.  

Azure Blob-tjänsten är för att lagra stora mängder data, inklusive binära data. Azure-blobar kan nås från var som helst, med hjälp av antingen HTTP eller HTTPS. Autentisering kan krävas beroende på vilken typ av blob-lagring. 

- Offentliga blobar kan nås av alla eller av användare som har en SAS-URL.
- Privata blobar kräver en inloggning och autentiseringsuppgifter.

Importera från blob storage kräver att data lagras i BLOB-objekt som använder den **blockblob** format. Filer som lagras i blob måste använda .csv-fil (CSV) eller tabbavgränsade (TSV)-format. När du läser filen posterna och tillämpliga attribut rubriker har lästs in som rader i minnet som en datauppsättning.


Vi rekommenderar starkt att du Profilerar dina data innan du importerar, för att se till att schemat är som förväntat. Importen genomsöker vissa antal head rader att fastställa schemat, men senare rader kan innehålla extra kolumner eller data som orsakar fel.



## <a name="manually-set-properties-in-the-import-data-module"></a>Manuellt ange egenskaper i modulen importera Data

Följande steg beskriver hur du konfigurerar manuellt importera källa.

1. Lägg till den **importdata** modulen i experimentet. Du hittar den här modulen i gränssnittet, i den **Data indata och utdata**

2. För **datakälla**väljer **Azure Blob Storage**.

3. För **autentiseringstyp**, Välj **offentliga (SAS URL)** om du vet att informationen har angetts som en allmän datakälla. En SAS-URL är en Tidsbundna URL för offentlig åtkomst som du kan skapa med hjälp av ett Azure storage-verktyg.

    Annars väljer **konto**.

4. Om dina data finns i en **offentliga** blob som kan nås med hjälp av en SAS-URL du behöver inte ytterligare autentiseringsuppgifter eftersom URL-strängen innehåller all information som behövs för att ladda ned och autentisering.

    I den **URI** fältet, Skriv eller klistra in den fullständiga URI som definierar konton och den offentliga blobben.



5. Om dina data finns i en **privata** konto, måste du ange autentiseringsuppgifter för inklusive kontonamnet och nyckeln.

    - För **kontonamn**, skriver eller klistrar du in namnet på det konto som innehåller den blob som du vill komma åt.

        Exempel: om en fullständig URL för storage-konto är `http://myshared.blob.core.windows.net`, skriver du `myshared`.

    - För **kontonyckel**, klistra in lagringsåtkomstnyckel som är associerat med kontot.

        Om du inte vet att snabbtangent, finns i avsnittet, ”hantera dina Azure storage-konton” i den här artikeln: [Om Azure Storage-konton](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. För **sökvägen till behållaren, katalogen eller blob**, skriver du namnet på den specifika blob som du vill hämta.

    Exempel: Om du har överfört en fil med namnet **data01.csv** till behållaren **trainingdata** i ett konto med namnet **mymldata**, full-URL: en för filen: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` .

    Därför i fältet **sökvägen till behållaren, katalogen eller blob**, skriver du: `trainingdata/data01.csv`

    Om du vill importera flera filer, kan du använda jokertecken `*` (asterisk) eller `?` (frågetecken).

    Till exempel förutsatt att behållaren `trainingdata` innehåller flera filer av ett kompatibelt format kan du använda följande specifikation kan läsa alla filer som börjar med `data`, och sammanfoga dem till en enda datauppsättning:

    `trainingdata/data*.csv`

    Du kan inte använda jokertecken i namn på behållare. Om du vill importera filer från flera behållare kan använda en separat instans av den **importera Data** -modulen för varje behållare och sedan Sammanfoga datauppsättningar med hjälp av den [Lägg till rader](./add-rows.md) modulen.

    > [!NOTE]
    > Om du har valt alternativet **Använd cachelagrade resultat**, alla ändringar du gör till filer i behållaren utlöses inte en uppdatering av data i experimentet.

7. För **Blob filformat**, Välj ett alternativ som anger formatet för data som lagras i blob, så att Azure Machine Learning kan bearbeta data på rätt sätt. Följande format stöds:

    - **CSV**: Fil med kommaavgränsade värden (CSV) är lagring standardformatet för export och import filer i Azure Machine Learning. Om data redan innehåller en rubrikrad, måste du markera alternativet **filen har rubrikrad**, eller sidhuvudet behandlas som en datarad.

       

    - **TSV**: Tabbavgränsade värden (TVS) är ett format som används av många machine learning-verktyg. Om data redan innehåller en rubrikrad, måste du markera alternativet **filen har rubrikrad**, eller sidhuvudet behandlas som en datarad.

       

    - **ARFF**: Detta format stöder import av filer i det format som används av Weka verktygsuppsättningen. 

   

8. Kör experimentet.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 