---
title: 'Exportera Data: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen exportera Data i Azure Machine Learning-tjänsten för att spara resultaten, mellanliggande data och fungerande data från dina experiment i molnet lagringsmål utanför Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028327"
---
# <a name="export-data-module"></a>Exportera datamodulen

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen och spara resultaten, mellanliggande data och fungerande data från dina experiment i molnet lagringsmål utanför Azure Machine Learning.

Den här modulen stöder exporterar eller sparar dina data till data för följande molntjänster:


- **Exportera till Azure Blob Storage**: Sparar data till Blob service i Azure. Data i Blob-tjänsten kan delas offentligt eller sparas i datalager som säkra program.

  
## <a name="how-to-configure-export-data"></a>Så här konfigurerar du exportera Data

1. Lägg till den **exportera Data** modulen i experimentet i gränssnittet. Du hittar den här modulen i den **indata och utdata** kategori.

2. Ansluta **exportera Data** den modul som innehåller data du vill exportera.

3. Dubbelklicka på **exportera Data** att öppna den **egenskaper** fönstret.

4. För **datamålet**, Välj typ av lagring i molnet där du ska spara dina data. Om du ändrar det här alternativet återställs alla andra egenskaper. Så var noga med att välja det här alternativet först!

5. Är ett konto servernamnet och autentiseringsinformation sätt som krävs för att få åtkomst till det angivna lagringskontot.

    **Exportera till Azure Blob Storage** är det enda alternativet i privat förhandsversion. Nedan visar hur du ställer in modulen.
    1. Azure blob-tjänsten är för att lagra stora mängder data, inklusive binära data. Det finns två typer av blob-lagring: offentliga blobar och BLOB-objekt som kräver autentiseringsuppgifter för inloggning.

    2. För **autentiseringstyp**, Välj **offentliga (SAS)** om du vet att lagringen har stöd för åtkomst via en SAS-URL.

          En SAS-URL är en särskild typ av URL: en som kan genereras med hjälp av ett Azure storage-verktyg och är tillgänglig för endast en begränsad tid.  Den innehåller all information som krävs för autentisering och ladda ned.

        För **URI**, Skriv eller klistra in den fullständiga URI som definierar konton och den offentliga blobben.

        För filformatet stöds CSV och TSV.

    3. Privata konton, Välj **konto**, och ange kontonamnet och kontonyckeln, så att experimentet kan skriva till lagringskontot.

         - **Kontonamn**: Skriv eller klistra in namnet på kontot där du vill spara data. Exempel: om en fullständig URL för storage-konto är `http://myshared.blob.core.windows.net`, skriver du `myshared`.

        - **Kontonyckel**: Klistra in lagringsåtkomstnyckel som är associerat med kontot.

        -  **Sökvägen till behållaren, katalogen eller blob**: Skriv namnet på bloben där exporterade data kommer att lagras. Till exempel att spara resultatet av experimentet till en ny blob med namnet **results01.csv** i behållaren **förutsägelser** i ett konto med namnet **mymldata**, en fullständig URL för den BLOB skulle vara `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Därför i fältet **sökvägen till behållaren, katalogen eller blob**, anger du behållaren och blobnamn som följer: `predictions/results01.csv`

        - Om du anger namnet på en blob som inte redan finns, skapar Azure blob för dig.

       -  Vid skrivning till en befintlig blob, du kan ange att aktuella innehållet i bloben skrivs över genom att ange egenskapen **Azure blob-lagring skrivläge**. Den här egenskapen anges som standard **fel**, vilket innebär att ett fel inträffar när en befintlig blobfil med samma namn finns.


    4. För **filformat för blob-fil**, Välj det format som data ska lagras.

        - **CSV**: Fil med kommaavgränsade värden (CSV) är standardformat för lagring. Om du vill exportera kolumnrubrikerna tillsammans med data, markerar du alternativet **skriva blob rubrikrad**.  Läs mer om komma - avgränsad format används i Azure Machine Learning, [konvertera till CSV](./convert-to-csv.md).

        - **TSV**: Tabbavgränsade värden (TVS) format är kompatibelt med många machine learning-verktyg. Om du vill exportera kolumnrubrikerna tillsammans med data, markerar du alternativet **skriva blob rubrikrad**.  

 
    5. **Använd cachelagrade resultat**: Välj det här alternativet om du vill undvika att skriva om resultatet till blob-fil varje gång du kör experimentet. Om det finns inga andra ändringar i modulparametrar, experimentet skriver resultatet bara första gången modulen körs eller när det finns ändringar av data.

    6. Kör experimentet.

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 