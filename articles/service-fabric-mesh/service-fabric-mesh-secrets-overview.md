---
title: Lagra och använda Azure Service Fabric nät program hemligheter
description: Service Fabric Mesh stöder hemligheter som Azure-resurser. Så här lagrar och hanterar du hemligheter med Service Fabric nätappar.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: da4eaa34840f14714616b85e24fd62cf65602b84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277625"
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
