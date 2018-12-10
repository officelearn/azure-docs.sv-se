---
title: Lagra och använda Azure Service Fabric Mesh-programhemligheter | Microsoft Docs
description: Lagra och använda Service Fabric Mesh-hemligheter.
services: service-fabric-mesh
keywords: hemligheter
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 63ac34e184d537eba2b915d4d108c7c1d8368aba
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891980"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh-programhemligheter
Service Fabric Mesh stöder hemligheter som Azure-resurser. En Service Fabric Mesh-hemlighet kan vara valfri känslig textinformation, till exempel lagringsanslutningssträngar, lösenord eller andra värden som bör lagras och överföras på ett säkert sätt.

![Översikt över Mesh-hemligheter][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Resurser för Mesh-hemligheter
En Mesh-programhemlighet består av:
* En **hemlighetsresurs**, vilket är en container som lagrar texthemligheter. Hemligheter som ingår i **hemlighetsresursen** lagras och överförs på ett säkert sätt.
* En eller flera **hemlighets-/värderesurser** som lagras i **hemlighetsresurscontainern**. Varje **hemlighets-/värderesurs** identifieras med ett versionsnummer.

## <a name="inline-or-stored-in-azure-key-vault"></a>Infogad eller lagras i Azure Key Vault
- Mesh-program och tjänstresurser omfattar hanterad tjänstidentitet (MSI) med Azure Active Directory (AAD) för att kunna komma åt hemligheter i Azure Key Vault.
- Hemligheter och certifikat kan förlängas automatiskt med principer.

## <a name="next-steps"></a>Nästa steg 
Mer information om Service Fabric Mesh-hemligheter finns i följande avsnitt:
- [Hantera Service Fabric Mesh-programhemligheter](service-fabric-mesh-howto-manage-secrets.md)
- [Introduktion till Service Fabric Resource Model](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
