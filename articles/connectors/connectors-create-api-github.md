---
title: Ansluta till GitHub med Azure Logikappar | Microsoft Docs
description: "Automatisera arbetsflöden för GitHub med Azure Logikappar"
services: logic-apps
documentationcenter: 
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
ms.date: 03/02/2018
ms.author: mandia; ladocs
ms.openlocfilehash: cd7cd3babbfb7efc5917d3a7ec5b9d10112ba791
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
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