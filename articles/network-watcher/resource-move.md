---
title: Flytta Azure Network Watcher-resurser | Microsoft Docs
description: Flytta Azure Network Watcher resurser mellan regioner
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 97349071fee6a95623e5b5efdc0c9818cfe7b811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388482"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Flytta Azure Network Watcher resurser mellan regioner

## <a name="moving-the-network-watcher-resource"></a>Flytta Network Watcher resursen
Network Watcher resursen representerar backend-tjänsten för Network Watcher och hanteras helt av Azure. Kunderna behöver inte hantera den. Flytt åtgärden stöds inte på den här resursen.

## <a name="moving-child-resources-of-network-watcher"></a>Flytta underordnade resurser för Network Watcher
Det finns för närvarande inte stöd för att flytta resurser mellan regioner för en underordnad resurs av `*networkWatcher*` resurs typen.

## <a name="next-steps"></a>Nästa steg
* Läs [Network Watcher översikt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)
* Se [vanliga frågor och svar om Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions)
