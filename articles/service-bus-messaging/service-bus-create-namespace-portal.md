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
ms.date: 11/30/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 0b1f6f7ec47e47f39407cdbfd5efef2a18944ecc
ms.openlocfilehash: 89610512c264cef4bd52728240ddc0a34091edea


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Skapa ett Service Bus-namnområde med Azure Portal
Ett namnområde är en gemensam behållare för alla meddelandekomponenter. Flera köer och ämnen kan finnas i ett enda namnområde och namnområden fungerar ofta som programbehållare. Det finns för närvarande två sätt att skapa ett Service Bus-namnområde.

1. Azure Portal (den här artikeln)
2. [Resource Manager-mallar][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Skapa ett namnområde i Azure Portal
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Grattis! Du har nu skapat ett namnområde för meddelandetjänsten i Service Bus.

## <a name="next-steps"></a>Nästa steg
Kolla in våra [GitHub-exempel][github-samples] som visar några av de mer avancerade funktionerna i meddelandetjänsten i Azure Service Bus.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples



<!--HONumber=Dec16_HO1-->


