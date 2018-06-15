---
title: 'Microsoft Genomics: Vanliga frågor | Microsoft Docs'
titleSuffix: Azure
description: Svar på vanliga frågor kunder fråga om Microsoft Genomics.
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: b3f30dc5d185615370a8273f71554f784d286cd9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517026"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Vanliga frågor

Den här artikeln innehåller de vanligaste frågorna som du kan ha relaterade till Microsoft Genomics. Läs mer på tjänsten Microsoft Genomics [vad är Microsoft Genomics?](overview-what-is-genomics.md). Mer information om felsökning finns i vår [felsökningsguiden](troubleshooting-guide-genomics.md). 


## <a name="what-is-the-sla-for-microsoft-genomics"></a>Vad är SLA för Microsoft Genomics?
Vi garanterar 99,9% av tiden Microsoft Genomics tjänsten kommer att kunna ta emot begäranden om arbetsflödet API. Mer information finns i [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Hur användningen av Microsoft Genomics visas på min faktura?
Microsoft Genomics växlar baserat på antalet gigabases som bearbetas per arbetsflöde. Mer information finns i [priser](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Var hittar en lista över alla möjliga kommandon och argument för den `msgen` klienten?
Du får en fullständig lista över tillgängliga kommandon och argument genom att köra `msgen help`. Om inga ytterligare argument har angetts visas en lista över tillgänglig hjälp avsnitt, en för varje `submit`, `list`, `cancel`, och `status`. För att få hjälp för ett specifikt kommando skriver `msgen help command`, till exempel `msgen help submit` visar en lista över alla skicka alternativ.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Vilka är de vanligaste använda kommandon för det `msgen` klienten?
De vanligaste kommandona är argument för den `msgen` klienten inkluderar: 

 |**Kommando**          |  **Fältbeskrivning** |
 |:--------------------|:-------------         |
 |`list`               |Returnerar en lista över jobb som du har skickat. Argument, se `msgen help list`.  |
 |`submit`             |Skickar en begäran om arbetsflödet till tjänsten. Argument, se `msgen help submit`.|
 |`status`             |Returnera statusen för arbetsflödet som anges av `--workflow-id`. Se även `msgen help status`. |
 |`cancel`             |Skickar en begäran om att avbryta bearbetningen av arbetsflödet som anges av `--workflow-id`. Se även `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Var hittar jag värdet för `--api-url-base`?
Gå till Azure-portalen och öppna sidan genomik konto. Under den **Management** rubrik, Välj **åtkomstnycklar**. Där kan du söka efter både API-URL och dina åtkomstnycklar.

## <a name="where-do-i-get-the-value-for---access-key"></a>Var hittar jag värdet för `--access-key`?
Gå till Azure-portalen och öppna sidan genomik konto. Under den **Management** rubrik, Välj **åtkomstnycklar**. Där kan du söka efter både API-URL och dina åtkomstnycklar.

## <a name="why-do-i-need-two-access-keys"></a>Varför behöver två åtkomstnycklar?
Du behöver två åtkomstnycklar om du vill uppdatera (generera) dem utan att avbryta användning av tjänsten. Till exempel att du vill uppdatera den första nyckeln. I så fall kan växla du alla nya arbetsflöden till med hjälp av den andra nyckeln. Vänta tills de redan körs arbetsflöden med hjälp av den första nyckeln är klar. Endast sedan uppdatera nyckeln.

## <a name="do-you-save-my-storage-account-keys"></a>Sparar du min lagringskontonycklar?
Din lagringskontonyckel används för att skapa kortsiktig åtkomsttoken för tjänsten Microsoft Genomics att läsa dina indatafiler och skriva utdatafilerna. Token Standardlängden är 48 timmar. Varaktigheten för detta token kan ändras med den `-sas/--sas-duration` möjlighet att skicka-kommandot; värdet anges i timmar.

## <a name="what-genome-references-can-i-use"></a>Vilka påvisa refererar till kan jag använda?

Dessa referenser stöds:
 |Referens              | Värdet för `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (ingen alt analys) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Hur formaterar min kommandoradsargument som en konfigurationsfil? 

msgen förstår konfigurationsfiler i följande format:
* Alla alternativ har angetts som nyckel / värde-par med värden åtskilda från nycklar av ett kolon.
Blanksteg ignoreras.
* Rader som börjar med `#` ignoreras.
* Några kommandoradsargument långt datumformat kan konverteras till en nyckel genom att ta bort dess inledande bindestreck och ersätta streck mellan ord med understreck. Här följer några exempel på konvertering:

 |Kommandoradsargument            | Konfiguration av fil rad |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
 |`-k/--access-key KEY`            | *access_key:Key*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Nästa steg

Använd följande resurser för att komma igång med Microsoft Genomics:
- Kom igång genom att köra ditt första arbetsflöde via tjänsten Microsoft Genomics. [Köra ett arbetsflöde via tjänsten Microsoft Genomics ](quickstart-run-genomics-workflow-portal.md)
- Skicka data för bearbetning av tjänsten Microsoft Genomics: [länkas FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [flera FASTQ eller BAM](quickstart-input-multiple.md) 

