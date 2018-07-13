---
title: Lägg till Twilio-anslutningen i Azure Logic apps | Microsoft Docs
description: Översikt över Twilio-anslutningen med REST API-parametrar
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 8bcf69a7c8e04cb45d795fd0d6f20d477c15865d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38652013"
---
# <a name="get-started-with-the-twilio-connector"></a>Kom igång med Twilio-anslutningen
Anslut till Twilio för att skicka och ta emot globala SMS och MMS IP-meddelanden. Med Twilio kan du:

* Skapa ditt flöde för företag som baseras på data som du får från Twilio. 
* Använd åtgärder som får ett meddelande och lista meddelanden. De här åtgärderna få svar och utdata gör tillgängligt för andra åtgärder. När du får ett nytt Twilio-meddelande kan du ta det här meddelandet och använda den till ett Service Bus-arbetsflöde. 

Kom igång genom att skapa en logikapp; Se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Skapa en anslutning till Twilio
När du lägger till den här anslutningen i logikappar, anger du följande Twilio-värden:

| Egenskap  | Krävs | Beskrivning |
| --- | --- | --- |
| Konto-ID |Ja |Ange ditt Twilio konto-ID |
| Åtkomsttoken |Ja |Ange ditt Twilio-åtkomsttoken |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Om du inte har en Twilio-åtkomsttoken, se [användarens identitet och åtkomsttoken](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Information om specifika

Visa alla utlösare och åtgärder som definierats i swagger och får även eventuella gränser i den [anslutningsinformationen](/connectors/twilio/).

## <a name="more-connectors"></a>Fler anslutningsappar
Gå tillbaka till den [API: er lista](apis-list.md).