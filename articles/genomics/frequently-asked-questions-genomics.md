---
title: 'Microsoft Genomics: Vanliga frågor – vanliga frågor och svar | Microsoft Docs'
titleSuffix: Azure
description: Svar på vanliga frågor kunder ställa om Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 56256a6c10ecb0d06dfd6194668b9c32c5540c0e
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683908"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Vanliga frågor

Den här artikeln visar de viktigaste frågorna som du kanske har relaterat till Microsoft Genomics. Mer information om Microsoft Genomics-tjänsten finns i [vad är Microsoft Genomics?](overview-what-is-genomics.md). Mer information om felsökning finns i vår [felsökningsguiden för](troubleshooting-guide-genomics.md). 

## <a name="what-is-the-microsoft-genomics-service-gatk-4-promotion"></a>Vad är Microsoft Genomics-tjänsten GATK 4 befordran?
Fram till slutet av kalenderåret 2018 erbjuder Microsoft Genomics-tjänsten 20 WGS körs med GATK4 utan kostnad. Delta i det här erbjudandet registrera [här](https://aka.ms/msgatk4). 

### <a name="what-are-the-common-issues-i-might-encounter-while-running-the-microsoft-genomics-service-gatk4-promotion"></a>Vilka är de vanligaste problemen som jag kan uppstå när du kör Microsoft Genomics-tjänsten GATK4 befordran
Här är en lista över vanliga fel som kan uppstå och deras rekommenderad upplösning:

| **meddelande**                                                                                                                                                                                    | **Orsak**                                                                                                    | **Lösning**                                                                                                                                                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `gatk4-promo` har inte aktiverats för ditt konto. Mer information finns i https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                               | Du försöker köra GATK4 arbetsflöden med Microsoft Genomics-tjänsten utan att aktiveras.       | Besök [här](https://aka.ms/msgatk4) att aktivera ditt konto. Observera att utvärderingsversionen upphör att gälla i slutet av kalenderåret 2018. Du kommer inte att kunna aktivera ditt konto för erbjudanden körningar efter detta datum. |
| Tack för att du testar `gatk4-promo`. Utvärderingsperioden har upphört. Mer information https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                  | GATK4 utvärderingsversionen har upphört att gälla i slutet av kalenderåret och du försöker anropa den `gatk4-promo` processnamn.  | Växla processnamn parametern till `gatk4`, i stället för `gatk4-promo`. Detta är den officiella gatk4-versionen och ditt arbetsflöde kommer att debiteras om du använder den här parametern.                                         |
| Tack för att du testar `gatk4-promo` du har använt alla dina tilldelade körningar. Mer information finns i https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics | Du har skickat alla dina erbjudanden 20 körs för GATK4.                               | Skicka eventuella nya gatk4 körs med processnamn argument har angetts till `gatk4` i stället för `gatk4-promo`. Ditt arbetsflöde debiteras när du använder den här parametern.                                                          |        


## <a name="can-i-run-gatk4-workflows-on-microsoft-genomics-without-signing-up-for-the-gatk4-promotion"></a>Kan jag köra GATK4 arbetsflöden på Microsoft Genomics utan att registrera dig för att främja GATK4?
Ja, i Microsoft Genomics-tjänsten filen Config.txt, ange processnamn till `gatk4`. Observera att du kommer att debiteras med standardavgifter för fakturering och 20 kostnadsfria körs gäller inte för Microsoft Genomics-konto.



## <a name="what-is-the-sla-for-microsoft-genomics"></a>Vad är serviceavtalet för Microsoft Genomics?
Vi garanterar att 99,9% av tiden Microsoft Genomics-tjänsten kommer att kunna ta emot arbetsflöde för API-begäranden. Mer information finns i [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Hur visas användningen av Microsoft Genomics visas på fakturan?
Microsoft Genomics fakturor baserat på antalet gigabaser bearbetade per arbetsflöde. Mer information finns i [priser](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Var hittar jag en lista över alla möjliga kommandon och argumenten för de `msgen` klienten?
Du kan hämta en fullständig lista över tillgängliga kommandon och argument genom att köra `msgen help`. Om några andra argument har angett, visas en lista över tillgänglig hjälp avsnitt, en för varje `submit`, `list`, `cancel`, och `status`. Om du vill få hjälp med ett visst kommando, Skriv `msgen help command`, till exempel `msgen help submit` visar en lista över alla skicka alternativ.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Vilka är de mest ofta använda kommandon för det `msgen` klienten?
Kommandona vanligaste är argumenten för de `msgen` klienten inkluderar: 

 |**Kommando**          |  **Fältbeskrivning** |
 |:--------------------|:-------------         |
 |`list`               |Returnerar en lista över jobb som du har skickat. Argument, se `msgen help list`.  |
 |`submit`             |Skickar ett arbetsflöde-begäran till tjänsten. Argument, se `msgen help submit`.|
 |`status`             |Returnerar status för arbetsflödet som anges av `--workflow-id`. Se även `msgen help status`. |
 |`cancel`             |Skickar en begäran om att avbryta bearbetningen av arbetsflödet som anges av `--workflow-id`. Se även `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Var hittar jag värdet för `--api-url-base`?
Gå till Azure-portalen och öppna din kontosida för Genomics. Under den **Management** rubrik, Välj **åtkomstnycklar**. Där kan hitta du både API-URL och dina åtkomstnycklar.

## <a name="where-do-i-get-the-value-for---access-key"></a>Var hittar jag värdet för `--access-key`?
Gå till Azure-portalen och öppna din kontosida för Genomics. Under den **Management** rubrik, Välj **åtkomstnycklar**. Där kan hitta du både API-URL och dina åtkomstnycklar.

## <a name="why-do-i-need-two-access-keys"></a>Varför behöver jag två åtkomstnycklar?
Du behöver två åtkomstnycklar om du vill uppdatera (återskapa) dem utan att störa användningen av tjänsten. Du bör till exempel har alla nya arbetsflöden som använder den andra nyckeln om du vill uppdatera den första nyckeln. Vänta sedan tills alla arbetsflöden som använder den första nyckeln ska slutföras innan du uppdaterar den första nyckeln.

## <a name="do-you-save-my-storage-account-keys"></a>Sparar du nycklar för mitt lagringskonto?
Din lagringskontonyckel används för att skapa kortsiktig åtkomsttoken för tjänsten Microsoft Genomics att läsa dina indatafiler och skriva utdatafilerna. Token Standardlängden är 48 timmar. Varaktigheten för detta token kan ändras med den `-sas/--sas-duration` möjlighet att skicka kommandot; värdet är i timmar.

## <a name="what-genome-references-can-i-use"></a>Vilka genom refererar till kan jag använda?

Dessa referenser stöds:
 |Referens              | Värdet för `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (inga alt analys) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Hur formaterar jag min kommandoradsargument som en konfigurationsfil 

msgen förstår konfigurationsfiler i följande format:
* Alla alternativ anges som nyckel / värde-par med värden som är avgränsade från nycklar med ett kolon.
Blanksteg ignoreras.
* Rader som börjar med `#` ignoreras.
* Alla kommandoradsargument i långt format kan konverteras till en nyckel genom att ta bort dess ledande tankstreck och ersätta tankstreck mellan ord med understreck. Här följer några exempel konvertering:

 |Kommandoradsargument            | Konfiguration av fil-rad |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
 |`-k/--access-key KEY`            | *access_key:Key*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Nästa steg

Använd följande resurser för att komma igång med Microsoft Genomics:
- Kom igång genom att köra ditt första arbetsflöde genom Microsoft Genomics-tjänsten. [Köra ett arbetsflöde genom Microsoft Genomics-tjänsten ](quickstart-run-genomics-workflow-portal.md)
- Skicka dina egna data för bearbetning av tjänsten Microsoft Genomics: [par FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [flera FASTQ eller BAM](quickstart-input-multiple.md) 

