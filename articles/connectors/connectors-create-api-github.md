---
title: Ansluta till GitHub - Azure Logikappar | Microsoft Docs
description: 'Övervaka GitHub-händelser med GitHub REST API: er och Azure Logic Apps'
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ce567dc631c3a147b795eb2355a4961faa8881d6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295820"
---
# <a name="connect-to-github"></a>Ansluta till GitHub

GitHub är en webbaserad Git-lagringsplatsen värd tjänst som erbjuder alla distribuerade revision kontrollen och källa kod (SCM) hanteringsfunktioner i Git samt andra funktioner.

Du kommer igång med GitHub-kopplingen [först skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Skapa en anslutning till GitHub

Om du vill använda GitHub-kopplingen i en logikapp, måste du först skapa en *anslutning* och ange information för dessa egenskaper: 

| Egenskap  | Krävs | Beskrivning | 
| -------- | -------- | ----------- | 
| Token | Ja | Ange dina autentiseringsuppgifter för GitHub. |

När du skapar anslutningen kan du utföra åtgärderna och lyssna efter utlösare som beskrivs i den här artikeln.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Connector-specifik information

Utlösare och åtgärder som definierats i Swagger och alla gränser, granska den [connector information](/connectors/github/).

## <a name="find-more-connectors"></a>Sök efter fler kopplingar

* Granska de [kopplingar listan](apis-list.md).