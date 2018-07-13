---
title: Så skapar du ett Service Bus-namnområde i Azure Portal | Microsoft Docs
description: Skapa ett Service Bus-namnområde med Azure Portal.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/29/2018
ms.author: sethm
ms.openlocfilehash: 2763e401454cdb6145067a3ac415c3a252d7c494
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131675"
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Skapa ett Service Bus-namnområde med Azure Portal

Ett namnområde är en omfångsbehållare för alla meddelandekomponenter. Flera köer och ämnen kan finnas i ett enda namnområde och namnområden fungerar ofta som programbehållare. Det finns två sätt att skapa ett Service Bus-namnområde:

1. Azure Portal (den här artikeln)
2. [Resource Manager-mallar][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Skapa ett namnområde i Azure Portal

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Grattis! Du har nu skapat ett namnområde för meddelandetjänsten i Service Bus.

## <a name="next-steps"></a>Nästa steg

Kolla in våra [GitHub-exempel][github-samples] för Service Bus som visar några av de mer avancerade funktionerna i Service Bus-meddelandetjänsten.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
