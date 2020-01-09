---
title: Azure Cloud Services Def. NetworkTrafficRules-schema | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: tgore03
ms.author: tagore
ms.openlocfilehash: e6d156810b9fdee69ddac122eec06db7267ddf36
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449043"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>NetworkTrafficRules-schema för Azure Cloud Services-definition
`NetworkTrafficRules`-noden är ett valfritt element i tjänst definitions filen som anger hur roller kommunicerar med varandra. Den begränsar vilka roller som har åtkomst till de interna slut punkterna för den aktuella rollen. `NetworkTrafficRules` är inte ett fristående element. den kombineras med två eller flera roller i en tjänst definitions fil.

Standard tillägget för tjänst definitions filen är. csdef.

> [!NOTE]
>  `NetworkTrafficRules`-noden är bara tillgänglig med Azure SDK version 1,3 eller senare.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Grundläggande tjänst definitions schema för nätverks trafik regler
Det grundläggande formatet för en tjänst definitions fil som innehåller definitioner av nätverks trafik är följande.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schema element
Noden `NetworkTrafficRules` i tjänst definitions filen innehåller dessa element, som beskrivs i detalj i följande avsnitt i det här avsnittet:

[NetworkTrafficRules Element](#NetworkTrafficRules)

[OnlyAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Mål element](#Destinations)

[RoleEndpoint-element](#RoleEndpoint)

AllowAllTraffic-element

[WhenSource Element](#WhenSource)

[FromRole-element](#FromRole)

##  <a name="NetworkTrafficRules"></a> NetworkTrafficRules Element
`NetworkTrafficRules`-elementet anger vilka roller som kan kommunicera med vilken slut punkt på en annan roll. En tjänst kan innehålla en `NetworkTrafficRules`-definition.

##  <a name="OnlyAllowTrafficTo"></a>OnlyAllowTrafficTo-element
Den `OnlyAllowTrafficTo` element beskriver en uppsättning slutpunkter för målet och de roller som kan kommunicera med dem. Du kan ange flera `OnlyAllowTrafficTo` noder.

##  <a name="Destinations"></a>Mål element
`Destinations`-elementet beskriver en samling RoleEndpoints än vad som kan kommunicera med.

##  <a name="RoleEndpoint"></a>RoleEndpoint-element
`RoleEndpoint`-elementet beskriver en slut punkt för en roll för att tillåta kommunikation med. Du kan ange flera `RoleEndpoint` element om det finns fler än en slut punkt på rollen.

| Attribut      | Typ     | Beskrivning |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Krävs. Namnet på slut punkten som tillåter trafik till.|
| `roleName`     | `string` | Krävs. Namnet på webb rollen som tillåter kommunikation till.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic-element
`AllowAllTraffic`-elementet är en regel som tillåter att alla roller kommunicerar med de slut punkter som definierats i noden `Destinations`.

##  <a name="WhenSource"></a> WhenSource Element
`WhenSource`-elementet beskriver en samling roller än vad som kan kommunicera med de slut punkter som definierats i noden `Destinations`.

| Attribut | Typ     | Beskrivning |
| --------- | -------- | ----------- |
| `matches` | `string` | Krävs. Anger den regel som ska användas när kommunikation tillåts. Det enda giltiga värdet är för närvarande `AnyRule`.|
  
##  <a name="FromRole"></a>FromRole-element
`FromRole`-elementet anger de roller som kan kommunicera med de slut punkter som definierats i noden `Destinations`. Du kan ange flera `FromRole` element om det finns mer än en roll som kan kommunicera med slut punkterna.

| Attribut  | Typ     | Beskrivning |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Krävs. Namnet på den roll som kommunikation ska tillåtas från.|

## <a name="see-also"></a>Se även
[Definitions schema för moln tjänst (klassisk)](schema-csdef-file.md)




