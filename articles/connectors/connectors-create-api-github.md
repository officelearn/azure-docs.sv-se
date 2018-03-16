---
title: Ansluta till GitHub med Azure Logikappar | Microsoft Docs
description: "Automatisera arbetsflöden för GitHub med Azure Logikappar"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/02/2018
ms.author: estfan; ladocs
ms.openlocfilehash: 830c3e6b5462c5d2d917388c0d0924e444342f93
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="connect-to-github"></a>Ansluta till GitHub

GitHub är en webbaserad Git-lagringsplatsen värd tjänst som erbjuder alla distribuerade revision kontrollen och källa kod (SCM) hanteringsfunktioner i Git samt andra funktioner.

Du kommer igång med GitHub-kopplingen [först skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Skapa en anslutning till GitHub

Om du vill använda GitHub-kopplingen i en logikapp, måste du först skapa en *anslutning* och ange information för dessa egenskaper: 

| Egenskap | Krävs | Beskrivning | 
| -------- | -------- | ----------- | 
| Token | Ja | Ange dina autentiseringsuppgifter för GitHub. |

När du skapar anslutningen kan du utföra åtgärderna och lyssna efter utlösare som beskrivs i den här artikeln.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Connector-specifik information

Utlösare och åtgärder som definierats i Swagger och alla gränser, granska den [connector information](/connectors/github/).

## <a name="find-more-connectors"></a>Sök efter fler kopplingar

* Granska de [kopplingar listan](apis-list.md).