---
title: "Skapa ett Service Bus-namnområde med Azure Portal | Microsoft Docs"
description: "Så här skapar du ett Service Bus-namnområde med Azure Portal."
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/23/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 5de92033eb7eb4fef8d27a215b3284ab80594065
ms.lasthandoff: 03/24/2017


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Skapa ett Service Bus-namnområde med Azure Portal
Ett namnområde är en gemensam behållare för alla meddelandekomponenter. Flera köer och ämnen kan finnas i ett enda namnområde och namnområden fungerar ofta som programbehållare. Det finns två sätt att skapa ett Service Bus-namnområde:

1. Azure Portal (den här artikeln)
2. [Resource Manager-mallar][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Skapa ett namnområde i Azure Portal
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Grattis! Du har nu skapat ett namnområde för meddelandetjänsten i Service Bus.

## <a name="next-steps"></a>Nästa steg
Kolla in våra [GitHub-exempel][github-samples] som visar några av de mer avancerade funktionerna i meddelandetjänsten i Azure Service Bus.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

