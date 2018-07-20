---
title: 'Microsoft Genomics: felsökningsguide | Microsoft Docs'
titleSuffix: Azure
description: Läs mer om felsökningsmetoder
keywords: felsökning, fel, felsökning
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: 9bd1690003fd37b6c2edd0f0421cf8d0e74f8cb5
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144184"
---
# <a name="troubleshooting-guide"></a>Felsökningsguide
Den här översikten beskrivs strategier för att lösa vanliga problem när du använder Microsoft Genomics-tjänsten. Vanliga frågor och svar, se [vanliga frågor](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Hur kontrollerar jag min jobbstatus?
Du kan kontrollera status för ditt arbetsflöde genom att anropa `msgen status` från kommandoraden, som visas. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Det finns tre obligatoriska argument:
* URL - bas-URI för API: et
* NYCKEL - åtkomstnyckeln för ditt Genomics-konto. 
* ID - arbetsflödes-ID

Gå till Azure-portalen och öppna din kontosida för Genomics för att hitta din URL och nyckel. Under den **Management** rubrik, Välj **åtkomstnycklar**. Där kan hitta du både API-URL och dina åtkomstnycklar.

Du kan också inkludera sökvägen till konfigurationsfilen istället för direkt att ange URL och nyckel. Observera att kommandoradsargument som har högre prioritet om du inkluderar de här argumenten i kommandoraden samt konfigurationsfilen. 

När du anropar `msgen status`, visas ett användarvänligt meddelande som beskriver om arbetsflödet lyckades eller ge en orsak till misslyckandet jobbet. 


## <a name="get-more-information-about-my-workflow-status"></a>Få mer information om min arbetsflödesstatus

Om du vill ha mer information om varför ett jobb inte kan ha lyckats kan utforska du loggfiler som skapas under arbetsflödet. I din utdatabehållaren bör du se en `[youroutputfilename].logs.zip` mapp.  Packat upp den här mappen, visas följande objekt:

* outputFileList.txt – en lista över de utdatafiler som skapas under arbetsflödet
* StandardError.txt - filen är tom.
* StandardOutput.txt - innehåller översta loggning av arbetsflödet. 
* GATK loggfiler – alla andra filer i den `logs` mapp

Den `standardoutput.txt` filen är ett bra ställe att börja att fastställa varför arbetsflödet lyckades inte, eftersom den innehåller mer information som på låg nivå av arbetsflödet. 

## <a name="common-issues-and-how-to-resolve-them"></a>Vanliga problem och hur du löser dem.
Det här avsnittet beskrivs kortfattat vanliga problem och hur du löser dem.

### <a name="fastq-files-are-unmatched"></a>Fastq-filer är oöverträffad
Fastq-filer bör skiljer sig bara av den avslutande /1 eller /2 i exempel-identifierare. Om du av misstag har skickat en oöverträffad FASTQ-filer, visas följande felmeddelanden visas när du anropar `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

Lös detta genom att granska om fastq-filer som skickas till arbetsflödet är faktiskt en matchande uppsättning. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Fel vid uppladdning .bam-fil. Utdatablob finns redan och Överskrivningsalternativet har angetts till False.
Om du ser ett felmeddelande, `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, den utgående mappen innehåller redan en utdatafil med samma namn.  Ta bort den befintliga utdatafilen eller aktivera Överskrivningsalternativet Skriv i konfigurationsfilen. Sedan kan skicka ditt arbetsflöde.

### <a name="when-to-contact-microsoft-genomics-support"></a>När du kontaktar Microsoft Genomics-support
Om du ser följande felmeddelanden uppstod ett internt fel. 

* `Error locating input files on worker machine`
* `Process management failure`

Försök att skicka ditt arbetsflöde. Om du fortsätter att ha misslyckade jobb, eller om du har andra frågor, kontakta Microsoft Genomics support från Azure-portalen. Mer information om hur du skickar en begäran kan hittas [här](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att felsöka och lösa vanliga problem med Microsoft Genomics-tjänsten. Mer information och fler vanliga frågor och svar finns [vanliga frågor](frequently-asked-questions-genomics.md). 
