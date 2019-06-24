---
title: Utlösa ett Batch-jobb med Azure Functions
description: Självstudie – gäller OCR skannade dokument som de läggs till en lagringsblob
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: d5a5197227ff62ca0c610e2c4e269480690d3faf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342020"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Självstudier: Utlösa ett Batch-jobb med Azure Functions

I de här självstudierna lär du dig att utlösa ett Batch-jobb med Azure Functions. Vi går igenom ett exempel där dokument som läggs till en Azure Storage blob-behållare har optisk teckenläsning (OCR) tillämpas via Azure Batch. Om du vill effektivisera OCR-bearbetning, konfigurerar vi en Azure-funktion som körs ett OCR för Batch-jobb och varje gång som en fil läggs till blob-behållaren.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Ett Azure Batch-konto och ett länkat Azure Storage-konto. Se [skapa ett Batch-konto](quick-create-portal.md#create-a-batch-account) mer information om hur du skapar och länka konton.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Skapa en Batch-pool och Batch-jobb med hjälp av Batch Explorer

I det här avsnittet ska du använda Batch Explorer för att skapa Batch-pool och Batch-jobb som ska köra OCR aktiviteterna. 

### <a name="create-a-pool"></a>Skapa en pool

1. Logga in på Batch Explorer med hjälp av dina autentiseringsuppgifter för Azure.
1. Skapa en pool genom att välja **pooler** i vänster Sidopanel och sedan den **Lägg till** ovanför formuläret. 
    1. Välj ett ID och visningsnamn. Vi använder `ocr-pool` i det här exemplet.
    1. Ange vilket skala **fast storlek**, och anger antalet dedikerade noder till 3.
    1. Välj **Ubuntu 18.04-LTS** som operativsystem.
    1. Välj `Standard_f2s_v2` som storleken på virtuella datorn.
    1. Aktivera startaktiviteten och Lägg till kommandot `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Se till att ange användaridentiteten som **uppgift standardanvändaren (Admin)** , vilket gör att startaktiviteter att inkludera kommandon med `sudo`.
    1. Välj **OK**.
### <a name="create-a-job"></a>Skapa ett jobb

1. Skapa ett jobb för poolen genom att välja **jobb** i vänster Sidopanel och sedan den **Lägg till** ovanför formuläret. 
    1. Välj ett ID och visningsnamn. Vi använder `ocr-job` i det här exemplet.
    1. Ange poolen `ocr-pool`, eller namnet har valt för din pool.
    1. Välj **OK**.


## <a name="create-blob-containers"></a>Skapa blob-behållare

Här ska du skapa blob-behållare som ska lagra dina indata och utdata-filer för OCR Batch-jobb.

1. Logga in med dina autentiseringsuppgifter för Azure Lagringsutforskaren.
1. Med hjälp av storage-konto som är kopplat till ditt Batch-konto, skapa två blob-behållare (för indatafilerna, en för utdatafilerna) genom att följa stegen i [har en blobbehållare](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

I det här exemplet heter behållaren för indata `input` och är där alla dokument utan OCR inledningsvis laddas upp för bearbetning. Utdatabehållaren heter `output` och är där Batch-jobbet skriver bearbetade dokument med OCR.  
    * I det här exemplet vi ringer vår inkommande container `input`, och våra utdatabehållaren `output`.  
    * Behållaren för indata är där alla dokument utan OCR inledningsvis laddas upp.  
    * Behållaren för utdata är där Batch-jobbet skriver dokument med OCR.  

Skapa en signatur för delad åtkomst för utdata-behållare i Storage Explorer. Gör detta genom att högerklicka på behållaren för utdata och välja **hämta signatur för delad åtkomst...** . Under **behörigheter**, kontrollera **skriva**. Inga andra behörigheter krävs.  

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

I det här avsnittet skapar du den Azure-funktion som utlöser OCR Batch-jobb när en fil har överförts till behållaren med indata.

1. Följ stegen i [skapa en funktion som utlöses av Azure Blob storage](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) att skapa en funktion.
    1. När du uppmanas ett storage-konto, använder du samma lagringskonto som du har länkat till Batch-kontot.
    1. För **körningsstack**, Välj .NET. Vi skriver vår funktion C# utnyttjar Batch .NET SDK.
1. När funktionen som utlöses av blob har skapats kan du använda den [ `run.csx` ](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) och [ `function.proj` ](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) från GitHub i funktionen.
    * `run.csx` körs när en ny blob läggs till i en indata-blob-behållare.
    * `function.proj` Listar de externa bibliotek i Funktionskoden, till exempel Batch .NET SDK.
1. Ändra platshållarvärdena för variabler i i `Run()` funktion av den `run.csx` filen så att dina autentiseringsuppgifter för Batch och storage. Du hittar dina autentiseringsuppgifter för Batch och storage i Azure-portalen i den **nycklar** i Batch-konto.
    * Hämta dina Batch- och storage-autentiseringsuppgifter i Azure-portalen i den **nycklar** i Batch-konto. 

## <a name="trigger-the-function-and-retrieve-results"></a>Utlös funktionen och hämta resultaten

Ladda upp allt eller något av de inlästa filerna från den [ `input_files` ](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) katalogen på GitHub till behållaren med indata. Övervaka Batch Explorer för att bekräfta att en uppgift läggs till `ocr-pool` för varje fil. Filen med OCR tillämpas har lagts till behållaren för utdata efter några sekunder. Filen är sedan synliga och hämtningsbara på Storage Explorer.

Dessutom kan du titta på loggarna filen längst ned i fönstret Azure Functions web-redigerare där du kan se meddelanden så här för alla filer som du överför till inkommande behållaren:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-06-21T20:02:35.129 [Information] Name of output text file: <outputTxtFile>
2019-06-21T20:02:35.130 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Du kan hämta utdatafilerna från Storage Explorer till den lokala datorn, väljer du först de filer du vill och välj sedan den **hämta** på menyfliksområdet längst upp. 

> [!TIP]
> Hämtade filer är sökbara om öppnas i en PDF-läsare.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att: 

> [!div class="checklist"]
> * Använda Batch Explorer för att skapa pooler och jobb
> * Använda Storage Explorer för att skapa blob-behållare och en signatur för delad åtkomst (SAS)
> * Skapa en Azure-blob-utlöst funktion
> * ladda upp indatafiler till Storage
> * övervaka körningen av uppgiften
> * hämta utdatafilerna.

* Fler exempel på hur du använder .NET API för att schemalägga och bearbeta Batch-arbetsbelastningar finns i [exemplen på GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Fler Azure Functions-utlösare som du kan använda för att köra Batch-arbetsbelastningar finns i [Azure Functions-dokumentationen](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
