---
title: Översikt över Diagnostik i Azure Functions
description: Lär dig hur du kan felsöka problem med din funktionsapp med Diagnostik för Azure Functions.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834038"
---
# <a name="azure-functions-diagnostics-overview"></a>Översikt över Diagnostik i Azure Functions

När du kör en funktionsapp vill du vara förberedd på eventuella problem som kan uppstå, från 4xx-fel för att utlösa fel. Diagnostik för Azure Functions är en intelligent och interaktiv upplevelse som hjälper dig att felsöka din funktionsapp utan konfiguration eller extra kostnad. När du stöter på problem med din funktionsapp påpekar Diagnostiken för Azure Functions vad som är fel för att guida dig till rätt information för att enklare och snabbare felsöka och lösa problemet. Den här artikeln visar grunderna i hur du använder Azure Functions diagnostik för att snabbare diagnostisera och lösa vanliga funktionsproblem.

## <a name="start-azure-functions-diagnostics"></a>Starta Diagnostik för Azure Functions

Så här kommer du åt Diagnostiken för Azure Functions:

1. Navigera till din funktionsapp i [Azure-portalen](https://portal.azure.com).
2. Välj fliken **Plattformsfunktioner.**
3. Välj **Diagnostisera och lösa problem** under **Resurshantering**, som öppnar Azure Functions diagnostik.
4. Välj en kategori som bäst beskriver problemet med din funktionsapp med hjälp av nyckelorden på startpanelen. Du kan också skriva ett nyckelord som bäst beskriver problemet i sökfältet. Du kan till `execution` exempel skriva för att visa en lista över diagnostikrapporter som är relaterade till körningen av funktionsappar och öppna dem direkt från startsidan.

![Startsida](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Använda det interaktiva gränssnittet

När du har valt en startsidas kategori som bäst stämmer överens med funktionsappens problem kan Azure Functions diagnostics interaktiva gränssnitt, Genie, guida dig genom att diagnostisera och lösa problemet med din app. Du kan använda panelgenvägarna som tillhandahålls av Genie för att visa den fullständiga diagnostikrapporten för den problemkategori som du är intresserad av. Panelgenvägarna ger dig ett direkt sätt att komma åt dina diagnostikmått.

![Genie](./media/functions-diagnostics/genie.png)

När du har valt en panel kan du se en lista över ämnen som är relaterade till problemet som beskrivs i panelen. Dessa avsnitt innehåller utdrag med anmärkningsvärd information från den fullständiga rapporten. Du kan välja något av dessa ämnen för att undersöka problemen ytterligare. Du kan också välja **Visa fullständig rapport för** att utforska alla ämnen på en enda sida.

![Förhandsgranskning av diagnostikrapport](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Visa en diagnostikrapport

När du har valt ett ämne kan du visa en diagnostikrapport som är specifik för din funktionsapp. Diagnostikrapporter använder statusikoner för att ange om det finns några specifika problem med din app. Du ser detaljerad beskrivning av problemet, rekommenderade åtgärder, relaterade mått och användbara dokument. Anpassade diagnostikrapporter genereras från en serie kontroller som körs på din funktionsapp. Diagnostikrapporter kan vara ett användbart verktyg för att identifiera problem i din funktionsapp och vägleda dig mot att lösa problemet.

## <a name="find-the-problem-code"></a>Hitta problemkoden

För skriptbaserade funktioner kan du använda **Funktionskörning och fel** under **Funktionsapp nedåt eller rapporteringsfel** för att begränsa på raden med kod som orsakar undantag eller fel. Den här funktionen kan vara ett användbart verktyg för att komma till grundorsaken och åtgärda problem från en viss kodrad. Det här alternativet är inte tillgängligt för förkompilerade C# och Java-funktioner.

![Diagnostikrapport om funktionskörningsfel](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Undantag för funktion](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Nästa steg

Du kan ställa frågor eller ge feedback om Azure Functions diagnostik på [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Vänligen `[Diag]` inkludera i titeln på din feedback.

> [!div class="nextstepaction"]
> [Övervaka dina funktionsappar](functions-monitoring.md)
