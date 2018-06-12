---
title: Wunderlist koppling i n Azure logikappar | Microsoft Docs
description: Skapa en anslutning till Wunderlist och använda den här anslutningen för att skapa ditt arbetsflöde i logikappar.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 1ed9b19700157abca6e5ac4265f1e8c99a3d846d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296381"
---
# <a name="get-started-with-the-wunderlist-connector"></a>Kom igång med Wunderlist-koppling
Wunderlist är en todo list- och manager för att hjälpa användare att utföra sina uppgifter.  Om du delar en privata lista fungerar på ett projekt eller planera en ledighet kan enkelt Wunderlist att samla in, dela och slutföra din listobjekt. Wunderlist synkroniserar direkt mellan din telefon, surfplatta och datorn, så du kan komma åt alla aktiviteter från var som helst.

Kom igång genom att skapa en logikapp nu. Se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-wunderlist"></a>Skapa en anslutning till Wunderlist
För att skapa logikappar med Wunderlist, måste du först skapa en **anslutning** ange detaljer för följande egenskaper:

| Egenskap  | Krävs | Beskrivning |
| --- | --- | --- |
| Token |Ja |Ange autentiseringsuppgifter för Wunderlist |

När du har skapat anslutningen kan du använda den och utföra åtgärderna som du kan lyssna efter utlösare.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/wunderlist/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).