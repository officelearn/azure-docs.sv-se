---
title: "Snabbstart: Skicka ett arbetsflöde med flera indata | Microsoft Docs"
titleSuffix: Azure
description: "I den här snabbstarten förutsätts det att du har klienten msgen installerad och har lyckats köra provdata genom tjänsten."
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: d410516f807b7914e15bed1fb93ee58d3e340d1e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Skicka ett arbetsflöde med flera indata från samma prov

I den här snabbstarten visar vi hur du skickar ett arbetsflöde till Microsoft Genomics-tjänsten om indatafilen är flera FASTQ- eller BAM-filer **som kommer från samma prov**. Kom ihåg att du **inte** kan skicka in blandade FASTQ- och BAM-filer.

I det här avsnittet förutsätts det att du redan har installerat och kört `msgen`-klienten och att du vet hur du använder Azure Storage. Om du har lyckats skickat ett arbetsflöde med tillhandahållna provdata är du redo att fortsätta med den här snabbstarten. 


## <a name="multiple-bam-files"></a>Flera BAM-filer

### <a name="upload-your-input-files-to-azure-storage"></a>Ladda upp dina indatafiler till Azure Storage
Vi antar att du har flera BAM-filer som indata, *reads.bam*, *additional_reads.bam* och *yet_more_reads.bam*, och att du har laddat upp dem till ditt lagringskonto *myaccount* i Azure. Har du API-URL och din åtkomstnyckel. Du vill ha utdata i **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Skicka jobbet till `msgen`-klienten 

Du kan skicka flera BAM-filer genom att skicka deras namn till argumentet --input-blob-name-1. Observera att alla filer måste komma från samma prov, men deras inbördes ordning är inte viktig. Nedan finns exempel på inskickade filer från kommandoraden i Windows, i Unix och med en konfigurationsfil. Radbrytningar har lagts till för tydlighetens skull:


För Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
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
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
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
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Skicka filen `config.txt` med det här anropet: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Flera, parade FASTQ-filer

### <a name="upload-your-input-files-to-azure-storage"></a>Ladda upp dina indatafiler till Azure Storage
Vi antar att du har flera parade FASTQ-filer som indata, *reads_1.fq.gz* och *reads_2.fq.gz*, *additional_reads_1.fq.gz* och *additional_ reads_2.fq.GZ* och *yet_more_reads_1.fq.gz* och *yet_more_reads_2.fq.gz*. Du har laddat upp dem till ditt lagringskonto *myaccount* i Azure och du har API-URL och din åtkomstnyckel. Du vill ha utdata i **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Skicka jobbet till `msgen`-klienten 

Parade FASTQ-filer måste komma från samma prov, men de måste också behandlas tillsammans.  Ordningen på filnamnen är viktig när de skickas som argument till--input-blob-name-1 och --input-blob-name-2. 

Nedan finns exempel på inskickade filer från kommandoraden i Windows, i Unix och med en konfigurationsfil. Radbrytningar har lagts till för tydlighetens skull:


För Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
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
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
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
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Skicka filen `config.txt` med det här anropet: `msgen submit -f config.txt`

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du laddat upp flera BAM-filer eller parade FASTQ-filer till Azure Storage och skickat ett arbetsflöde till Microsoft Genomics-tjänsten via `msgen` Python-klienten. Ytterligare information om att skicka arbetsflöden och andra kommandon som du kan använda med tjänsten Microsoft Genomics finns i [vanliga frågor och svar](frequently-asked-questions-genomics.md). 