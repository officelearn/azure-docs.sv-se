---
title: "Så skapar du ett Service Bus-namnområde i Azure Portal | Microsoft Docs"
description: "Skapa ett Service Bus-namnområde med Azure Portal."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: a24fa21848005d9768d26ae865680a4851e1dd81
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Skapa ett Service Bus-namnområde med Azure Portal

Ett namnområde är en omfångsbehållare för alla meddelandekomponenter. Flera köer och ämnen kan finnas i ett enda namnområde och namnområden fungerar ofta som programbehållare. Det finns två sätt att skapa ett Service Bus-namnområde:

1. Azure Portal (den här artikeln)
2. [Resource Manager-mallar][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Skapa ett namnområde i Azure Portal

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Grattis! Du har nu skapat ett namnområde för meddelandetjänsten i Service Bus.

## <a name="next-steps"></a>Nästa steg

Kolla in våra [GitHub-exempel][github-samples] som visar några av de mer avancerade funktionerna i Service Bus-meddelandetjänsten.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
