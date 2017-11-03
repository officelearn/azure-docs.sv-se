---
title: "Hur du lägger till en Händelsehubb händelsekälla Azure tid serien Insights miljön | Microsoft Docs"
description: "Den här självstudiekursen beskrivs hur du lägger till en källa som är ansluten till en Händelsehubb till tid serien insikter-miljön"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: f6a993b3858cfb94dd9795f5e55f15fa6ec7dcb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-event-hub-event-source"></a>Hur du lägger till en Händelsehubb händelsekälla

Den här kursen visar hur du använder Azure-portalen att lägga till en händelsekälla läser från en Händelsehubb till tid serien insikter-miljön.

## <a name="prerequisites"></a>Krav

Du har skapat en Händelsehubb och skriver händelser till den. Mer information om Händelsehubbar finns <https://azure.microsoft.com/services/event-hubs/>

> [Konsumentgrupper] Varje gång serien insikter händelsekälla måste ha sin egen dedikerad konsumentgrupp som inte delas med andra användare. Om flera läsare förbrukar händelser från samma konsumentgrupp, förväntas alla läsare finns fel. Observera att det finns också en gräns på 20 konsumentgrupper per Event Hub. Mer information finns i [Programmeringsguide för Event Hubs](../event-hubs/event-hubs-programming-guide.md).

## <a name="choose-an-import-option"></a>Välj ett alternativ

Inställningarna för händelsekällan kan anges manuellt eller en händelsehubb kan väljas från händelsehubbar som är tillgängliga för dig.
I den **importalternativ** selector, väljer du något av följande alternativ:

* Ange Event Hub-inställningar manuellt
* Använd Händelsehubb från tillgängliga prenumerationer

### <a name="select-an-available-event-hub"></a>Välj en tillgänglig Händelsehubb

I följande tabell beskrivs varje alternativ på fliken ny händelsekälla med dess beskrivning när du väljer en tillgänglig Händelsehubb som en händelsekälla:

| EGENSKAPSNAMN | BESKRIVNING |
| --- | --- |
| Händelsekällans namn | Namnet på din händelsekälla. Det här namnet måste vara unikt i din miljö för tid serien insikter.
| Källa | Välj **Händelsehubb** att skapa en Händelsehubb händelsekälla.
| Prenumerations-Id | Välj den prenumeration som den här händelsehubben skapades.
| Service bus-namnrymd | Välj Service Bus-namnrymd som innehåller Händelsehubben.
| Namnet på händelsehubben | Välj namnet på Händelsehubben.
| Namnet på händelsehubben princip | Välj den princip för delad åtkomst som kan skapas på fliken Event Hub konfigurera. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för dina händelsekällan *måste* har **läsa** behörigheter.
| Event hub konsumentgrupp | Konsumentgrupp att läsa händelser från Event Hub. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla.

### <a name="provide-event-hub-settings-manually"></a>Ange Event Hub-inställningar manuellt

I följande tabell beskrivs varje egenskap på fliken ny händelsekälla med dess beskrivning när du anger inställningarna manuellt:

| EGENSKAPSNAMN | BESKRIVNING |
| --- | --- |
| Händelsekällans namn | Namnet på din händelsekälla. Det här namnet måste vara unikt i din miljö för tid serien insikter.
| Källa | Välj **Händelsehubb** att skapa en Händelsehubb händelsekälla.
| Prenumerations-Id | Den prenumeration som den här händelsehubben skapades.
| Resursgrupp | Den prenumeration som den här händelsehubben skapades.
| Service bus-namnrymd | En Service Bus-namnrymd är en behållare för en uppsättning meddelandeentiteter. När du har skapat en ny Händelsehubb skapade du även en Service Bus-namnrymd.
| Namnet på händelsehubben | Namnet på din Event Hub. När du skapade din händelsehubb gav du den även ett specifikt namn
| Namnet på händelsehubben princip | Den princip för delad åtkomst som kan skapas på fliken Event Hub konfigurera. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för dina händelsekällan *måste* har **läsa** behörigheter.
| Event hub principnyckel | Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnrymd. Ange de primära och sekundära nycklarna här.
| Event hub konsumentgrupp | Konsumentgrupp att läsa händelser från Event Hub. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla.

## <a name="next-steps"></a>Nästa steg

1. Lägga till en princip för åtkomst av data i din miljö [Definiera principer för dataåtkomst](time-series-insights-data-access.md)
1. Åtkomst till din miljö i det [tid serien Insights-portalen](https://insights.timeseries.azure.com)