---
title: "Lägg till Yammer-koppling i dina Azure Logic Apps | Microsoft Docs"
description: "Översikt över Yammer-anslutningen med REST API-parametrar"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 7f1758e9b95f534b23188f427ae0edaddbb29a48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-yammer-connector"></a>Kom igång med Yammer-koppling
Ansluta till Yammer till access konversationer i företagsnätverket. Med Yammer kan du:

* Skapa ditt företag flödet som baseras på de data som du får från Yammer. 
* Använd utlöser för när det finns ett nytt meddelande i en grupp eller en feed din följande.
* Använd åtgärder för att skicka ett meddelande får alla meddelanden med mera. De här åtgärderna få svar och utdata gör tillgängligt för andra åtgärder. När ett nytt meddelande visas, kan du skicka ett e-postmeddelande med hjälp av Office 365.

Kom igång genom att skapa en logikapp nu. Se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-yammer"></a>Skapa en anslutning till Yammer
Om du vill använda Yammer-anslutningstjänsten måste du först skapa en **anslutning** ange detaljer för dessa egenskaper: 

| Egenskap | Krävs | Beskrivning |
| --- | --- | --- |
| Token |Ja |Ange autentiseringsuppgifter för Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/yammer/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).