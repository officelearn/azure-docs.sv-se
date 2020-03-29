---
title: Få tillgång till, övervaka och hantera din GitHub-repo
description: Övervaka GitHub-händelser och hantera din GitHub-repo genom att skapa automatiserade arbetsflöden med Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378457"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Övervaka och hantera din GitHub-repo med hjälp av Azure Logic Apps

GitHub är en webbaserad Git-lagringsplatsvärdtjänst som erbjuder alla funktioner för distribuerad revisionskontroll och källkodshantering (SCM) i Git plus andra funktioner.

Om du vill komma igång med GitHub-anslutningen [skapar du först en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Skapa en anslutning till GitHub

Om du vill använda GitHub-anslutningen i en logikapp måste du först skapa en *anslutning* och sedan ange information om dessa egenskaper: 

| Egenskap | Krävs | Beskrivning | 
| -------- | -------- | ----------- | 
| Token | Ja | Ange dina GitHub-autentiseringsuppgifter. |

När du har skapat anslutningen kan du utföra åtgärderna och lyssna efter de utlösare som beskrivs i den här artikeln.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i [kopplingens referenssida](/connectors/github/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)