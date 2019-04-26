---
title: Förstå Azure detaljerad användningsinformation | Microsoft Docs
description: Lär dig hur du har läst och förstått avsnitt i detaljerad användningsinformation CSV för din Azure-prenumeration
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: a143fc6d9dbd78ae365f943a00ac9f8492d5e51c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60369632"
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Förstå villkoren på din Microsoft Azure användningsdebitering 

Detaljerad användning avgifter CSV-filen innehåller avgifter för användning av varje dag och mätare för den aktuella faktureringsperioden. 

För att få din detaljerad användningsfil kan se [så här hämtar du din Azure-fakturering och daglig användningsdata](billing-download-azure-invoice-daily-usage-date.md).
Det är tillgängligt i en fil med kommaavgränsade värden (.csv)-format som kan öppnas i ett kalkylbladsprogram. Om det finns två tillgängliga versioner laddar du ned version två. Det är det mest aktuella filformatet.

Användningsdebitering är det totala antalet **månatliga** debiteringar i en prenumeration. Avgifter för användning beakta inte en kredit eller rabatt.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Detaljerade villkor och beskrivningar av filen detaljerad användning

I följande avsnitt beskrivs viktiga termer som visas i version 2 av filen detaljerad användning.

### <a name="statement"></a>Meddelande

Överst på detaljerad användning CSV-filen visas de tjänster som du använde under faktureringsperioden för den månaden. I följande tabell visas de termer och beskrivningar som visas i det här avsnittet.

| Period | Beskrivning |
| --- | --- |
|Faktureringsperiod |Faktureringsperioden när mätarna som har använts |
|Mätarkategori |Identifierar huvudtjänst för användning |
|Mätarens underkategori |Anger typen av Azure-tjänst som kan påverka priset |
|Mätarnamn |Anger måttenhet för den mätning som används |
|Mätarregion |Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret |
|SKU |Identifierar unika system-ID för varje Azure mätare |
|Enhet |Identifierar i vilken enhet tjänsten debiteras. Till exempel GB, timmar, 10 000 s. |
|Förbrukat antal |Hur mycket av används under faktureringsperioden |
|Inkluderad mängd |Mängden mätaren som ingår utan kostnad i den aktuella faktureringsperioden |
|Överbliven kvantitet |Visar skillnaden mellan det antal som används och det antal som ingår. Du faktureras för den mängden. För användningsbaserad betalning där inga ingår kvantitet med erbjudandet är denna summa samma som den förbrukade mängden. |
|Inom åtagande |Visar mätnings-kostnader som subtraheras från ditt åtagandebelopp som är associerade med 6 eller 12-erbjudandet. Mätaren avgifter dras i kronologisk ordning. |
|Valuta |Den valuta i den aktuella faktureringsperioden |
|Överanvändning |Visar mätnings-avgifter som överstiger ditt åtagandebelopp som är associerade med ditt 6- eller 12-månaders erbjudande |
|Åtagandepris |Visar åtagandepriset baserat på totala åtagandet som är associerade med ditt 6- eller 12-månaders erbjudande |
|Pris |Det pris du debiteras per faktureringsbar enhet |
|Värde |Visar resultatet av gånger kolumnen pris överförbrukning kvantitet kolumnen. Om Förbrukat antal inte överstiger det antal som ingår, kostar ingenting i den här kolumnen. |

### <a name="daily-usage"></a>Daglig användning

Avsnittet daglig användning i CSV-filen visar användningsinformation som påverkar faktureringstaxor. I följande tabell visas de termer och beskrivningar som visas i det här avsnittet.

| Period | Beskrivning |
| --- | --- |
|Datum för användning |Det datum när mätaren användes |
|Mätarkategori |Identifierar den huvudtjänst som användningen gäller |
|Mätar-ID |Identifierare för faktureringsmätaren som används för prisfaktureringsanvändning |
|Mätarens underkategori |Definierar den Azure-tjänst-typ som kan påverka priset |
|Mätarnamn |Anger måttenhet för den mätning som används |
|Mätarregion |Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret |
|Enhet |Identifierar enhet mätaren debiteras. Till exempel GB, timmar, 10 000 s. |
|Förbrukat antal |Mängden mätaren som har förbrukats för den dagen |
|Resursplats |Identifierar det datacenter där mätaren körs |
|Förbrukad tjänst |Azure-plattformstjänster som du använde |
|Resursgrupp |Resursgruppen där den distribuerade mätning körs i. <br/><br/>Mer information finns i [Översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|Instans-ID | Identifierare för mätaren. <br/><br/> Identifieraren innehåller namnet du anger för mätaren när den skapades. Det är antingen namnet på resursen eller det fullständigt kvalificerade resurs-ID. Mer information finns i [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources). |
|Taggar | Tagg som du tilldelar till mätaren. Använd taggar om du vill gruppera faktureringsposter.<br/><br/>Du kan till exempel använda taggar för att fördela kostnader per avdelning som använder mätaren. Tjänster som stöder sändande taggar är virtuella datorer, lagring och nätverkstjänster som etablerats med hjälp av den [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources). Mer information finns i [ordna dina Azure-resurser med taggar](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Ytterligare info |Tjänstspecifik metadata. Till exempel detta en Avbildningstyp för en virtuell dator. |
|Serviceinfo 1 |Det projektnamn som tillhör tjänsten för din prenumeration |
|Serviceinfo 2 |Äldre fält som samlar in valfria tjänstspecifika metadata |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Hur gör jag till att kostnader på min detaljerad användningsfil är korrekta?
Om det finns en avgift på din detaljerad användning-fil som du vill ha mer information på, se [förstå fakturan för Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>Vad gäller externa tjänstavgifter?
Externa tjänster (även kallat Marketplace-order) tillhandahålls av oberoende tjänsteleverantörer och faktureras separat. Kostnaderna som visas inte på fakturan som Azure. Mer information finns i [förstå din Azure externa tjänstavgifter](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
