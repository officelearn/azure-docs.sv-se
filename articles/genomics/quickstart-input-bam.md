---
title: Skicka ett arbetsflöde med en BAM-fil indata – Microsoft Genomics
titleSuffix: Azure
description: Den här artikeln förutsätter att du har klienten msgen installerad och har kört exempeldata via tjänsten.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 329ac374edbffa65a1b654a05a4934828f25d5de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780850"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Skicka ett arbetsflöde med en BAM-fil som indata

Den här artikeln visar hur du skickar ett arbetsflöde till Microsoft Genomics-tjänsten om indatafilen är en enskild BAM-fil. I det här avsnittet förutsätts det att du redan har installerat och kört `msgen`-klienten och att du vet hur du använder Azure Storage. Om du har skickat ett arbetsflöde med tillhandahållna provdata är du redo att fortsätta med den här artikeln. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Konfigurera: Överför BAM-filen till Azure storage
Vi antar att du har en enskild BAM-fil, *reads.bam*, och du har laddat upp den till ditt lagringskonto *myaccount* i Azure som **https://<span></span>myaccount.BLOB.Core<span></span>.windows<span></span>.net<span></span>/inputs/reads<span></span>.bam<span> </span>** . Du har API-URL och din åtkomstnyckel. Du vill ha utdata i **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.



## <a name="submit-your-job-to-the-msgen-client"></a>Skicka jobbet till `msgen`-klienten 


Här är den minsta möjliga uppsättningen argument som du måste ange för `msgen`-klienten. Radbrytningar har lagts till för tydlighetens skull:

För Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


För Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Om du föredrar att använda en konfigurationsfil skulle den innehålla följande:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Skicka filen `config.txt` med det här anropet: `msgen submit -f config.txt`

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du laddat upp en BAM-fil i Azure Storage och skickat ett arbetsflöde till Microsoft Genomics-tjänsten via `msgen` Python-klienten. Ytterligare information om att skicka arbetsflöden och andra kommandon som du kan använda med tjänsten Microsoft Genomics finns i [vanliga frågor och svar](frequently-asked-questions-genomics.md). 
