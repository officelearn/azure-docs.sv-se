---
title: Konfigurera säkerhet för att komma åt och hantera Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskrivs hur du konfigurerar säkerheten och behörigheterna som hanteringsåtkomst principer och data åtkomstprinciper för att skydda Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 7cb5dc5b170103f98d56abc920f36dd85f855961
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364830"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Bevilja åtkomst till en Time Series Insights-miljö med Azure Portal

Time Series Insights-miljöer har två oberoende typer av principer för åtkomst.

* Hantera åtkomstprinciper
* Dataåtkomstprinciper

Båda principerna beviljar Azure Active Directory-huvudkonton (användare och appar) olika behörigheter i en viss miljö. Huvudkonton (användare och appar) måste tillhöra den active directory (kallas även Azure-klient) som är associerade med prenumerationen som innehåller miljön.

Principer för hantering av åtkomst beviljar behörigheter som rör konfigurationen av miljön, som
*   Skapandet och borttagningen av miljön, händelsekällor, referensdatamängder och
*   hantering av principerna för dataåtkomst.

Principerna för dataåtkomst beviljar behörigheter för att utfärda datafrågor, manipulera referensdata i miljön och sparade delade frågor och perspektiv som är associerade till miljön.

De två typerna av principer möjliggör en tydlig uppdelning mellan åtkomst till hantering av miljön och till data inuti miljön. Det är exempelvis möjligt att konfigurera en miljö så att miljöns ägare/skapare av miljön tas bort från dataåtkomsten. Dessutom kan beviljas användare och tjänster som ska kunna läsa data från miljön inte åtkomst till miljöns konfiguration.

[!INCLUDE [iot-tsi-data-access](../../includes/iot-tsi-data-access.md)]

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du lägger till en Event Hub-händelsekälla till Azure Time Series Insights-miljön](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.
* Visa din miljö i [Time Series Insights explorer](https://insights.timeseries.azure.com).
