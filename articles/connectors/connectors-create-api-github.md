---
title: Ansluta till GitHub – Azure Logic Apps | Microsoft Docs
description: 'Övervaka GitHub händelser med GitHub REST API: er och Azure Logic Apps'
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
ms.openlocfilehash: 0d2ff9368bc244a5afd6fafc40cf476b90a80a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462600"
---
# <a name="connect-to-github"></a>Anslut till GitHub

GitHub är en webbaserad Git-lagringsplats värdtjänst som erbjuder alla distribuerad revideringskontroll och källa kod management (SCM)-funktionerna i Git samt andra funktioner.

Du kommer igång med GitHub-anslutningsapp [först skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Skapa en anslutning till GitHub

Om du vill använda GitHub-anslutningsapp i en logikapp, måste du först skapa en *anslutning* och ange information för de här egenskaperna: 

| Egenskap  | Krävs | Beskrivning | 
| -------- | -------- | ----------- | 
| Token | Ja | Ange dina autentiseringsuppgifter för GitHub. |

När du har skapat anslutningen kan du köra åtgärderna och lyssna efter utlösare som beskrivs i den här artikeln.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Information om specifika

För utlösare och åtgärder som definierats i Swagger och eventuella gränser kan du granska den [anslutningsinformationen](/connectors/github/).

## <a name="find-more-connectors"></a>Sök efter fler anslutningar

* Granska den [lista över Anslutningsappar](apis-list.md).