---
title: Azure Service Fabric CLI (sfctl) skriptdistribueringsexempel
description: Distribuera ett program till ett Azure Service Fabric-kluster med hjälp av Azure Service Fabric-CLI
services: service-fabric
documentationcenter: ''
author: TylerMSFT
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/16/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: d3e619750c45ac2d8e0b942a304aadfa05301624
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069513"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Distribuera ett program till ett Service Fabric-kluster

Det här exempelskriptet kopierar ett programpaket till ett klusteravbildningsarkiv, registrerar programtypen i klustret och skapar en instans av programmet från programtypen. Alla standardtjänster skapas också vid samma tillfälle.

Installera också [Service Fabric CLI](../service-fabric-cli.md).

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Rensa distribution

När du är klar kan [borttagningsskriptet](cli-remove-application.md) användas för att ta bort programmet. Borttagningsskriptet tar bort programinstansen, avregistrerar programtypen och tar bort programpaketet från avbildningsarkivet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Service Fabric CLI-dokumentationen](../service-fabric-cli.md).

Fler Service Fabric CLI-exempel för Azure Service Fabric finns i [Service Fabric CLI-exempel](../samples-cli.md).
