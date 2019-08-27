---
title: Anslut till GitHub-Azure Logic Apps
description: 'Övervaka GitHub-händelser med GitHub REST API: er och Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 6a6e2a803ee2a272189abf0f21796b2305eea40b
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050902"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Anslut till GitHub från Azure Logic Apps

GitHub är en webbaserad värd tjänst för git-lagring som erbjuder alla funktioner för distribuerad revision och SCM (hantering av käll kods hantering) i git och andra funktioner.

Kom igång med GitHub-anslutningen genom att [först skapa en Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Skapa en anslutning till GitHub

Om du vill använda GitHub-anslutningen i en Logic-app måste du först skapa en *anslutning* och sedan ange information om de här egenskaperna: 

| Egenskap | Obligatorisk | Beskrivning | 
| -------- | -------- | ----------- | 
| Token | Ja | Ange dina GitHub-autentiseringsuppgifter. |

När du har skapat anslutningen kan du köra åtgärderna och lyssna efter de utlösare som beskrivs i den här artikeln.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/github/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)