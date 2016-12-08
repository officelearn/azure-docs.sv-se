---
title: "Skapa ett Service Bus-namnområde med Azure Portal | Microsoft Docs"
description: "För att komma igång med Service Bus, behöver du ett namnområde. Så här skapar du ett med hjälp av Azure Portal."
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
ms.date: 08/22/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 9ace119de3676bcda45d524961ebea27ab093415
ms.openlocfilehash: 5fa107c857b3291cf2687cb4097649e23e9f95da


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Skapa ett Service Bus-namnområde med Azure Portal
Ett namnområde är en gemensam behållare för alla dina meddelandekomponenter. Flera köer och ämnen kan finnas i ett enda namnområde och namnområden fungerar ofta som programbehållare. Det finns för närvarande två sätt att skapa ett Service Bus-namnområde.

1. Azure Portal (den här artikeln)
2. [Resource Manager-mallar][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Skapa ett namnområde i Azure Portal
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Grattis! Du har nu skapat ett namnområde för meddelandetjänsten i Service Bus.

## <a name="next-steps"></a>Nästa steg
Kolla in våra [GitHub-exempel][github-exempel] som visar några av de mer avancerade funktionerna i Azure Service Bus-meddelandefunktionen.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-exempel]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples



<!--HONumber=Nov16_HO3-->


