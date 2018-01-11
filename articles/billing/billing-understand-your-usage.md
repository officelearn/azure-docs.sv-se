---
title: "Förstå din Azure detaljerad användning"
description: "Lär dig hur du läst och förstått avsnitt i din detaljerad användning CSV för din Azure-prenumeration"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 999f24b5a5c1bd5d259b785f9107697d2928b4eb
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Förstå villkor för din Microsoft Azure användningsdebitering 

Detaljerad användning avgifter CSV-fil innehåller dagligen och mätaren nivå användningskostnader för den aktuella faktureringsperioden. 

För detaljerad användning-fil, se [få dina Azure billing faktura och dagligen användningsdata](billing-download-azure-invoice-daily-usage-date.md).
Den är tillgänglig i en fil med kommaavgränsade värden (CSV)-filformat som kan öppnas i ett kalkylbladsprogram. Om du ser två versioner som är tillgängliga, hämta version 2. Det är det mest aktuella filformatet.

Avgifterna för användning av är det totala antalet **månatliga** avgifter för en prenumeration. Avgifter för användning beakta inte eventuella krediter eller rabatter.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Detaljerade villkor och beskrivningar av filen detaljerad användning

I följande avsnitt beskrivs viktiga termer som visas i version 2 av filen detaljerad användning.

### <a name="statement"></a>Meddelande

Överst i CSV-fil för detaljerad användning visas de tjänster som du använde under den månaden faktureringsperioden. Följande tabell listar de termer och beskrivningar som visas i det här avsnittet.

| Period | Beskrivning |
| --- | --- |
|Faktureringsperiod |När mätare användes faktureringsperioden |
|Mätarkategori |Identifierar tjänsten på den översta nivån för användning |
|Mätarens underkategori |Definierar typ av Azure-tjänst som kan påverka hastigheten |
|Mätarnamn |Identifierar måttenheten för mätaren används |
|Mätarregion |Identifierar platsen för datacenter för vissa tjänster som är mest baserat på plats för datacenter |
|SKU |Identifierar unika system-ID för varje Azure-mätare |
|Enhet |Identifierar i vilken enhet tjänsten debiteras. Till exempel GB, timmar, 10 000 s. |
|Förbrukat antal |Mängden mätaren används under faktureringsperioden |
|Inkluderad mängd |Mängden mätaren som ingår kostnadsfritt i din aktuella faktureringsperioden |
|Överbliven kvantitet |Visar skillnaden mellan antalet används och det antal som ingår. Det är debiteras för den här mängden. För erbjudanden med ingen ingår kvantitet betala per användning med erbjudandet är denna summa samma som antalet används. |
|Inom åtagande |Visar mätaren avgifter som dras från din beloppet som är associerade med 6 eller 12 månaders erbjudandet. Mätaren avgifter subtraheras i kronologisk ordning. |
|Valuta |Valutan som används i din aktuella faktureringsperioden |
|Överanvändning |Visar mätaren avgifterna som överskrider din beloppet som är associerade med erbjudandet 6 eller 12 månader |
|Åtagandepris |Visar antalet åtagande baserat på totala beloppet som är associerade med erbjudandet 6 eller 12 månader |
|Pris |Hastigheten med vilken du är debiteras per fakturerbar enhet |
|Värde |Visar resultatet av att multiplicera kolumnen överförbrukning antal efter kolumnen hastighet. Om antalet används inte överstiger antalet ingår, är gratis i den här kolumnen. |

### <a name="daily-usage"></a>Daglig användning

Avsnittet daglig användning i CSV-filen visar användningsinformation som påverkar de fakturerbara kostnader. Följande tabell listar de termer och beskrivningar som visas i det här avsnittet.

| Period | Beskrivning |
| --- | --- |
|Datum för användning |Datum när mätaren användes |
|Mätarkategori |Identifierar tjänsten på den översta nivån som tillhör denna användning |
|Mätar-ID |Fakturerade mätaren identifierare som används för pris fakturering användning |
|Mätarens underkategori |Definierar den Azure-tjänst-typ som kan påverka hastigheten |
|Mätarnamn |Identifierar måttenheten för mätaren används |
|Mätarregion |Identifierar platsen för datacenter för vissa tjänster som är mest baserat på plats för datacenter |
|Enhet |Identifierar den enhet som mätaren debiteras i. Till exempel GB, timmar, 10 000 s. |
|Förbrukat antal |Mängden mätaren har förbrukats för den dagen |
|Resursplats |Identifierar datacenter där mätaren körs |
|Förbrukad tjänst |Tjänsten Azure-plattformen som du använde |
|Resursgrupp |Resursgruppen som distribuerade mätaren körs i. <br/><br/>Mer information finns i [Översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|Instans-ID | Identifierare för mätaren. <br/><br/> Identifieraren innehåller namnet du anger för mätaren när den skapades. Det är antingen namnet på resursen eller fullständiga resurs-ID. Mer information finns i [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources). |
|Taggar | Taggen som du tilldelar mätaren. Använd taggar fakturering posterna.<br/><br/>Du kan till exempel använda taggar för att distribuera kostnaderna av den enhet som använder mätaren. Tjänster som stöder ljusavgivande taggar är virtuella datorer, lagring och nätverkstjänster som etablerats med hjälp av den [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources). Mer information finns i [ordna dina Azure-resurser med taggar](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Ytterligare info |Tjänstspecifika metadata. Till exempel en bildtyp för en virtuell dator. |
|Serviceinfo 1 |Projektnamnet som tjänsten som tillhör på din prenumeration |
|Serviceinfo 2 |Äldre fält som samlar in valfria tjänstspecifika metadata |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Hur gör jag till att tilläggen i detaljerad användning filen är korrekta?
Om en avgift på detaljerad användning filen som du vill ha mer information på Se [förstå fakturan för Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>Nyheter om externa serviceavgifter?
Externa tjänster (även kallat marknadsplatsorder) tillhandahålls av oberoende tjänsteleverantörer och faktureras separat. Avgifterna visas inte på Azure fakturan. Läs mer i [förstå Azure externa avgifterna](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?) få snabbt lösa problemet.
