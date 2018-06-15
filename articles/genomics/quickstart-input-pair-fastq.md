---
title: 'Snabbstart: Skicka ett arbetsflöde med flera FASTQ-filer som indata | Microsoft Docs'
titleSuffix: Azure
description: I den här snabbstarten förutsätts det att du har klienten msgen installerad och har lyckats köra provdata genom tjänsten.
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: f093397803f21c023a2c32e42709ecfcd0e3aec7
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
ms.locfileid: "26684491"
---
# <a name="submit-a-workflow-using-fastq-file-inputs"></a>Skicka ett arbetsflöde med flera FASTQ-fil som indata

I den här snabbstarten visar vi hur du skickar ett arbetsflöde till Microsoft Genomics-tjänsten om indatafilerna är ett enskilt par FASTQ-filer. I det här avsnittet förutsätts det att du redan har installerat och kört `msgen`-klienten och att du vet hur du använder Azure Storage. Om du har lyckats skickat ett arbetsflöde med tillhandahållna provdata är du redo att fortsätta med den här snabbstarten. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Förberedelse: Ladda upp FASTQ-filerna till Azure Storage
Vi antar vi att du har två filer, *reads_1.fq.gz* och *reads_2.fq.gz*, och att du har laddat upp dem till ditt lagringskonto *myaccount* i Azure som  **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/indata/reads_1<span></span>. rk<span></span>.gz<span> </span>**  och **https://<span></span>myaccount.blob.core.<span> </span>windows<span></span>.net /<span></span>indata /<span></span>reads_2.fq<span></span>.gz<span> </span>** . Du har API-URL och din åtkomstnyckel. Du vill ha utdata i **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


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
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
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
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Om du föredrar att använda en konfigurationsfil skulle den innehålla följande:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Skicka filen `config.txt` med det här anropet: `msgen submit -f config.txt`

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du laddat upp ett par FASTQ-filer Azure Storage och skickat ett arbetsflöde till Microsoft Genomics-tjänsten via `msgen` Python-klienten. Ytterligare information om att skicka arbetsflöden och andra kommandon som du kan använda med tjänsten Microsoft Genomics finns i [vanliga frågor och svar](frequently-asked-questions-genomics.md). 
