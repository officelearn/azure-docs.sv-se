---
title: Azure Resource Manager-mallexempel – Event Grid | Microsoft Docs
description: Azure Resource Manager-mallexempel för Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246278"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Azure Resource Manager-mallar för Event Grid

Följande tabell innehåller länkar till Azure Resource Manager-mallar för Event Grid.

| | |
|-|-|
|**Event Grid-prenumerationer**||
| [Anpassat ämne och en prenumeration med WebHook-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Distribuerar ett Event Grid-anpassat ämne. Skapar en prenumeration på detta anpassade ämne som använder en WebHook-slutpunkt. |
| [Prenumeration av anpassat ämne med EventHub-slutpunkt](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Skapar en Event Grid-prenumeration till ett anpassat ämne som redan finns. Prenumerationen använder en EventHub för slutpunkten. |
| [Prenumeration på resursgrupp](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| Prenumererar på händelser för en resursgrupp. Den resursgrupp som du anger som mål under distributionen är källan för händelser. Prenumerationen använder en EventHub för slutpunkten. |
| [Blob storage-konto och prenumeration](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Distribuerar ett Azure Blob storage-konto och prenumererar på händelser för detta lagringskonto. |
| | |
