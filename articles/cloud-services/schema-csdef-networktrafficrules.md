---
title: Azure Cloud Services Def. NetworkTrafficRules-Schema | Microsoft Docs
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
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 8925943b0a5d151d55adedcfe3f01b5a14c63c1b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821702"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services Definition NetworkTrafficRules Schema
Den `NetworkTrafficRules` nod är ett valfritt element i tjänstdefinitionsfilen som anger hur roller kommunicerar med varandra. Det begränsar vilka roller som kan komma åt interna slutpunkterna för den specifika-rollen. Den `NetworkTrafficRules` är inte ett fristående element; den kombineras med två eller flera roller i en tjänstdefinitionsfilen.

Standardfilnamnstillägget för tjänstdefinitionsfilen är .csdef.

> [!NOTE]
>  Den `NetworkTrafficRules` noden är endast tillgängligt med hjälp av Azure SDK-version 1.3 eller högre.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Grundläggande tjänstdefinitionsschemat för regler för nätverkstrafik
Det grundläggande formatet för en tjänstdefinitionsfilen som innehåller definitioner för nätverk-trafik är som följer.

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
Den `NetworkTrafficRules` nod i tjänstdefinitionsfilen innehåller de här elementen beskrivs detaljerat i följande avsnitt i det här avsnittet:

[NetworkTrafficRules Element](#NetworkTrafficRules)

[OnlyAllowTrafficTo Element](#OnlyAllowTrafficTo)

[Mål-Element](#Destinations)

[RoleEndpoint Element](#RoleEndpoint)

AllowAllTraffic Element

[WhenSource Element](#WhenSource)

[FromRole Element](#FromRole)

##  <a name="NetworkTrafficRules"></a> NetworkTrafficRules Element
Den `NetworkTrafficRules` elementet anger vilka roller kan kommunicera med vilken slutpunkt på en annan roll. En tjänst kan innehålla en `NetworkTrafficRules` definition.

##  <a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo Element
Den `OnlyAllowTrafficTo` element beskriver en uppsättning slutpunkter för målet och de roller som kan kommunicera med dem. Du kan ange flera `OnlyAllowTrafficTo` noder.

##  <a name="Destinations"></a> Mål-Element
Den `Destinations` element beskriver en uppsättning RoleEndpoints än vad som kan förmedlas med.

##  <a name="RoleEndpoint"></a> RoleEndpoint Element
Den `RoleEndpoint` element beskriver en slutpunkt för en roll som tillåter kommunikation med. Du kan ange flera `RoleEndpoint` element om det finns mer än en slutpunkt på rollen.

| Attribut      | Type     | Beskrivning |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Krävs. Namnet på slutpunkten att tillåta trafik till.|
| `roleName`     | `string` | Krävs. Namnet på web-roll att tillåta kommunikation till.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic Element
Den `AllowAllTraffic` elementet är en regel som tillåter alla roller att kommunicera med slutpunkter som definierats i den `Destinations` noden.

##  <a name="WhenSource"></a> WhenSource Element
Den `WhenSource` element beskriver en uppsättning roller än vad som kan kommunicera med slutpunkter som definierats i den `Destinations` noden.

| Attribut | Type     | Beskrivning |
| --------- | -------- | ----------- |
| `matches` | `string` | Krävs. Anger regeln ska gälla när tillåter kommunikation. Det enda giltiga värdet är för närvarande `AnyRule`.|
  
##  <a name="FromRole"></a> FromRole Element
Den `FromRole` elementet anger de roller som kan kommunicera med slutpunkter som definierats i den `Destinations` noden. Du kan ange flera `FromRole` element om det finns mer än en roll som kan kommunicera med slutpunkter.

| Attribut  | Type     | Beskrivning |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Krävs. Namnet på rollen som du vill tillåta kommunikation från.|

## <a name="see-also"></a>Se även
[Molnet (klassisk) Tjänstdefinitionsschemat](schema-csdef-file.md)
