---
title: Skriptexempel för Service Fabric CLI – Lista ett program i ett kluster
description: Service Fabric CLI-skriptexempel – Visa en lista över de program som har etablerats i ett Service Fabric-kluster.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: aljo
ms.custom: ''
ms.openlocfilehash: 8fd83190f3cf92ef0f88ff0fb2a807e03199a5c1
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670293"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Visa en lista över program som körs i ett Service Fabric-kluster

Det här skriptexemplet ansluter till ett Service Fabric-kluster och visar sedan en lista över alla etablerade program.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Service Fabric CLI-dokumentationen](../service-fabric-cli.md).

Fler Service Fabric CLI-exempel för Azure Service Fabric finns i [Service Fabric CLI-exempel](../samples-cli.md).
