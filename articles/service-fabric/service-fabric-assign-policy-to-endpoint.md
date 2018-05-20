---
title: Tilldela principer för åtkomst till Azure Service Fabric-Tjänsteslutpunkter | Microsoft Docs
description: Lär dig hur du tilldelar säkerhet principer för åtkomst till HTTP eller HTTPS-slutpunkter i Service Fabric-tjänsten.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: f9de8d213d11a8ccb3ffff484a67560d9e2abe77
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Tilldela en säkerhetsprincip åtkomst för HTTP och HTTPS-slutpunkter
Om du använder en Kör som-princip och service manifest deklarerar http-slutpunkt resurser, måste du ange en **SecurityAccessPolicy**.  **SecurityAccessPolicy** garanterar att portar som allokerats till dessa slutpunkter korrekt är begränsade till det användarkonto som tjänsten körs under. Annars **http.sys** inte har åtkomst till tjänsten och du får ett fel med anrop från klienten. I följande exempel gäller en slutpunkt som kallas kontot Customer1 **EndpointName**, vilket ger fullständig behörighet.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

För en HTTPS-slutpunkt, också ange namnet på certifikatet som ska returneras till klienten. Du refererar till certifikatet med hjälp av **EndpointBindingPolicy**.  Certifikatet har definierats i den **certifikat** avsnitt i programmanifestet.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Läs följande artiklar för nästa steg:
* [Förstå programmet modellen](service-fabric-application-model.md)
* [Ange resurser i en tjänstmanifestet](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
