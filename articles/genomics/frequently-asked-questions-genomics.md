---
title: Vanliga frågor - Vanliga frågor
titleSuffix: Microsoft Genomics
description: Få svar på vanliga frågor som rör användning av Microsoft Genomics-tjänsten, inklusive teknisk information, SLA och fakturering.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986044"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Vanliga frågor

I den här artikeln visas de vanligaste frågorna som du kan ha relaterade till Microsoft Genomics. Mer information om Microsoft Genomics-tjänsten finns i [Vad är Microsoft Genomics?](overview-what-is-genomics.md). Mer information om felsökning finns i vår [felsökningsguide](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Hur kör jag GATK4-arbetsflöden på Microsoft Genomics?
I Microsoft Genomics-tjänstens config.txt-fil anger du `gatk4`process_name till . Observera att du kommer att debiteras till regelbundna faktureringspriser.

## <a name="how-do-i-enable-output-compression"></a>Hur aktiverar jag utdatakomprimering?
Du kan komprimera utdatavcf eller gvcf med ett valfritt argument för utdatakomprimering. Detta motsvarar att `-bgzip` köra `-tabix` följt av på vcf eller `.gz` gvcf utgång, `.tbi` att producera (bgzip utgång) och (tabix utdata) filer. `bgzip`komprimerar vcf- eller gvcf-filen och `tabix` skapar ett index för den komprimerade filen. Argumentet är en boolesk, `false` som är inställd på som `true` standard för vcf-utdata, och till som standard för gcvf-utdata. Om du vill använda `-bz` på `--bgzip-output` `true` kommandoraden anger eller som `false`(kör bgzip och tabix) eller . Om du vill använda det här argumentet i `bgzip_output: true` `bgzip_output: false` filen config.txt lägger du till eller till filen.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Vad är SLA för Microsoft Genomics?
Vi garanterar att 99,9 % av tiden som Microsoft Genomics-tjänsten kommer att vara tillgänglig för att ta emot API-begäranden för arbetsflöde. Mer information finns i [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Hur visas användningen av Microsoft Genomics på min faktura?
Microsoft Genomics-räkningar baserat på antalet gigabases som bearbetas per arbetsflöde. Mer information finns i [Prissättning](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Var hittar jag en lista över alla möjliga `msgen` kommandon och argument för klienten?
Du kan få en fullständig lista över `msgen help`tillgängliga kommandon och argument genom att köra . Om inga ytterligare argument finns visas en lista över tillgängliga hjälpavsnitt, `cancel`en `status`för var och en av `submit`, `list`, och . Om du vill ha hjälp `msgen help command`med ett visst kommando skriver du ; Till exempel `msgen help submit` listar alla alternativ för att skicka.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Vilka är de vanligaste kommandona `msgen` för klienten?
De vanligaste kommandona är argument `msgen` för klienten är: 

 |**Kommandot**          |  **Fältbeskrivning** |
 |:--------------------|:-------------         |
 |`list`               |Returnerar en lista över jobb som du har skickat. Argument finns `msgen help list`i .  |
 |`submit`             |Skickar en arbetsflödesbegäran till tjänsten. Argument finns `msgen help submit`i .|
 |`status`             |Returnerar status för det `--workflow-id`arbetsflöde som anges av . Se `msgen help status`även . |
 |`cancel`             |Skickar en begäran om att avbryta `--workflow-id`bearbetningen av arbetsflödet som anges av . Se `msgen help cancel`även . |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Var får jag värdet `--api-url-base`för?
Gå till Azure-portalen och öppna din genomikkontosida. Välj **Access-nycklar**under rubriken **Hantering** . Där hittar du både API-URL:en och dina åtkomstnycklar.

## <a name="where-do-i-get-the-value-for---access-key"></a>Var får jag värdet `--access-key`för?
Gå till Azure-portalen och öppna din genomikkontosida. Välj **Access-nycklar**under rubriken **Hantering** . Där hittar du både API-URL:en och dina åtkomstnycklar.

## <a name="why-do-i-need-two-access-keys"></a>Varför behöver jag två åtkomstnycklar?
Du behöver två åtkomstnycklar om du vill uppdatera (återskapa) dem utan att avbryta användningen av tjänsten. Om du till exempel vill uppdatera den första nyckeln bör du låta alla nya arbetsflöden använda den andra nyckeln. Vänta sedan tills alla arbetsflöden som använder den första nyckeln ska slutföras innan du uppdaterar den första nyckeln.

## <a name="do-you-save-my-storage-account-keys"></a>Sparar du mina lagringskontonycklar?
Din lagringskontonyckel används för att skapa korttidsåtkomsttokens för microsoft genomik-tjänsten för att läsa dina indatafiler och skriva utdatafilerna. Standardtokenvaraktigheten är 48 timmar. Tokenvaraktigheten kan ändras med `-sas/--sas-duration` alternativet skicka kommandot; värdet är i timmar.

## <a name="what-genome-references-can-i-use"></a>Vilka arvsreferenser kan jag använda?

Dessa referenser stöds:

 |Referens              | Värdet av`-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37 (på)                    | `R=b37m1`                     |
 |hg38 (HG38)                   | `R=hg38m1`                    |      
 |hg38 (ingen alt-analys) | `R=hg38m1x`                   |  
 |hg19 (HG19)                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Hur formaterar jag mina kommandoradsargument som en config-fil? 

msgen förstår konfigurationsfiler i följande format:
* Alla alternativ anges som nyckelvärdespar med värden avgränsade från nycklar med ett kolon.
  Blanktecken ignoreras.
* Rader som `#` börjar med ignoreras.
* Alla kommandoradsargument i det långa formatet kan konverteras till en tangent genom att ta bort dess inledande streck och ersätta streck mellan ord med understreck. Här är några konverteringsexempel:

  |Kommandoradsargument            | Arkivrad för konfiguration |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key:NYCKEL*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Nästa steg

Använd följande resurser för att komma igång med Microsoft Genomics:
- Kom igång genom att köra ditt första arbetsflöde via Microsoft Genomics-tjänsten. [Kör ett arbetsflöde genom Microsoft Genomics-tjänsten](quickstart-run-genomics-workflow-portal.md)
- Skicka in dina egna data för bearbetning av Microsoft Genomics-tjänsten: [parat FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ eller BAM](quickstart-input-multiple.md) 

