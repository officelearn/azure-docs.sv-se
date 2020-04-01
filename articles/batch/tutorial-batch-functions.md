---
title: Utlösa ett batchjobb med Azure Functions
description: Självstudiekurs - Använd OCR på skannade dokument när de läggs till i en lagringsblob
author: LauraBrenner
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: a967fdc14b85f294ee11cbcc57a8d2280dba38e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017198"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Självstudiekurs: Utlösa ett batchjobb med Azure-funktioner

I den här självstudien får du lära dig hur du utlöser ett batchjobb med Azure Functions. Vi går igenom ett exempel där dokument som läggs till i en Azure Storage-blob-behållare har optisk teckenigenkänning (OCR) som tillämpas på dem via Azure Batch. För att effektivisera OCR-bearbetningen konfigurerar vi en Azure-funktion som kör ett Batch OCR-jobb varje gång en fil läggs till i blob-behållaren.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har ett, skapa ett [gratis konto](https://azure.microsoft.com/free/) innan du börjar.
* Ett Azure Batch-konto och ett länkat Azure Storage-konto. Se [Skapa ett batchkonto](quick-create-portal.md#create-a-batch-account) för mer information om hur du skapar och länkar konton.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Utforskaren för Azure Storage](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Skapa en batchpool och batch-jobb med Batch Explorer

I det här avsnittet ska du använda Batch Explorer för att skapa batchpoolen och batch-jobbet som ska köra OCR-uppgifter. 

### <a name="create-a-pool"></a>Skapa en pool

1. Logga in på Batch Explorer med dina Azure-autentiseringsuppgifter.
1. Skapa en pool genom att välja **Pooler** i det vänstra sidofältet och sedan knappen **Lägg till** ovanför sökformuläret. 
    1. Välj ett ID och visningsnamn. Vi använder `ocr-pool` för det här exemplet.
    1. Ange skalningstypen till **Fast storlek**och ange antalet dedikerade noder till 3.
    1. Välj **Ubuntu 18.04-LTS** som operativsystem.
    1. Välj `Standard_f2s_v2` som storlek på den virtuella datorn.
    1. Aktivera startuppgiften och `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`lägg till kommandot . Var noga med att ange användaridentiteten som **uppgiftsstandardanvändare (Admin)**, vilket gör att startuppgifter kan innehålla kommandon med `sudo`.
    1. Välj **OK**.
### <a name="create-a-job"></a>Skapa ett jobb

1. Skapa ett jobb i poolen genom att välja **Jobb** i det vänstra sidofältet och sedan knappen **Lägg till** ovanför sökformuläret. 
    1. Välj ett ID och visningsnamn. Vi använder `ocr-job` för det här exemplet.
    1. Ställ in `ocr-pool`poolen på , eller vilket namn du väljer för din pool.
    1. Välj **OK**.


## <a name="create-blob-containers"></a>Skapa blob-behållare

Här ska du skapa blob-behållare som lagrar dina indata- och utdatafiler för OCR Batch-jobbet.

1. Logga in i Storage Explorer med dina Azure-autentiseringsuppgifter.
1. Med hjälp av lagringskontot som är länkat till ditt Batch-konto skapar du två blob-behållare (en för indatafiler, en för utdatafiler) genom att följa stegen i [Skapa en blob-behållare](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

I det här exemplet namnges `input` indatabehållaren och det är där alla dokument utan OCR ursprungligen överförs för bearbetning. Utdatabehållaren `output` namnges och är där batch-jobbet skriver bearbetade dokument med OCR.  
    * I det här exemplet anropar `input`vi vår indatabehållare och vår utdatabehållare `output`.  
    * Indatabehållaren är där alla dokument utan OCR ursprungligen överförs.  
    * Utdatabehållaren är där batch-jobbet skriver dokument med OCR.  

Skapa en signatur för delad åtkomst för utdatabehållaren i Storage Explorer. Gör detta genom att högerklicka på utdatabehållaren och välja **Hämta signatur för delad åtkomst...**. Kontrollera **Skriv**under **Behörigheter**. Inga andra behörigheter är nödvändiga.  

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

I det här avsnittet ska du skapa Azure-funktionen som utlöser OCR Batch-jobbet när en fil överförs till din indatabehållare.

1. Följ stegen i [Skapa en funktion som utlöses av Azure Blob-lagring](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) för att skapa en funktion.
    1. När du uppmanas att ange ett lagringskonto använder du samma lagringskonto som du länkade till ditt Batch-konto.
    1. För **runtime stack**väljer du .NET. Vi skriver vår funktion i C# för att utnyttja batchen .NET SDK.
1. När blob-utlöst funktion har skapats [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) använder du och från GitHub i funktionen.
    * `run.csx`körs när en ny blob läggs till i din indatablobbbehållare.
    * `function.proj`visar en lista över externa bibliotek i funktionskoden, till exempel Batch .NET SDK.
1. Ändra platshållarvärdena för variablerna i filens `Run()` funktion så att de `run.csx` återspeglar dina batch- och lagringsautentiseringsuppgifter. Du hittar dina batch- och lagringskontouppgifter i Azure-portalen i avsnittet **Nycklar** i ditt batchkonto.
    * Hämta dina batch- och lagringskontouppgifter i Azure-portalen i avsnittet **Nycklar** i ditt batchkonto. 

## <a name="trigger-the-function-and-retrieve-results"></a>Utlösa funktionen och hämta resultat

Ladda upp någon eller alla [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) skannade filer från katalogen på GitHub till inmatningsbehållaren. Övervaka Batch Explorer för att bekräfta `ocr-pool` att en uppgift läggs till för varje fil. Efter några sekunder läggs filen med OCR till i utdatabehållaren. Filen är sedan synlig och kan hämtas på Storage Explorer.

Dessutom kan du titta på loggfilen längst ned i webbredigerarfönstret i Azure Functions, där du ser meddelanden som detta för varje fil som du laddar upp till din inmatningsbehållare:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Om du vill hämta utdatafilerna från Storage Explorer till den lokala datorn markerar du först de filer du vill använda och väljer sedan **Hämta** i det övre menyfliksområdet. 

> [!TIP]
> De nedladdade filerna är sökbara om de öppnas i en PDF-läsare.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att: 

> [!div class="checklist"]
> * Använda Batch Explorer för att skapa pooler och jobb
> * Använda Storage Explorer för att skapa blob-behållare och en signatur för delad åtkomst (SAS)
> * Skapa en blob-utlöst Azure-funktion
> * ladda upp indatafiler till Storage
> * övervaka körningen av uppgiften
> * hämta utdatafilerna.

* Fler exempel på hur du använder .NET API för att schemalägga och bearbeta batcharbetsbelastningar finns [i exemplen på GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Mer information om Azure Functions-utlösare som du kan använda för att köra batcharbetsbelastningar finns [i Azure Functions-dokumentationen](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
