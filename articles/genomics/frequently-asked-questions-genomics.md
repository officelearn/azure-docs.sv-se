---
title: Vanliga frågor och svar
titleSuffix: Microsoft Genomics
description: Få svar på vanliga frågor om att använda tjänsten Microsoft Genomics, inklusive teknisk information, SLA och fakturering.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: 4a2b66f95467e7f6cb99f632548351f827e259c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476438"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: vanliga frågor

Den här artikeln innehåller de vanligaste frågorna som du kanske har relaterat till Microsoft Genomics. Mer information om Microsoft Genomics-tjänsten finns i [Vad är Microsoft Genomics?](overview-what-is-genomics.md). Mer information om fel sökning finns i vår [fel söknings guide](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Hur gör jag för att köra GATK4-arbetsflöden på Microsoft Genomics?
I filen config. txt för Microsoft Genomics anger du den PROCESS_NAME som `gatk4`. Observera att du debiteras enligt vanliga fakturerings kostnader.


## <a name="what-is-the-sla-for-microsoft-genomics"></a>Vad är service avtalet för Microsoft Genomics?
Vi garanterar att 99,9% av tiden Microsoft Genomics tjänsten kommer att vara tillgänglig för att ta emot API-begäranden för arbets flöden. Mer information finns i [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Hur visas användningen av Microsoft Genomics på fakturan?
Microsoft Genomics räkningar baserat på antalet bearbetade gigabaser per arbets flöde. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Var hittar jag en lista över alla möjliga kommandon och argument för den `msgen` klienten?
Du kan få en fullständig lista över tillgängliga kommandon och argument genom att köra `msgen help`. Om inga ytterligare argument anges visas en lista över tillgängliga hjälp avsnitt, en för var och en av `submit`, `list`, `cancel`och `status`. Om du vill ha hjälp med ett speciellt kommando skriver du `msgen help command`; `msgen help submit` visar till exempel alla sändnings alternativ.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Vilka är de vanligaste kommandona för den `msgen` klienten?
De vanligaste kommandona är argument för den `msgen` klienten är: 

 |**Kommando**          |  **Fältbeskrivning** |
 |:--------------------|:-------------         |
 |`list`               |Returnerar en lista med jobb som du har skickat. För argument, se `msgen help list`.  |
 |`submit`             |Skickar en arbets flödes förfrågan till tjänsten. För argument, se `msgen help submit`.|
 |`status`             |Returnerar statusen för det arbets flöde som anges av `--workflow-id`. Se även `msgen help status`. |
 |`cancel`             |Skickar en begäran om att avbryta bearbetningen av arbets flödet som anges av `--workflow-id`. Se även `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Var får jag värdet för `--api-url-base`?
Gå till Azure Portal och öppna ditt Genomiks konto sida. Under **hanterings** rubriken väljer du **åtkomst nycklar**. Där hittar du både API-URL: en och dina åtkomst nycklar.

## <a name="where-do-i-get-the-value-for---access-key"></a>Var får jag värdet för `--access-key`?
Gå till Azure Portal och öppna ditt Genomiks konto sida. Under **hanterings** rubriken väljer du **åtkomst nycklar**. Där hittar du både API-URL: en och dina åtkomst nycklar.

## <a name="why-do-i-need-two-access-keys"></a>Varför behöver jag två åtkomst nycklar?
Du behöver två åtkomst nycklar om du vill uppdatera (återskapa) dem utan att avbryta användningen av tjänsten. Om du till exempel vill uppdatera den första nyckeln bör du ha alla nya arbets flöden som använder den andra nyckeln. Vänta sedan tills alla arbets flöden använder den första nyckeln för att slutföra innan du uppdaterar den första nyckeln.

## <a name="do-you-save-my-storage-account-keys"></a>Sparar du mina lagrings konto nycklar?
Din lagrings konto nyckel används för att skapa kortsiktiga åtkomsttoken för Microsoft Genomics tjänsten för att läsa dina indatafiler och skriva utdatafilerna. Standard varaktigheten för token är 48 timmar. Varaktigheten för token kan ändras med alternativet `-sas/--sas-duration` för kommandot Skicka. värdet anges i timmar.

## <a name="what-genome-references-can-i-use"></a>Vilka användar-och användar referenser kan jag använda?

Följande referenser stöds:

 |Referens              | Värde för `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (ingen Alt-analys) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Hur gör jag för att formatera mina kommando rads argument som en konfigurations fil? 

msgen förstår konfigurationsfilerna i följande format:
* Alla alternativ anges som nyckel/värde-par med värden åtskilda från nycklar med kolon.
  Blank steg ignoreras.
* Rader som börjar med `#` ignoreras.
* Alla kommando rads argument i långt format kan konverteras till en nyckel genom att de inledande strecken raderas och ersätts med streck mellan ord med under streck. Här följer några exempel på konvertering:

  |Kommando rads argument            | Konfigurations fil rad |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base: https://url*    |
  |`-k/--access-key KEY`            | *access_key: nyckel*              |      
  |`-pa/--process-args R=B37m1`     | *process_args: R-b37m1*        |  

## <a name="next-steps"></a>Nästa steg

Använd följande resurser för att komma igång med Microsoft Genomics:
- Kom igång genom att köra ditt första arbets flöde via Microsoft Genomicss tjänsten. [Köra ett arbets flöde via Microsoft Genomics tjänsten](quickstart-run-genomics-workflow-portal.md)
- Skicka in dina egna data för bearbetning av tjänsten Microsoft Genomics: [parad FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [flera fastq eller BAM](quickstart-input-multiple.md) 

