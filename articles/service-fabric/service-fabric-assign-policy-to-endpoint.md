---
title: Tilldela principer för åtkomst till Azure Service Fabric-tjänstslutpunkter | Microsoft Docs
description: Lär dig hur du tilldelar säkerhet åtkomstprinciper till HTTP eller HTTPS-slutpunkter i Service Fabric-tjänsten.
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
ms.openlocfilehash: 8d5017cbd2177d080e5cef3d99a9f6b62eae08d5
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978969"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Tilldela en säkerhetsåtkomstprincip för HTTP och HTTPS-slutpunkter
Om du använder en Kör som-princip och tjänstmanifestet deklarerar HTTP-slutpunkt resurser, måste du ange en **SecurityAccessPolicy**.  **SecurityAccessPolicy** säkerställer att portar som allokerats till de här slutpunkterna är begränsad till det användarkonto som tjänsten körs under korrekt. I annat fall **http.sys** inte åtkomst till tjänsten och du får fel med anrop från klienten. I följande exempel gäller Customer1-konto till en slutpunkt som kallas **EndpointName**, vilket ger det fullständiga åtkomsträttigheter.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

För en HTTPS-slutpunkt kan du också ange namnet på certifikatet som ska gå tillbaka till klienten. Du refererar till certifikatet med hjälp av **EndpointBindingPolicy**.  Certifikatet har definierats i den **certifikat** avsnittet i programmanifestet.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> När du använder HTTPS, Använd inte samma port och certifikat för olika tjänstinstanser (oberoende av programmet) som distribuerats till samma nod. Uppgradera två olika tjänster som använder samma port i olika programinstanser resulterar i en uppgraderingen skulle misslyckas. Mer information finns i [uppgradera flera program med HTTPS-slutpunkter ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged--> Nästa steg finns i följande artiklar:
* [Förstå programmodellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
