---
title: Lagra och använda Azure Service Fabric Mesh-programhemligheter | Microsoft Docs
description: Lagra och använda Service Fabric Mesh-hemligheter.
services: service-fabric-mesh
keywords: hemligheter
author: v-steg
ms.author: jeconnoc
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: b5cfa93298222d914069b6ab63deb8ba8a9b59c3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875333"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh-programhemligheter
Service Fabric Mesh stöder hemligheter som Azure-resurser. En Service Fabric Mesh-hemlighet kan vara valfri känslig textinformation, till exempel lagringsanslutningssträngar, lösenord eller andra värden som bör lagras och överföras på ett säkert sätt.

![Översikt över Mesh-hemligheter][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Resurser för Mesh-hemligheter
En Mesh-programhemlighet består av:
* En **hemlighetsresurs**, vilket är en container som lagrar texthemligheter. Hemligheter som ingår i **hemlighetsresursen** lagras och överförs på ett säkert sätt.
* En eller flera **hemlighets-/värderesurser** som lagras i **hemlighetsresurscontainern**. Varje **hemlighets-/värderesurs** identifieras med ett versionsnummer.

## <a name="next-steps"></a>Nästa steg 
Mer information om Service Fabric Mesh-hemligheter finns i följande avsnitt:
- [Hantera Service Fabric Mesh-programhemligheter](service-fabric-mesh-howto-manage-secrets.md)
- [Introduktion till Service Fabric Resource Model](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
