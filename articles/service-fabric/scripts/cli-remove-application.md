---
title: Azure Service Fabric CLI (sfctl) skript för att ta bort exempel
description: Ta bort ett program från ett Azure Service Fabric-kluster med hjälp av Azure Service Fabric-CLI
services: service-fabric
documentationcenter: ''
author: thraka
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 83f2fc52debd24afd97a391466cb5a0b1a8cd93c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642724"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Ta bort ett program från ett Service Fabric-kluster

Det här exempelskriptet tar bort en instans som kör en Service Fabric-instans och avregistrerar en programtyp och version från klustret.  Om du tar bort en programinstans raderas även de tjänstinstanser som körs som associeras med det programmet. Därefter tas programmets filer bort från avbildningsarkivet. 

Installera också [Service Fabric CLI](../service-fabric-cli.md).

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Service Fabric CLI-dokumentationen](../service-fabric-cli.md).

Fler Service Fabric CLI-exempel för Azure Service Fabric finns i [Service Fabric CLI-exempel](../samples-cli.md).
