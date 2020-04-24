---
title: Utlös ett batch-jobb med Azure Functions
description: Självstudie – Använd OCR för skannade dokument när de läggs till i en Storage-BLOB
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: 01c3ab167239affa4d7ae94f5649d60072c3c270
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117173"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Självstudie: utlösa ett batch-jobb med Azure Functions

I den här självstudien får du lära dig hur du utlöser ett batch-jobb med hjälp av Azure Functions. Vi går igenom ett exempel där dokument som har lagts till i en Azure Storage BLOB-behållare har optisk tecken igenkänning (OCR) som tillämpas på dem via Azure Batch. För att förenkla OCR-bearbetningen konfigurerar vi en Azure-funktion som kör ett batch-OCR-jobb varje gång en fil läggs till i BLOB-behållaren.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Ett Azure Batch-konto och ett länkat Azure Storage-konto. Mer information om hur du skapar och länkar konton finns i [skapa ett batch-konto](quick-create-portal.md#create-a-batch-account) .
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Skapa en batch-pool och ett batch-jobb med Batch Explorer

I det här avsnittet ska du använda Batch Explorer för att skapa batch-poolen och batch-jobbet som ska köra OCR-aktiviteter. 

### <a name="create-a-pool"></a>Skapa en pool

1. Logga in för att Batch Explorer med dina Azure-autentiseringsuppgifter.
1. Skapa en pool genom att välja **pooler** i det vänstra fältet och sedan knappen **Lägg till** ovanför Sök formuläret. 
    1. Välj ett ID och visnings namn. Vi ska använda `ocr-pool` det här exemplet.
    1. Ange skalnings typen till **fast storlek**och ange antalet dedikerade noder till 3.
    1. Välj **Ubuntu 18,04-LTS** som operativ system.
    1. Välj `Standard_f2s_v2` som storlek på den virtuella datorn.
    1. Aktivera start uppgiften och Lägg till kommandot `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`. Se till att ange användar identiteten som **Standard användare för aktiviteten (admin)**, vilket gör att start aktiviteter kan innehålla `sudo`kommandon med.
    1. Välj **OK**.
### <a name="create-a-job"></a>Skapa ett jobb

1. Skapa ett jobb på poolen genom att välja **jobb** i det vänstra fältet och sedan knappen **Lägg till** ovanför sökformuläret. 
    1. Välj ett ID och visnings namn. Vi ska använda `ocr-job` det här exemplet.
    1. Ange poolen till `ocr-pool`eller det namn som du har valt för poolen.
    1. Välj **OK**.


## <a name="create-blob-containers"></a>Skapa BLOB-behållare

Här skapar du Blob-behållare som lagrar dina indata och utdatafiler för OCR-batchjobbet.

1. Logga in för att Storage Explorer med dina Azure-autentiseringsuppgifter.
1. Skapa två BLOB-behållare (en för indatafiler, en för utdatafiler) med hjälp av det lagrings konto som är kopplat till ditt batch-konto genom att följa stegen i [skapa en BLOB-behållare](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container).

I det här exemplet är indatafilen namngiven `input` och är där alla dokument utan OCR ursprungligen laddas upp för bearbetning. Behållaren utdata heter `output` och är där batch-jobbet skriver bearbetade dokument med OCR.  
    * I det här exemplet ska vi anropa vår container för `input`indata och vår behållare `output`för utdata.  
    * Indatafilen är där alla dokument utan OCR laddas upp första gången.  
    * Behållaren utdata är där batch-jobbet skriver dokument med OCR.  

Skapa en signatur för delad åtkomst för din utmatnings behållare i Storage Explorer. Det gör du genom att högerklicka på behållaren utdata och välja **Hämta signatur för delad åtkomst...**. Under **behörigheter**kontrollerar du **Skriv**. Inga andra behörigheter krävs.  

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

I det här avsnittet ska du skapa Azure-funktionen som utlöser batch-jobbet OCR när en fil laddas upp till din indatafil.

1. Följ stegen i [skapa en funktion som utlöses av Azure Blob Storage](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function) för att skapa en funktion.
    1. När du uppmanas att ange ett lagrings konto använder du samma lagrings konto som du länkade till ditt batch-konto.
    1. Välj .NET för **körnings stack**. Vi skriver vår funktion i C# för att utnyttja batch .NET SDK.
1. När den BLOB-utlöst funktionen har skapats använder du [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) och [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) från GitHub i funktionen.
    * `run.csx`körs när en ny BLOB läggs till i BLOB-behållaren för inflöde.
    * `function.proj`visar en lista över de externa biblioteken i funktions koden, till exempel batch .NET SDK.
1. Ändra värdena för plats hållaren för variablerna `Run()` i `run.csx` filens funktion för att återspegla dina autentiseringsuppgifter för batch och lagring. Du hittar autentiseringsuppgifterna för batch-och lagrings kontot i Azure Portal i avsnittet **nycklar** i batch-kontot.
    * Hämta autentiseringsuppgifterna för batch-och lagrings kontot i Azure Portal i avsnittet **nycklar** i batch-kontot. 

## <a name="trigger-the-function-and-retrieve-results"></a>Utlös funktionen och hämta resultat

Ladda upp några eller alla skannade filer från [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) katalogen på GitHub till din indatafil. Övervaka Batch Explorer för att bekräfta att en uppgift läggs till `ocr-pool` för varje fil. Efter några sekunder läggs filen med den OCR som används till i behållaren utdata. Filen är sedan synlig och kan hämtas på Storage Explorer.

Dessutom kan du titta på logg filen längst ned i Azure Functions Web Editor-fönstret där du ser meddelanden som detta för varje fil som du överför till din indatafil:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Om du vill ladda ned utdatafilerna från Storage Explorer till din lokala dator väljer du först de filer som du vill använda och väljer sedan **hämtningen** i det översta menyfliksområdet. 

> [!TIP]
> De hämtade filerna är sökbara om de öppnas i en PDF-läsare.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att: 

> [!div class="checklist"]
> * Använd Batch Explorer för att skapa pooler och jobb
> * Använd Storage Explorer för att skapa BLOB-behållare och en signatur för delad åtkomst (SAS)
> * Skapa en BLOB-utlöst Azure-funktion
> * ladda upp indatafiler till Storage
> * övervaka körningen av uppgiften
> * hämta utdatafilerna.

* Fler exempel på hur du använder .NET API för att schemalägga och bearbeta batch-arbetsbelastningar finns i [exemplen på GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp). 

* Om du vill se fler Azure Functions utlösare som du kan använda för att köra batch-arbetsbelastningar, se [Azure Functions-dokumentationen](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings).
