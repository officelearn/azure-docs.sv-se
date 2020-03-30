---
title: Azure Cloud Services Def. NetworkTrafficRules Schema | Microsoft-dokument
description: Lär dig mer om NetworkTrafficRules, som begränsar de roller som kan komma åt de interna slutpunkterna för en roll. Den kombineras med roller i en tjänstdefinitionsfil.
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
ms.openlocfilehash: e53c10395ec3168e656633cc43fb2d01902209fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534736"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Definition av Azure Cloud Services-nätverksnätverkTrafficRules-schema
Noden `NetworkTrafficRules` är ett valfritt element i tjänstdefinitionsfilen som anger hur roller kommunicerar med varandra. Det begränsar vilka roller som kan komma åt de interna slutpunkterna för den specifika rollen. Det `NetworkTrafficRules` är inte ett fristående element; Det kombineras med två eller flera roller i en tjänstdefinitionsfil.

Standardtillägget för tjänstdefinitionsfilen är .csdef.

> [!NOTE]
>  Noden `NetworkTrafficRules` är endast tillgänglig med Azure SDK version 1.3 eller senare.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Grundläggande tjänstdefinitionsschema för nätverkstrafikreglerna
Det grundläggande formatet för en tjänstdefinitionsfil som innehåller definitioner av nätverkstrafik är följande.

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

## <a name="schema-elements"></a>Schemaelement
Noden `NetworkTrafficRules` i tjänstdefinitionsfilen innehåller dessa element, som beskrivs i detalj i efterföljande avsnitt i det här avsnittet:

[NetworkTrafficRules Element](#NetworkTrafficRules)

[EndastAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Element för destinationer](#Destinations)

[RollEndpoint-element](#RoleEndpoint)

AllowAllTraffic Element

[Närkälla element](#WhenSource)

[FromRole Element](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a>NetworkTrafficRules Element
Elementet `NetworkTrafficRules` anger vilka roller som kan kommunicera med vilken slutpunkt på en annan roll. En tjänst kan `NetworkTrafficRules` innehålla en definition.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a>EndastAllowTrafficTo Element
Elementet `OnlyAllowTrafficTo` beskriver en samling målslutpunkter och de roller som kan kommunicera med dem. Du kan `OnlyAllowTrafficTo` ange flera noder.

##  <a name="destinations-element"></a><a name="Destinations"></a>Element för destinationer
Elementet `Destinations` beskriver en samling RoleEndpoints än vad som kan kommuniceras med.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a>RollEndpoint-element
Elementet `RoleEndpoint` beskriver en slutpunkt på en roll som kommunikationen kan tillåtas. Du kan `RoleEndpoint` ange flera element om det finns mer än en slutpunkt på rollen.

| Attribut      | Typ     | Beskrivning |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Krävs. Namnet på slutpunkten som trafik kan tillåtas.|
| `roleName`     | `string` | Krävs. Namnet på webbrollen som du vill tillåta kommunikation.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic Element
Elementet `AllowAllTraffic` är en regel som gör att alla roller `Destinations` kan kommunicera med slutpunkterna som definierats i noden.

##  <a name="whensource-element"></a><a name="WhenSource"></a>Närkälla element
Elementet `WhenSource` beskriver en samling roller än vad som kan `Destinations` kommunicera med slutpunkterna som definierats i noden.

| Attribut | Typ     | Beskrivning |
| --------- | -------- | ----------- |
| `matches` | `string` | Krävs. Anger vilken regel som ska tillämpas när kommunikation tillåts. Det enda giltiga `AnyRule`värdet är för närvarande .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a>FromRole Element
Elementet `FromRole` anger de roller som kan kommunicera med `Destinations` slutpunkterna som definierats i noden. Du kan `FromRole` ange flera element om det finns fler än en roll som kan kommunicera med slutpunkterna.

| Attribut  | Typ     | Beskrivning |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Krävs. Namnet på rollen som kommunikation ska tillåtas.|

## <a name="see-also"></a>Se även
[Molntjänst (klassiskt) definitionsschema](schema-csdef-file.md)




