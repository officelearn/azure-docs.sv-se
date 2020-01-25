---
title: Azure Resource Manager-mallexempel – Event Grid | Microsoft Docs
description: Den här artikeln innehåller en lista över Azure Resource Manager mal sampel för Azure Event Grid på GitHub.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720630"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Azure Resource Manager-mallar för Event Grid

JSON-syntaxen och egenskaperna som ska användas i en mall finns på sidan med [Microsoft.EventGrid-resurstyper](/azure/templates/microsoft.eventgrid/allversions). Följande tabell innehåller länkar till Azure Resource Manager-mallar för Event Grid.

| | |
|-|-|
|**Event Grid-prenumerationer**||
| [Anpassat ämne och en prenumeration med WebHook-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Distribuerar ett Event Grid-anpassat ämne. Skapar en prenumeration på detta anpassade ämne som använder en WebHook-slutpunkt. |
| [Prenumeration av anpassat ämne med EventHub-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Skapar en Event Grid-prenumeration till ett anpassat ämne. Prenumerationen använder en EventHub för slutpunkten. |
| [Azure-prenumeration eller prenumeration på resursgrupp](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Prenumerera på händelser för en resursgrupp eller Azure-prenumeration. Den resursgrupp som du anger som mål under distributionen är källan för händelser. Prenumerationen använder en WebHook för slutpunkten. |
| [Blob storage-konto och prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Distribuerar ett Azure Blob storage-konto och prenumererar på händelser för detta lagringskonto. |
| | |
