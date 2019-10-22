---
title: 'Exportera data: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen exportera data i Azure Machine Learning-tjänsten för att spara resultat, mellanliggande data och arbeta data från dina pipelines till moln lagrings platser utanför Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b7b4b9de1e91279243e35f1b71f1ef6d2244e9e0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693720"
---
# <a name="export-data-module"></a>Exportera datamodul

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att spara resultat, mellanliggande data och arbeta data från dina pipelines till moln lagrings mål utanför Azure Machine Learning.

Den här modulen stöder export eller sparande av data till följande moln data tjänster:


- **Exportera till Azure Blob Storage**: sparar data i BLOB service i Azure. Data i Blob Service kan delas offentligt eller sparas i skyddade program data lager.

  
## <a name="how-to-configure-export-data"></a>Så här konfigurerar du export data

1. Lägg till modulen **Exportera data** till din pipeline i gränssnittet. Du hittar den här modulen i kategorin för **indata och utdata** .

2. Anslut **Exportera data** till modulen som innehåller de data som du vill exportera.

3. Dubbelklicka på **Exportera data** för att öppna fönstret **Egenskaper** .

4. För **data destination**väljer du den typ av moln lagring där du sparar dina data. Om du gör några ändringar i det här alternativet återställs alla andra egenskaper. Var noga med att välja det här alternativet först!

5. Ange ett konto namn och en autentiseringsmetod som krävs för att komma åt det angivna lagrings kontot.

    **Export till Azure Blob Storage** är det enda alternativet i privat förhands granskning. Nedan visas hur du ställer in modulen.
    1. Azure Blob service är för att lagra stora mängder data, inklusive binära data. Det finns två typer av blob-lagring: offentliga blobbar och blobbar som kräver inloggnings uppgifter.

    2. För **Autentiseringstyp**väljer du **offentlig (SAS)** om du vet att lagringen har stöd för åtkomst via en SAS-URL.

          En SAS-URL är en särskild typ av URL som kan genereras med hjälp av ett Azure Storage-verktyg och är bara tillgänglig för en begränsad tid.  Den innehåller all information som behövs för autentisering och hämtning.

        För **URI**skriver eller klistrar du in den fullständiga URI som definierar kontot och den offentliga blobben.

        För fil format stöds CSV och TSV.

    3. För privata konton väljer du **konto**och anger konto namnet och konto nyckeln, så att pipelinen kan skriva till lagrings kontot.

         - **Konto namn**: Skriv eller klistra in namnet på det konto där du vill spara data. Om den fullständiga URL: en för lagrings kontot till exempel är `http://myshared.blob.core.windows.net`, skriver du `myshared`.

        - **Konto nyckel**: klistra in den lagrings åtkomst nyckel som är kopplad till kontot.

        -  **Sökväg till behållare, katalog eller BLOB**: Ange namnet på bloben där exporterade data ska lagras. Om du till exempel vill spara resultatet från din pipeline till en ny BLOB med namnet **results01. csv** i behållar **förutsägelserna** i ett konto med namnet **mymldata**, skulle den fullständiga URL: en för blobben `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            I fältet **sökväg till behållare, katalog eller BLOB**skulle du därför ange behållare och blob-namn enligt följande: `predictions/results01.csv`

        - Om du anger namnet på en blob som inte redan finns, skapar Azure blobben.

       -  När du skriver till en befintlig BLOB kan du ange att det aktuella innehållet i blobben ska skrivas över genom att ange egenskapen, **Skriv läge för Azure Blob Storage**. Den här egenskapen anges som standard till **fel**, vilket innebär att ett fel uppstår när en befintlig BLOB-fil med samma namn hittas.


    4. För **fil format för BLOB-filen**väljer du det format som data ska lagras i.

        - **CSV**: kommaavgränsade värden (CSV) är standard lagrings formatet. Om du vill exportera kolumn rubriker tillsammans med data väljer du alternativet, **Skriv BLOB-huvudraden**.  Mer information om det kommaavgränsade formatet som används i Azure Machine Learning finns i [konvertera till CSV](./convert-to-csv.md).

        - **TSV**: formatet Tabbavgränsade värden (TSV) är kompatibelt med många Machine Learning-verktyg. Om du vill exportera kolumn rubriker tillsammans med data väljer du alternativet, **Skriv BLOB-huvudraden**.  

 
    5. **Använd cachelagrade resultat**: Välj det här alternativet om du vill undvika att skriva om resultatet till BLOB-filen varje gång du kör pipelinen. Om det inte finns några andra ändringar i modulens parametrar skriver pipelinen bara resultatet första gången modulen körs, eller när det finns ändringar i data.

    6. Köra en pipeline.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 