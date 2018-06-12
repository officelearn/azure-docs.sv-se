---
title: Lägg till Twilio-koppling i dina Azure Logic apps | Microsoft Docs
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
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296143"
---
# <a name="get-started-with-the-twilio-connector"></a>Kom igång med Twilio-koppling
Anslut till Twilio skicka och ta emot globala IP, SMS och MMS-meddelanden. Med Twilio kan du:

* Skapa ditt företag flödet som baseras på de data som du får från Twilio. 
* Använd åtgärder som får ett meddelande och listmeddelanden. De här åtgärderna få svar och utdata gör tillgängligt för andra åtgärder. När du får ett nytt Twilio-meddelande kan du exempelvis ta meddelandet och använda den ett Service Bus-arbetsflöde. 

Kom igång genom att skapa en logikapp; Se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Skapa en anslutning till Twilio
När du lägger till den här anslutningen dina logic apps kan du ange följande Twilio-värden:

| Egenskap  | Krävs | Beskrivning |
| --- | --- | --- |
| Konto-ID |Ja |Ange ditt Twilio-konto-ID |
| Åtkomsttoken |Ja |Ange ditt Twilio-åtkomsttoken |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Om du inte har en Twilio-åtkomsttoken, se [användaridentitet & åtkomsttoken](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/twilio/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).