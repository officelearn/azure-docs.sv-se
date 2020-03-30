---
title: Felsökningsguide
titleSuffix: Microsoft Genomics
description: Läs mer om felsökningsstrategier för att använda Microsoft Genomics, inklusive felmeddelanden och hur du löser dem.
keywords: felsökning, fel, felsökning
services: genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: f6ef56e4188a7541036db096e4ab35a1b95fc141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73486009"
---
# <a name="troubleshooting-guide"></a>Felsökningsguide

Här är några felsökningstips för några av de vanliga problem som du kan ställas inför när du använder Microsoft Genomics-tjänsten, MSGEN.

 Vanliga frågor och svar, som inte är relaterade till felsökning, finns i [Vanliga frågor](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Steg 1: Hitta felkoder som är associerade med arbetsflödet

Du kan hitta de felmeddelanden som är associerade med arbetsflödet genom att:

1. Använda kommandoraden och skriva in`msgen status`
2. Undersöker innehållet i standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Använda kommandoraden`msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Det finns tre argument som krävs:

* URL - bas-URI för API
* KEY - åtkomstnyckeln för ditt Genomikkonto
    * Om du vill hitta webbadressen och nyckeln går du till Azure-portalen och öppnar din Microsoft Genomics-kontosida. Välj **Access-nycklar**under rubriken **Hantering** . Där hittar du både API-URL:en och dina åtkomstnycklar.

  
* ID - arbetsflödes-ID
    * Så här hittar du `msgen list` kommandot arbetsflödes-ID. Förutsatt att din config-fil innehåller URL:en och dina åtkomstnycklar, och ligger på samma plats som msgen exe, kommer kommandot att se ut så här: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Utdata från det här kommandot kommer att se ut så här:
        
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
  >  Du kan också inkludera sökvägen till konfigurationsfilen i stället för att direkt ange URL:en och NYCKELN. Om du tar med dessa argument på kommandoraden och konfigurationsfilen har kommandoradsargumenten företräde.  

För arbetsflödes-ID 1001 och config.txt-fil som placeras i samma sökväg som msgen körbar, kommer kommandot att se ut så här:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. Undersök innehållet i standardoutput.txt 
Leta reda på utdatabehållaren för arbetsflödet i fråga. MSGEN skapar en `[workflowfilename].logs.zip` mapp efter varje arbetsflödeskörning. Packa upp mappen för att visa dess innehåll:

* outputFileList.txt - en lista över utdatafiler som produceras under arbetsflödet
* standarderror.txt - den här filen är tom.
* standardoutput.txt - loggar alla statusmeddelanden på den högsta nivån, inklusive fel, som inträffade när arbetsflödet kördes.
* GATK-loggfiler - alla `logs` andra filer i mappen

Om du vill felsöka undersöker du innehållet i standardoutput.txt och noterar eventuella felmeddelanden som visas.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Steg 2: Prova rekommenderade steg för vanliga fel

I det här avsnittet beskrivs kortfattat vanliga fel från Microsoft Genomics Service (msgen) och de strategier som du kan använda för att lösa dem. 

Microsoft Genomics-tjänsten (msgen) kan kasta följande två typer av fel:

1. Interna tjänstfel: Fel som är interna för tjänsten, som kanske inte löses genom att åtgärda parametrar eller indatafiler. Ibland kan du lösa dessa fel genom att skicka in arbetsflödet igen.
2. Indatafel: Fel som kan lösas med rätt argument eller fastställande av filformat.

### <a name="1-internal-service-errors"></a>1. Fel i intern tjänst

Ett internt tjänstfel kan inte vara användarens åtgärdsbara. Du kan skicka arbetsflödet igen, men om det inte fungerar kontaktar du Microsoft Genomics support

| Felmeddelande                                                                                                                            | Rekommenderade steg för felsökning                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ett internt fel uppstod. Försök att skicka arbetsflödet igen. Om felet visas igen kontaktar du Microsoft Genomics support för att få hjälp | Skicka arbetsflödet igen. Kontakta Microsoft Genomics support för hjälp om problemet kvarstår genom att skapa en [supportbiljett](file-support-ticket-genomics.md ). |

### <a name="2-input-errors"></a>2. Indatafel

Dessa fel är användarens funktionsbara. Baserat på typen av fil och felkod, microsoft Genomics tjänsten utger olika felkoder. Följ de rekommenderade felsökningsstegen nedan.

| Typ av fil | Felkod | Felmeddelande                                                                           | Rekommenderade steg för felsökning                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Alla          | 701        | Read [readId] har [numberOfBases] baser, men gränsen är [maxReadLength]           | Den vanligaste orsaken till det här felet är filkorruption som leder till sammanfogning av två läsningar. Kontrollera dina indatafiler. |
| Bam          | 200        |   Det gick inte att läsa filen "[dittfilnamn]".                                                                                       | Kontrollera formatet på BAM-filen. Skicka arbetsflödet igen med en korrekt formaterad fil.                                                                           |
| Bam          | 201        |  Det gick inte att läsa BAM-filen [File_name].                                                                                      |Kontrollera formatet på BAM-filen.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                            |
| Bam          | 202        | Det gick inte att läsa BAM-filen [File_name]. Filen är för liten och saknas rubrik.                                                                                        | Kontrollera formatet på BAM-filen.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                            |
| Bam          | 203        |   Det gick inte att läsa BAM-filen [File_name]. Filhuvudet var skadat.                                                                                      |Kontrollera formatet på BAM-filen.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                           |
| Bam          | 204        |    Det gick inte att läsa BAM-filen [File_name]. Filhuvudet var skadat.                                                                                     | Kontrollera formatet på BAM-filen.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                           |
| Bam          | 205        |    Det gick inte att läsa BAM-filen [File_name]. Filhuvudet var skadat.                                                                                     | Kontrollera formatet på BAM-filen.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                            |
| Bam          | 206        |   Det gick inte att läsa BAM-filen [File_name]. Filhuvudet var skadat.                                                                                      | Kontrollera formatet på BAM-filen.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                            |
| Bam          | 207        |  Det gick inte att läsa BAM-filen [File_name]. Filen trunkeras nära offset [offset].                                                                                       | Kontrollera formatet på BAM-filen.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                            |
| Bam          | 208        |   Ogiltig BAM-fil. ReadID [Read_Id] har ingen sekvens i filen [File_name].                                                                                      | Kontrollera formatet på BAM-filen.  Skicka arbetsflödet med en korrekt formaterad fil.                                                                             |
| FASTQ (FASTQ)        | 300        |  Det gick inte att läsa FASTQ-filen. [File_name] slutar inte med en nylinje.                                                                                     | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                           |
| FASTQ (FASTQ)        | 301        |   Det gick inte att läsa FASTQ-filen [File_name]. FASTQ-posten är större än buffertstorlek vid förskjutning: [_offset]                                                                                      | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                         |
| FASTQ (FASTQ)        | 302        |     FASTQ Syntax fel. Filen [File_name] har en tom rad.                                                                                    | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                         |
| FASTQ (FASTQ)        | 303        |       FASTQ Syntax fel. Filen[File_name] har ett ogiltigt starttecken vid förskjutningen: [_offset], radtyp: [line_type], tecken: [_char]                                                                                  | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                         |
| FASTQ (FASTQ)        | 304      |  FASTQ Syntax-fel vid readID [_ReadID].  Första läsning av batch har inte readID slutar på / 1 i filen [File_name]                                                                                       | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                         |
| FASTQ (FASTQ)        | 305        |  FASTQ Syntax-fel vid readID [_readID]. Andra läsning av batch har inte readID slutar på / 2 i filen [File_name]                                                                                      | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                          |
| FASTQ (FASTQ)        | 306        |  FASTQ Syntax-fel vid readID [_ReadID]. Första läsning av par har inte ett ID som slutar i /1 i filen [File_name]                                                                                       | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                          |
| FASTQ (FASTQ)        | 307        |   FASTQ Syntax-fel vid readID [_ReadID]. ReadID slutar inte med /1 eller/2. Filen [File_name] kan inte användas som en parkopplad FASTQ-fil.                                                                                      |Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                          |
| FASTQ (FASTQ)        | 308        |  FASTQ läsfel. Läsningar av båda ändar svarade annorlunda. Valde du rätt FASTQ-filer?                                                                                       | Korrigera formatet på FASTQ-filen och skicka arbetsflödet igen.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Steg 3: Kontakta Microsofts genomikstöd

Om du fortsätter att ha jobbfel, eller om du har några andra frågor, kontaktar du Microsoft Genomics support från Azure-portalen. Ytterligare information om hur du skickar en supportbegäran hittar du [här](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du felsöker och löser vanliga problem med Microsoft Genomics-tjänsten. Mer information och mer allmänna vanliga frågor finns i [Vanliga frågor](frequently-asked-questions-genomics.md). 
