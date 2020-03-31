---
title: Tilldela åtkomstprinciper till tjänstslutpunkter
description: Lär dig hur du tilldelar säkerhetsåtkomstpoliser till HTTP- eller HTTPS-slutpunkter i tjänsten Service Fabric.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614663"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Tilldela en säkerhetsåtkomstprincip för HTTP- och HTTPS-slutpunkter
Om du tillämpar en körningsprincip och tjänstmanifestet deklarerar HTTP-slutpunktsresurser måste du ange en **SecurityAccessPolicy**.  **SecurityAccessPolicy** säkerställer att portar som allokerats till dessa slutpunkter är korrekt begränsade till användarkontot som tjänsten körs som. Annars har **http.sys** inte åtkomst till tjänsten och du får fel med samtal från klienten. I följande exempel tillämpas Customer1-kontot på en slutpunkt som heter **EndpointName**, vilket ger det fullständiga åtkomsträttigheter.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

För en HTTPS-slutpunkt anger du också namnet på certifikatet som ska returneras till klienten. Du refererar till certifikatet med **EndpointBindingPolicy**.  Certifikatet definieras i avsnittet **Certifikat** i ansökningsmanifestet.

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
> När du använder HTTPS ska du inte använda samma port och certifikat för olika tjänstinstanser (oberoende av programmet) som distribueras till samma nod. Om du uppgraderar två olika tjänster med samma port i olika programinstanser kan det leda till ett uppgraderingsfel. Mer information finns i [Uppgradera flera program med HTTPS-slutpunkter ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Nästa steg finns i följande artiklar:
* [Förstå programmodellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
