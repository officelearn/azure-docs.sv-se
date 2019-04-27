---
title: 'Microsoft Genomics: felsökningsguide | Microsoft Docs'
titleSuffix: Azure
description: Läs mer om felsökningsmetoder
keywords: felsökning, fel, felsökning
services: microsoft-genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: article
ms.date: 10/29/2018
ms.openlocfilehash: 78084e6beac7b390b1ea1afe888030c5224856b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790512"
---
# <a name="troubleshooting-guide"></a>Felsökningsguide

Här är några felsökningstips för några vanliga problem som du kanske stöter på när du använder tjänsten Microsoft Genomics MSGEN.

 Vanliga frågor och svar, inte relaterade till felsökning, finns i [vanliga frågor](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Steg 1: Leta upp felkoder som är associerad med arbetsflödet

Du kan hitta de felmeddelanden som är associerad med arbetsflödet genom att:

1. Från kommandoraden och skriva  `msgen status`
2. Undersöker innehållet i standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Med hjälp av kommandoraden `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Det finns tre obligatoriska argument:

* URL - bas-URI för API: et
* NYCKEL - åtkomstnyckeln för ditt Genomics-konto
    * För att hitta din URL och nyckel, gå till Azure-portalen och öppna sidan Microsoft Genomics-konto. Under den **Management** rubrik, Välj **åtkomstnycklar**. Där kan hitta du både API-URL och dina åtkomstnycklar.

  
* ID - arbetsflödes-ID
    * Du hittar ditt arbetsflöde ID-typen i `msgen list` kommando. Om vi antar att din konfigurationsfilen innehåller URL: en och dina åtkomstnycklar och om det finns är på samma plats som din msgen exe kommandot ser ut så här: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Utdata från det här kommandot ser ut så här:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Du kan också inkludera sökvägen till konfigurationsfilen istället för direkt att ange URL och nyckel. Om du använder de här argumenten i kommandoraden samt konfigurationsfilen blir har kommandoradsargument som högre prioritet.  

För arbetsflöde-ID 1001 och config.txt-fil som placeras i samma sökväg som msgen körbara startar ser kommandot ut så här:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.  Granska innehållet i standardoutput.txt 
Leta upp utdatabehållaren för arbetsflödet i fråga. MSGEN skapar en, `[workflowfilename].logs.zip` mappen efter varje arbetsflödeskörning. Packa upp mappen om du vill visa dess innehåll:

* outputFileList.txt – en lista över de utdatafiler som skapas under arbetsflödet
* StandardError.txt - filen är tom.
* StandardOutput.txt – loggar alla översta statusmeddelanden, inklusive fel som uppstod vid körning av arbetsflödet.
* GATK loggfiler – alla andra filer i den `logs` mapp

Granska innehållet i standardoutput.txt för felsökning, och notera eventuella felmeddelanden som visas.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Steg 2: Testa rekommenderade åtgärder för vanliga fel

Det här avsnittet beskriver kortfattat vanliga fel utdata genom Microsoft Genomics-tjänsten (msgen) och de strategier som du kan använda för att lösa dem. 

Microsoft Genomics-tjänsten (msgen) kan ge följande två typer av fel:

1. Internt fel: Fel som är interna för tjänsten, som inte kan lösas genom att åtgärda parametrar eller indatafiler. Ibland skickar in igen arbetsflödet kan åtgärda dessa fel.
2. Indatafel: Fel som kan lösas genom att använda rätt argument eller åtgärda filformat.

### <a name="1-internal-service-errors"></a>1. Internt fel

Ett internt tjänstfel är inte användare som är användbara. Du kan skicka arbetsflödet men om det inte fungerar, kontakta supporten för Microsoft Genomics

| Felmeddelande                                                                                                                            | Rekommenderade steg för felsökning                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ett internt fel har inträffat. Försök att skicka igen arbetsflödet. Om du ser det här felet, kontakta Microsoft Genomics-support för hjälp | Skicka arbetsflödet igen. Kontakta Microsoft Genomics stöd för att få hjälp om problemet kvarstår genom att skapa en [biljett](file-support-ticket-genomics.md ). |

### <a name="2-input-errors"></a>2. Indatafel

De här felen är användare som är användbara. Microsoft Genomics-tjänsten utdata baserat på vilken typ av fil- och felkoden distinkta felkoder. Följ rekommenderade felsökningsstegen som anges nedan.

| Typ av fil | Felkod | Felmeddelande                                                                           | Rekommenderade steg för felsökning                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Alla          | 701        | Läs [readId] har [numberOfBases] baser, men gränsen är [maxReadLength]           | Den vanligaste orsaken till det här felet är skadade filer vilket leder till sammanslagning av två läsningar. Kontrollera dina indatafiler. |
| BAM          | 200        |   Det går inte att läsa filen [yourFileName].                                                                                       | Kontrollera formatet på BAM-fil. Skicka arbetsflödet igen med en korrekt formaterad fil.                                                                           |
| BAM          | 201        |  Det går inte att läsa BAM-fil [filnamn].                                                                                      |Kontrollera formatet på BAM-fil.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                            |
| BAM          | 202        | Det går inte att läsa BAM-fil [filnamn]. För små och saknas filhuvudet.                                                                                        | Kontrollera formatet på BAM-fil.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                            |
| BAM          | 203        |   Det går inte att läsa BAM-fil [filnamn]. Rubrik för filen är skadad.                                                                                      |Kontrollera formatet på BAM-fil.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                           |
| BAM          | 204        |    Det går inte att läsa BAM-fil [filnamn]. Rubrik för filen är skadad.                                                                                     | Kontrollera formatet på BAM-fil.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                           |
| BAM          | 205        |    Det går inte att läsa BAM-fil [filnamn]. Rubrik för filen är skadad.                                                                                     | Kontrollera formatet på BAM-fil.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                            |
| BAM          | 206        |   Det går inte att läsa BAM-fil [filnamn]. Rubrik för filen är skadad.                                                                                      | Kontrollera formatet på BAM-fil.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                            |
| BAM          | 207        |  Det går inte att läsa BAM-fil [filnamn]. Filen trunkeras nära offset [offset].                                                                                       | Kontrollera formatet på BAM-fil.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                            |
| BAM          | 208        |   Ogiltig BAM-fil. ReadID [Read_Id] har ingen sekvens i [filnamn]-filen.                                                                                      | Kontrollera formatet på BAM-fil.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                             |
| FASTQ        | 300        |  Det går inte att läsa FASTQ-filen. [Filnamn] avslutas inte med en ny rad.                                                                                     | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                           |
| FASTQ        | 301        |   Det går inte att läsa FASTQ-fil [filnamn]. FASTQ-posten är större än buffertstorleken vid förskjutningen: [_offset]                                                                                      | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                         |
| FASTQ        | 302        |     FASTQ syntaxfel. Filen [filnamn] har en tom rad.                                                                                    | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                         |
| FASTQ        | 303        |       FASTQ syntaxfel. Filen [filnamn] har ett ogiltigt från tecken vid förskjutningen: skriver du [_offset], rad: [line_type] tecken: [_char]                                                                                  | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                         |
| FASTQ        | 304      |  FASTQ syntaxfel vid readID [_ReadID].  Första läsning av batch har inte readID som slutar på /1 i filen [filnamn]                                                                                       | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                         |
| FASTQ        | 305        |  FASTQ syntaxfel vid readID [_readID]. Andra läsning av batch har inte readID som slutar på /2 i filen [filnamn]                                                                                      | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                          |
| FASTQ        | 306        |  FASTQ syntaxfel vid readID [_ReadID]. Första läsning av par har inte ett ID som slutar med /1 i filen [filnamn]                                                                                       | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                          |
| FASTQ        | 307        |   FASTQ syntaxfel vid readID [_ReadID]. ReadID tar inte slut med /1 eller / 2. Filen [filnamn] kan inte användas som en parade FASTQ-fil.                                                                                      |Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                          |
| FASTQ        | 308        |  FASTQ läsfel. Läsningar av båda ändar svarat på olika sätt. Du väljer rätt FASTQ-filer?                                                                                       | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Steg 3: Kontakta supporten för Microsoft Genomics

Om du fortsätter att ha misslyckade jobb, eller om du har andra frågor, kontakta Microsoft Genomics support från Azure-portalen. Mer information om hur du skickar en begäran kan hittas [här](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att felsöka och lösa vanliga problem med Microsoft Genomics-tjänsten. Mer information och fler vanliga frågor och svar finns [vanliga frågor](frequently-asked-questions-genomics.md). 
