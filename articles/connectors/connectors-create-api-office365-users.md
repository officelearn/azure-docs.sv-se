---
title: Anslut till Office 365-användare – Azure Logic Apps | Microsoft Docs
description: 'Hantera användarprofiler med Office 365 användare REST API: er och Azure Logic Apps'
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 3865fbc4fbc39da0860218565b0a8956b2dad8ee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105879"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Kom igång med anslutningsappen för Office 365-användare
Anslut till Office 365-användare att hämta profiler och Sök efter användare. Med Office 365-användare kan du:

* Skapa ditt flöde för företag som baseras på data som du får från Office 365-användare. 
* Använd åtgärder som hämta direktrapporter, hämta en chef användarprofil och mycket mer. De här åtgärderna få svar och utdata gör tillgängligt för andra åtgärder. Exempelvis kan du hämta direktrapporter för en person, och sedan tar den här informationen och uppdatera en SQL Azure-databas. 

Du kan komma igång genom att skapa en logikapp nu, se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Skapa en anslutning till Office 365-användare
När du lägger till den här anslutningen i logikappar, måste du logga in på ditt Office 365-användare och ge logic apps för att ansluta till ditt konto.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

När du har skapat anslutningen kan ange du egenskaper för Office 365-användare, t.ex. användar-ID. Den **REST API-referens** i den här artikeln beskriver de här egenskaperna.

## <a name="connector-specific-details"></a>Information om specifika

Visa alla utlösare och åtgärder som definierats i swagger och får även eventuella gränser i den [anslutningsinformationen](/connectors/officeusers/).

## <a name="more-connectors"></a>Fler anslutningsappar
Gå tillbaka till den [API: er lista](apis-list.md).