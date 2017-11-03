---
title: "Hur du lägger till en IoT-hubb händelsekälla Azure tid serien Insights miljön | Microsoft Docs"
description: "Den här självstudiekursen beskrivs hur du lägger till en källa som är ansluten till en IoT-hubb i miljön tid serien insikter"
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
ms.openlocfilehash: 3b98728ced81256d05b1bed2c92fc66c5ca61b98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-iot-hub-event-source"></a>Hur du lägger till en IoT-hubb händelsekälla

Den här självstudiekursen beskrivs hur du använder Azure-portalen för att lägga till en händelsekälla läser från en IoT-hubb i miljön tid serien insikter.

## <a name="prerequisites"></a>Krav

Du har skapat en IoT-hubb och skriver händelser till den. Mer information om IoT-hubbar finns <https://azure.microsoft.com/services/iot-hub/>

> [Konsumentgrupper] Varje gång serien insikter händelsekälla måste ha sin egen dedikerad konsumentgrupp som inte delas med andra användare. Om flera läsare förbrukar händelser från samma konsumentgrupp, förväntas alla läsare finns fel. Mer information finns i [IoT-hubb Utvecklarhandbok](../iot-hub/iot-hub-devguide.md).

## <a name="choose-an-import-option"></a>Välj ett alternativ

Inställningarna för händelsekällan kan anges manuellt eller en IoT-hubb kan väljas från IoT-hubbar som är tillgängliga för dig.
I den **importalternativ** selector, väljer du något av följande alternativ:

* Ange inställningar för IoT-hubb manuellt
* Använd IoT-hubb från tillgängliga prenumerationer

### <a name="select-an-available-iot-hub"></a>Välj en tillgänglig IoT-hubb

I följande tabell beskrivs varje alternativ på fliken ny händelsekälla med dess beskrivning när du väljer en tillgänglig IoT-hubb som en händelsekälla:

| EGENSKAPSNAMN | BESKRIVNING |
| --- | --- |
| Händelsekällans namn | Namnet på din händelsekälla. Det här namnet måste vara unikt i din miljö för tid serien insikter.
| Källa | Välj **IoT-hubb** att skapa en IoT-hubb händelsekälla.
| Prenumerations-Id | Välj den prenumeration som den här IoT-hubben har skapats.
| IoT-hubbnamnet | Välj namnet på IoT-hubben.
| Principnamn för IoT-hubb | Välj den princip för delad åtkomst som finns på inställningsfliken för IoT-hubb. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för dina händelsekällan *måste* har **tjänsten ansluta** behörigheter.
| Konsumentgrupp för IoT-hubb | Konsumentgrupp som händelser ska läsas från IoT-hubben. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla.

### <a name="provide-iot-hub-settings-manually"></a>Ange inställningar för IoT-hubb manuellt

I följande tabell beskrivs varje egenskap på fliken ny händelsekälla med dess beskrivning när du anger inställningarna manuellt:

| EGENSKAPSNAMN | BESKRIVNING |
| --- | --- |
| Händelsekällans namn | Namnet på din händelsekälla. Det här namnet måste vara unikt i din miljö för tid serien insikter.
| Källa | Välj **IoT-hubb** att skapa en IoT-hubb händelsekälla.
| Prenumerations-Id | Den prenumeration som den här IoT-hubben har skapats.
| Resursgrupp | Den prenumeration som den här IoT-hubben har skapats.
| IoT-hubbnamnet | Namnet på din IoT-hubb. När du skapade din IoT-hubb gav du den även ett specifikt namn
| Principnamn för IoT-hubb | Den princip för delad åtkomst som kan skapas på inställningsfliken för IoT-hubb. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. Princip för delad åtkomst för dina händelsekällan *måste* har **tjänsten ansluta** behörigheter.
| IoT-hubb principnyckel | Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnrymd. Ange de primära och sekundära nycklarna här.
| Konsumentgrupp för IoT-hubb | Konsumentgrupp som händelser ska läsas från IoT-hubben. Vi rekommenderar starkt att använda en dedikerad konsumentgrupp för din händelsekälla.

## <a name="next-steps"></a>Nästa steg

1. Lägga till en princip för åtkomst av data i din miljö [Definiera principer för dataåtkomst](time-series-insights-data-access.md)
1. Åtkomst till din miljö i det [tid serien Insights-portalen](https://insights.timeseries.azure.com)