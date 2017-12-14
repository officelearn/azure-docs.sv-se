---
title: "Skriptexempel för Service Fabric CLI - lista över program på ett kluster"
description: "Service Fabric CLI skriptexempel - visa en lista över de program som har etablerats på ett Service Fabric-kluster."
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: 
ms.openlocfilehash: f764c36bcc4905224242cbdf1602d7482e52efac
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Lista över program som körs i ett Service Fabric-kluster

Det här exempelskriptet ansluter till Service Fabric-klustret och visar en lista över alla etablerade program.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Service Fabric CLI dokumentationen](../service-fabric-cli.md).

Ytterligare Service Fabric CLI prover för Azure Service Fabric kan hittas i den [Service Fabric CLI exempel](../samples-cli.md).
