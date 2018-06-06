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
ms.openlocfilehash: 33d6d83d4dcd0ec9bebf8d4197684e0e52c48ac5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701326"
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

> [!WARNING] 
> När du använder HTTPS, Använd inte samma port och certifikat för olika tjänstinstanser (oberoende av programmet) distribueras till samma nod. Uppgradera två olika tjänster som använder samma port i annat fall leder en uppgraderar. Mer information finns i [uppgraderar flera program med HTTPS-slutpunkter ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Läs följande artiklar för nästa steg:
* [Förstå programmet modellen](service-fabric-application-model.md)
* [Ange resurser i en tjänstmanifestet](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
