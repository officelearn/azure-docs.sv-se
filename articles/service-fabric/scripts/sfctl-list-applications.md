---
title: Skriptexempel för Service Fabric CLI – Lista ett program i ett kluster
description: Service Fabric CLI-skriptexempel – Visa en lista över de program som har etablerats i ett Service Fabric-kluster.
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: ''
ms.openlocfilehash: be7464cab4f9a1014fa129314c332bc41b2f499c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212950"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Visa en lista över program som körs i ett Service Fabric-kluster

Det här skriptexemplet ansluter till ett Service Fabric-kluster och visar en lista över alla etablerade program.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Service Fabric CLI-dokumentationen](../service-fabric-cli.md).

Fler Service Fabric CLI-exempel för Azure Service Fabric finns i [Service Fabric CLI-exempel](../samples-cli.md).
