---
title: 'Exportera data: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen exportera data i Azure Machine Learning-tjänsten för att spara resultat, mellanliggande data och arbeta data från dina experiment till moln lagrings platser utanför Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: a4fb539f4c86d27813b60964794fc1f398d3f2a4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128762"
---
# <a name="export-data-module"></a>Exportera datamodul

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att spara resultat, mellanliggande data och arbeta data från dina experiment till moln lagrings mål utanför Azure Machine Learning.

Den här modulen stöder export eller sparande av data till följande moln data tjänster:


- **Exportera till Azure Blob Storage**: Sparar data till Blob Service i Azure. Data i Blob Service kan delas offentligt eller sparas i skyddade program data lager.

  
## <a name="how-to-configure-export-data"></a>Så här konfigurerar du export data

1. Lägg till modulen **Exportera data** i experimentet i gränssnittet. Du hittar den här modulen i kategorin för **indata och utdata** .

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

    3. För privata konton väljer du **konto**och anger konto namnet och konto nyckeln, så att experimentet kan skriva till lagrings kontot.

         - **Kontonamn**: Skriv eller klistra in namnet på det konto där du vill spara data. Till exempel, om den fullständiga URL: en för lagrings `http://myshared.blob.core.windows.net`kontot är, skriver `myshared`du.

        - **Konto nyckel**: Klistra in den lagrings åtkomst nyckel som är kopplad till kontot.

        -  **Sökväg till behållare, katalog eller BLOB**: Ange namnet på bloben där exporterade data ska lagras. Om du till exempel vill spara resultatet av experimentet i en ny BLOB med **namnet results01. csv** i behållar **förutsägelserna** i ett konto med namnet **mymldata**, blir den fullständiga URL `http://mymldata.blob.core.windows.net/predictions/results01.csv`: en för blobben.

            I fältet **sökväg till behållare, katalog eller BLOB**skulle du därför ange behållare och blob-namn enligt följande:`predictions/results01.csv`

        - Om du anger namnet på en blob som inte redan finns, skapar Azure blobben.

       -  När du skriver till en befintlig BLOB kan du ange att det aktuella innehållet i blobben ska skrivas över genom att ange egenskapen, **Skriv läge för Azure Blob Storage**. Den här egenskapen anges som standard till **fel**, vilket innebär att ett fel uppstår när en befintlig BLOB-fil med samma namn hittas.


    4. För **fil format för BLOB-filen**väljer du det format som data ska lagras i.

        - **CSV**: Kommaavgränsade värden (CSV) är standard lagrings formatet. Om du vill exportera kolumn rubriker tillsammans med data väljer du alternativet, **Skriv BLOB**-huvudraden.  Mer information om det kommaavgränsade formatet som används i Azure Machine Learning finns i [konvertera till CSV](./convert-to-csv.md).

        - **TSV**: Formatet Tabbavgränsade värden (TSV) är kompatibelt med många Machine Learning-verktyg. Om du vill exportera kolumn rubriker tillsammans med data väljer du alternativet, **Skriv BLOB**-huvudraden.  

 
    5. **Använd cachelagrade resultat**: Välj det här alternativet om du vill undvika att skriva om resultatet till BLOB-filen varje gång du kör experimentet. Om det inte finns några andra ändringar i modulens parametrar skriver experimentet endast första gången modulen körs, eller när det finns ändringar i data.

    6. Kör experimentet.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 