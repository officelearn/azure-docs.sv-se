---
title: Skriptexempel för Service Fabric CLI – Lista ett program i ett kluster
description: Service Fabric CLI-skriptexempel – Visa en lista över de program som har etablerats i ett Service Fabric-kluster.
services: service-fabric
documentationcenter: ''
author: TylerMSFT
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: twhitney
ms.custom: ''
ms.openlocfilehash: bea88460ecaa093a0719d627e3608fd1d530727b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070206"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Visa en lista över program som körs i ett Service Fabric-kluster

Det här skriptexemplet ansluter till ett Service Fabric-kluster och visar sedan en lista över alla etablerade program.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Service Fabric CLI-dokumentationen](../service-fabric-cli.md).

Fler Service Fabric CLI-exempel för Azure Service Fabric finns i [Service Fabric CLI-exempel](../samples-cli.md).
