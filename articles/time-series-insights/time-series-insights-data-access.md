---
title: "Principer för dataåtkomst i Azure Time Series Insights | Microsoft Docs"
description: "I den här självstudien lär du dig att hantera principer för att hantera dataåtkomst i Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: e975c6d8f217bc73948c0c046204b16b1a742bc7
ms.contentlocale: sv-se
ms.lasthandoff: 05/20/2017

---

# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Bevilja åtkomst till en Time Series Insights-miljö med Azure Portal

Time Series Insights-miljöer har två oberoende typer av principer för åtkomst.

* Hantera åtkomstprinciper
* Dataåtkomstprinciper

Båda principerna beviljar Azure Active Directory-huvudkonton (användare och appar) olika behörigheter i en viss miljö. Huvudkontona (användare och appar) måste tillhöra den Active Directory (eller ”Azure-klient”) som är associerad med prenumerationen som innehåller miljön.

Principer för hantering av åtkomst beviljar behörigheter som rör konfigurationen av miljön, som
*    Skapandet och borttagningen av miljön, händelsekällor, referensdatamängder och
*    hantering av principerna för dataåtkomst.

Principerna för dataåtkomst beviljar behörigheter för att utfärda datafrågor, manipulera referensdata i miljön och sparade delade frågor och perspektiv som är associerade till miljön.

De två typerna av principer möjliggör en tydlig uppdelning mellan åtkomst till hantering av miljön och till data inuti miljön. Det är till exempel möjligt att konfigurera en miljö så att miljöns ägare/skapare tas bort från dataåtkomsten. Användare och tjänster som tillåts läsa data från miljön kan nekas åtkomst till miljöns konfiguration.

## <a name="grant-data-access"></a>Bevilja åtkomst till data
I följande steg visas hur du ger åtkomst till data för en användares huvudnamn:

1.    Logga in på [Azure Portal](https://portal.azure.com).
2.    Klicka på ”Alla resurser” på menyn på vänster sida av Azure Portal.
3.    Välj Time Series Insights-miljö.

  ![Hantera Time Series Insights-källan – miljö](media/data-access/getstarted-grant-data-access1.png)

4.    Välj ”Dataplansåtkomst” och klicka på ”Lägg till”

  ![Hantera Time Series Insights-källan – lägg till](media/data-access/getstarted-grant-data-access2.png)

5.    Klicka på ”Välj användare”.
6.    Sök och välj användare via e-postmeddelandet.
7.    Klicka på ”Välj” i bladet ”Välj användare”.

  ![Hantera Time Series Insights-källan – välj användare](media/data-access/getstarted-grant-data-access3.png)

8.    Klicka på ”Välj roll”.
9.    Välj ”Deltagare” om du vill tillåta att användare kan ändra referensdata och dela sparade frågor och perspektiv med andra användare av miljön. Välj annars ”Läsare” för att tillåta att användare söker data i miljön och sparar personliga (inte delade) frågor i miljön.
10.    Klicka på ”OK” på bladet ”Välj roll”.

  ![Hantera Time Series Insights-källan – välj roll](media/data-access/getstarted-grant-data-access4.png)

11.    Klicka på ”OK” på bladet ”Välj användarroll”.
12.    Du bör se:

  ![Hantera Time Series Insights-källan – resultat](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa en händelsekälla](time-series-insights-add-event-source.md)
* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan
* Visa din miljö i [Time Series Insights-portalen](https://insights.timeseries.azure.com)

