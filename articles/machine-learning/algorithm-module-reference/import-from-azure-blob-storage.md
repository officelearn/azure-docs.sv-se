---
title: 'Importera från Azure Blob Storage: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig det här avsnittet beskriver hur du använder modulen importera från Azure Blob Storage i Azure Machine Learning-tjänsten för att läsa data från Azure Blob Storage, så att du kan använda data i ett maskin inlärnings experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fea64070c496379351bb75f2a38aba9b4db70dcd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128724"
---
# <a name="import-from-azure-blob-storage-module"></a>Importera från Azure Blob Storage-modulen

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att läsa data från Azure Blob Storage, så att du kan använda dem i ett maskin inlärnings experiment.  

Azure Blob service är för att lagra stora mängder data, inklusive binära data. Azure-blobbar kan nås från var som helst, genom att använda antingen HTTP eller HTTPS. Autentisering kan krävas beroende på typen av Blob Storage. 

- Offentliga blobbar kan nås av vem som helst, eller av användare som har en SAS-URL.
- Privata blobbar kräver inloggning och autentiseringsuppgifter.

Import från Blob Storage kräver att data lagras i blobbar som använder **Block-Blob** -format. Filerna som lagras i blobben måste använda antingen kommaavgränsade (CSV) eller tabbavgränsade format (TSV). När du läser filen läses posterna och alla tillämpliga attribut in som rader i minnet som en data uppsättning.


Vi rekommenderar starkt att du profilerar dina data innan du importerar, för att se till att schemat är som förväntat. Import processen söker igenom ett antal huvud rader för att fastställa schemat, men senare rader kan innehålla extra kolumner eller data som orsakar fel.



## <a name="manually-set-properties-in-the-import-data-module"></a>Ange egenskaper manuellt i modulen importera data

Följande steg beskriver hur du konfigurerar import källan manuellt.

1. Lägg till modulen **Importera data** i experimentet. Du hittar den här modulen i gränssnittet i **data indata och utdata**

2. För **data källa**väljer du **Azure Blob Storage**.

3. För **Autentiseringstyp**väljer du **offentlig (SAS-URL)** om du vet att informationen har angetts som en offentlig data källa. En SAS-URL är en tidsbegränsad URL för offentlig åtkomst som du kan skapa med hjälp av ett Azure Storage-verktyg.

    Annars väljer du **konto**.

4. Om dina data finns i en **offentlig** blob som kan nås med hjälp av en SAS-URL behöver du inte ytterligare AUTENTISERINGSUPPGIFTER eftersom URL-strängen innehåller all information som behövs för nedladdning och autentisering.

    I fältet **URI** skriver eller klistrar du in den fullständiga URI som definierar kontot och den offentliga blobben.



5. Om dina data finns i ett **privat** konto måste du ange autentiseringsuppgifter, inklusive konto namnet och nyckeln.

    - För **konto namn**skriver eller klistrar du in namnet på det konto som innehåller den blob som du vill få åtkomst till.

        Till exempel, om den fullständiga URL: en för lagrings `http://myshared.blob.core.windows.net`kontot är, skriver `myshared`du.

    - För **konto nyckel**klistrar du in den lagrings åtkomst nyckel som är kopplad till kontot.

        Om du inte känner till åtkomst nyckeln kan du läsa avsnittet "hantera dina Azure Storage-konton" i den här artikeln: [Om Azure Storage-konton](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. För **sökväg till behållare, katalog eller BLOB**skriver du namnet på den angivna blobben som du vill hämta.

    Om du till exempel har laddat upp en fil med namnet **data01. csv** till behållaren **trainingdata** i ett konto med namnet **mymldata**skulle den fullständiga URL: en för `http://mymldata.blob.core.windows.net/trainingdata/data01.txt`filen bli:.

    I fältet **sökväg till behållare, katalog eller BLOB**skulle du därför skriva:`trainingdata/data01.csv`

    Om du vill importera flera filer kan du använda jokertecken `*` (asterisk) eller `?` (frågetecken).

    Anta till exempel att containern `trainingdata` innehåller flera filer i ett kompatibelt format, du kan använda följande specifikation för att läsa alla filer som börjar med `data`och sammanfoga dem till en enda data uppsättning:

    `trainingdata/data*.csv`

    Det går inte att använda jokertecken i behållar namn. Om du behöver importera filer från flera behållare använder du en separat instans av modulen **Importera data** för varje behållare och sammanfogar sedan data uppsättningarna med modulen [Lägg till rader](./add-rows.md) .

    > [!NOTE]
    > Om du har valt alternativet **Använd cachelagrade resultat**, Utlös inte en uppdatering av data i experimentet vid alla ändringar som du gör i filerna i behållaren.

7. För **BLOB-filformat**väljer du ett alternativ som anger formatet på de data som lagras i blobben, så att Azure Machine Learning kan bearbeta data korrekt. Följande format stöds:

    - **CSV**: Kommaavgränsade värden (CSV) är standard lagrings formatet för att exportera och importera filer i Azure Machine Learning. Om data redan innehåller en rubrik rad, måste du välja alternativet, **filen har rubrik rad**eller så behandlas rubriken som en datarad.

       

    - **TSV**: Tabbavgränsade värden (TSV) är ett format som används av många Machine Learning-verktyg. Om data redan innehåller en rubrik rad, måste du välja alternativet, **filen har rubrik rad**eller så behandlas rubriken som en datarad.

       

    - **ARFF**: Det här formatet stöder import av filer i det format som används av Wekas-verktyg. 

   

8. Kör experimentet.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 