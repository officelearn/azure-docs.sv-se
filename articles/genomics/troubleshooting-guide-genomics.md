---
title: 'Microsoft genomik: felsökningsguide för | Microsoft Docs'
titleSuffix: Azure
description: Lär dig mer om hur du felsöker strategier
keywords: Felsökning av problemet, felsökning
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/13/2018
ms.openlocfilehash: 18761c02cc423affe7b1050700e560b1f0b0594d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshooting-guide"></a>Felsökningsguide
Den här översikten beskriver strategier för att lösa vanliga problem när du använder Microsoft Genomics-tjänsten. Vanliga frågor och svar, se [vanliga frågor](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Hur jag för att kontrollera status för Mina?
Du kan kontrollera status för ditt arbetsflöde genom att anropa `msgen status` från kommandoraden, som visas. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Det finns tre argument som krävs:
* URL - bas-URI-API: t
* NYCKEL - åtkomstnyckeln för ditt konto genomik. 
* ID - arbetsflödes-ID

Hitta din URL och nyckel, gå till Azure-portalen och öppna sidan genomik konto. Under den **Management** rubrik, Välj **åtkomstnycklar**. Där kan du söka efter både API-URL och dina åtkomstnycklar.

Du kan också inkludera sökvägen till den config-fil i stället för att direkt ange URL och nyckel. Observera att om du inkluderar följande argument på kommandoraden samt konfigurationsfilen kommandoradsargument företräde. 

När du anropar `msgen status`, visas ett användarvänligt meddelande som beskriver om arbetsflödet lyckades eller ger en orsak till misslyckandet jobbet. 


## <a name="get-more-information-about-my-workflow-status"></a>Mer information om statusen för arbetsflödet

Du kan utforska loggfiler som skapas under arbetsflödet för att få mer information om varför ett jobb inte kan ha slutförts. I din utdata-behållare, bör du se en `[youroutputfilename].logs.zip` mapp.  Packa upp den här mappen, visas följande objekt:

* outputFileList.txt - en lista över utdatafilerna produceras under arbetsflödet
* StandardError.txt - filen är tom.
* StandardOutput.txt - innehåller översta loggning av arbetsflödet. 
* GATK loggfiler - alla filer i den `logs` mapp

Den `standardoutput.txt` filen är ett bra ställe att börja att avgöra varför arbetsflödet misslyckades, eftersom den innehåller mer information på låg nivå av arbetsflödet. 

## <a name="common-issues-and-how-to-resolve-them"></a>Vanliga problem och hur du löser dem.
Det här avsnittet beskrivs kortfattat vanliga problem och hur du löser dem.

### <a name="fastq-files-are-unmatched"></a>Fastq filerna är omatchade
Fastq filer bör skiljer sig bara av avslutande /1 eller /2 i exempel-identifierare. Om du av misstag har skickat omatchat FASTQ filer, kan du se följande felmeddelanden när du anropar `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

För att lösa problemet, granska om fastq-filer som skickats till arbetsflödet är faktiskt en matchande uppsättning. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Fel vid uppladdning .bam-fil. Utdata blob finns redan och Överskrivningsalternativet har angetts till False.
Om du ser följande felmeddelande, `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, den utgående mappen innehåller redan en utdatafil med samma namn.  Ta bort befintliga utdatafilen eller aktivera Överskrivningsalternativet i konfigurationsfilen. Sedan kan skicka arbetsflödet.

### <a name="when-to-contact-microsoft-genomics-support"></a>När du kontaktar Microsoft Genomics support
Om du ser följande felmeddelanden uppstod ett internt fel. 

* `Error locating input files on worker machine`
* `Process management failure`

Försök att skicka ditt arbetsflöde. Om du fortsätter att ha jobbfel, eller om du har några andra frågor, kontaktar du Microsoft genomik stöd från Azure-portalen.

![Kontakta supporten på Azure-portalen](./media/troubleshooting-guide/genomics-contact-support.png "kontakta supporten på Azure-portalen")

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du felsöker och löser vanliga problem med tjänsten Microsoft Genomics. Mer information och mer allmänna frågor finns [vanliga frågor](frequently-asked-questions-genomics.md). 