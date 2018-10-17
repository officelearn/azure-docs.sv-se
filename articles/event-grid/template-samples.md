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
ms.date: 09/18/2018
ms.author: tomfitz
ms.openlocfilehash: c462334597b41b914b6a0a0e3c8a67ad97e2bd7a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295590"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Azure Resource Manager-mallar för Event Grid

Följande tabell innehåller länkar till Azure Resource Manager-mallar för Event Grid.

| | |
|-|-|
|**Event Grid-prenumerationer**||
| [Anpassat ämne och en prenumeration med WebHook-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Distribuerar ett Event Grid-anpassat ämne. Skapar en prenumeration på detta anpassade ämne som använder en WebHook-slutpunkt. |
| [Prenumeration av anpassat ämne med EventHub-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Skapar en Event Grid-prenumeration till ett anpassat ämne. Prenumerationen använder en EventHub för slutpunkten. |
| [Azure-prenumeration eller prenumeration på resursgrupp](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Prenumerera på händelser för en resursgrupp eller Azure-prenumeration. Den resursgrupp som du anger som mål under distributionen är källan för händelser. Prenumerationen använder en WebHook för slutpunkten. |
| [Blob storage-konto och prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Distribuerar ett Azure Blob storage-konto och prenumererar på händelser för detta lagringskonto. |
| | |
