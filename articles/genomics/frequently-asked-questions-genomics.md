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
ms.openlocfilehash: 804076fdc653622336ac3b99c15df0bc027510d9
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730159"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Vanliga frågor

Den här artikeln visar de viktigaste frågorna som du kanske har relaterat till Microsoft Genomics. Mer information om Microsoft Genomics-tjänsten finns i [vad är Microsoft Genomics?](overview-what-is-genomics.md). Mer information om felsökning finns i vår [felsökningsguiden för](troubleshooting-guide-genomics.md). 


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
Du behöver två åtkomstnycklar om du vill uppdatera (återskapa) dem utan att störa användningen av tjänsten. Exempelvis kan du uppdatera den första nyckeln. I så fall kan växla du alla nya arbetsflöden till den andra nyckeln. Vänta tills de pågående arbetsflöden med hjälp av den första nyckeln är klar. Först därefter uppdatera nyckeln.

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

