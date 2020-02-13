---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 0177f1ffbc21cf5508b86a9c0f3c625fead34faf
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172942"
---
|Namn |Beskrivning |Påverkan (ar) |Version |
|---|---|---|---|
|[Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |Service Fabric tillhandahåller tre skyddsnivåer (ingen, logga in och EncryptAndSign) för nod-till-nod-kommunikation med hjälp av en primär klustercertifikat. Ange skydds nivå för att säkerställa att alla nod-till-nod-meddelanden är krypterade och digitalt signerade |Granskning, inaktive rad |1.0.0 |
|[Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |Granska användningen av klientautentisering endast via Azure Active Directory i Service Fabric |Granskning, inaktive rad |1.0.0 |
