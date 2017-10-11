---
title: "Lägg till Yammer-koppling i dina Azure Logic Apps | Microsoft Docs"
description: "Översikt över Yammer-anslutningen med REST API-parametrar"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
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
ms.author: mandia; ladocs
ms.openlocfilehash: c7a213343b4fb2b5a89a5052a459061b404a431c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-yammer-connector"></a>Kom igång med Yammer-koppling
Ansluta till Yammer till access konversationer i företagsnätverket. Med Yammer kan du:

* Skapa ditt företag flödet som baseras på de data som du får från Yammer. 
* Använd utlöser för när det finns ett nytt meddelande i en grupp eller en feed din följande.
* Använd åtgärder för att skicka ett meddelande får alla meddelanden med mera. De här åtgärderna få svar och utdata gör tillgängligt för andra åtgärder. När ett nytt meddelande visas, kan du skicka ett e-postmeddelande med hjälp av Office 365.

Kom igång genom att skapa en logikapp nu. Se [skapa en logikapp](../logic-apps/logic-apps-create-a-logic-app.md).

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