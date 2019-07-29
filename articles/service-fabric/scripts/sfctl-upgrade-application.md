---
title: Skriptexempel för Service Fabric CLI – Uppdatera ett program i ett kluster
description: Skriptexempel för Service Fabric CLI – Uppdatera ett program med en ny version. Det här exemplet uppgraderar också ett distribuerat program med de nya bitarna.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 72195a3a127e33ffa6118f77c4fa58ba5f60ee17
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600086"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Distribuera ett programcertifikat till ett Service Fabric-kluster

Exempelskriptet laddar upp en ny version av ett befintligt program och uppgraderar sedan ett distribuerat program med de nya bitarna.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Exempelskript

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Service Fabric CLI-dokumentationen](../service-fabric-cli.md).

Fler Service Fabric CLI-exempel för Azure Service Fabric finns i [Service Fabric CLI-exempel](../samples-cli.md).
