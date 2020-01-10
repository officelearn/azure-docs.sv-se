---
title: Översikt över Azure Functions diagnostik
description: Lär dig hur du kan felsöka problem med din Function-app med Azure Functions Diagnostics.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834038"
---
# <a name="azure-functions-diagnostics-overview"></a>Översikt över Azure Functions diagnostik

När du kör en Function-app vill du vara för beredd för eventuella problem som kan uppstå, från 4xx-fel till Utlös ande fel. Azure Functions Diagnostics är en intelligent och interaktiv upplevelse som hjälper dig att felsöka din Function-app utan någon konfiguration eller extra kostnad. När du stöter på problem med din Function-app, kan Azure Functions diagnostik ta reda på vad som är fel för att hjälpa dig med rätt information för att enklare och snabbare felsöka och lösa problemet. Den här artikeln visar grunderna i hur du använder Azure Functions Diagnostics för att snabbare diagnostisera och lösa vanliga problem med appar.

## <a name="start-azure-functions-diagnostics"></a>Starta Azure Functions diagnostik

För att komma åt Azure Functions diagnostik:

1. Navigera till din Function-app i [Azure Portal](https://portal.azure.com).
2. Välj fliken **plattforms funktioner** .
3. Välj **diagnostisera och lös problem** under **resurs hantering**, som öppnar Azure Functions Diagnostics.
4. Välj en kategori som bäst beskriver problemet med din Function-app med hjälp av nyckelorden på Start sidan. Du kan också ange ett nyckelord som bäst beskriver problemet i Sök fältet. Du kan till exempel skriva `execution` om du vill visa en lista med diagnostiska rapporter som är relaterade till din funktion för att köra appen och öppna dem direkt från start sidan.

![Startsida](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Använda det interaktiva gränssnittet

När du har valt en kategori för start sidan som bäst överensstämmer med funktions appens problem, kan Azure Functions diagnosticss interaktiva gränssnitt, Genie, hjälpa dig att diagnostisera och lösa problem med din app. Du kan använda de kortkommandon som tillhandahålls av Genie för att visa en fullständig diagnostisk rapport för den problem kategori som du är intresse rad av. Panel gen vägarna ger dig ett direkt sätt att komma åt dina diagnostiska mått.

![Genie](./media/functions-diagnostics/genie.png)

När du har valt en panel kan du se en lista över de avsnitt som handlar om problemet som beskrivs i panelen. De här avsnitten innehåller kodfragment av viktig information från den fullständiga rapporten. Du kan välja något av dessa avsnitt om du vill undersöka problemen ytterligare. Du kan också välja **Visa fullständig rapport** om du vill utforska alla ämnen på en enda sida.

![Förhands granskning av diagnostisk rapport](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Visa en diagnostisk rapport

När du har valt ett ämne kan du Visa en diagnostisk rapport som är unik för din Function-app. Diagnostiska rapporter använder status ikoner för att indikera om det finns några specifika problem med din app. Du ser detaljerad beskrivning av problemet, rekommenderade åtgärder, relaterade mått och användbara dokument. Anpassade diagnostiska rapporter genereras från en serie kontroller som körs i din Function-app. Diagnostiska rapporter kan vara ett användbart verktyg för att hitta problem i din Function-app och GUID för att lösa problemet.

## <a name="find-the-problem-code"></a>Hitta problem koden

För skriptbaserade funktioner kan du använda **Function Execution och errors** under **Funktionsapp ned eller rapportera fel** för att begränsa den kodrad som orsakar undantag eller fel. Den här funktionen kan vara ett användbart verktyg för att komma till rotor saken och åtgärda problem från en speciell kodrad. Det här alternativet är inte tillgängligt för C# förkompilerade och Java-funktioner.

![Diagnostisk rapport om funktions körnings fel](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Funktions undantag](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Nästa steg

Du kan ställa frågor eller ge feedback om Azure Functions diagnostik på [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Ta med `[Diag]` i titeln på din feedback.

> [!div class="nextstepaction"]
> [Övervaka dina funktions program](functions-monitoring.md)
