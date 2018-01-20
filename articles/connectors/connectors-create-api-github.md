---
title: GitHub-anslutningen i Azure Logic Apps | Microsoft Docs
description: "Skapa logikappar med Azure App service. GitHub är en webbaserad Git-lagringsplats värd för tjänsten. Den erbjuder alla distribuerade revision kontroll- och koden management (SCM) funktioner av Git samt lägga till egna funktioner."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: c9120babaa5f6da4f33bd60ba27434e24cb2f45e
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-github-connector"></a>Kom igång med GitHub-koppling
GitHub är en webbaserad Git-lagringsplats värd för tjänsten. Den erbjuder alla distribuerade revision kontroll- och koden management (SCM) funktioner av Git samt lägga till egna funktioner.

Du kan komma igång genom att skapa en logikapp nu, se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Skapa en anslutning till GitHub
För att skapa logikappar med GitHub, måste du först skapa en **anslutning** ange detaljer för följande egenskaper: 

| Egenskap | Krävs | Beskrivning |
| --- | --- | --- |
| Token |Ja |Ange autentiseringsuppgifter för GitHub |

Du kan använda den köra åtgärder och lyssna för utlösare som beskrivs i den här artikeln när du har skapat anslutningen. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/github/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).