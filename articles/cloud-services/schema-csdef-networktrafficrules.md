---
title: Azure Cloud Services Def. NetworkTrafficRules schemat | Microsoft Docs
ms.custom: 
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: "17"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 37ec6c771ad83be680d95e9b5597d8da3cace9ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services Definition NetworkTrafficRules Schema
Den `NetworkTrafficRules` noden är ett valfritt element i tjänstdefinitionsfilen som anger hur roller kommunicerar med varandra. Det begränsar vilka roller kan komma åt interna slutpunkter för den specifika-rollen. Den `NetworkTrafficRules` är inte ett fristående element; den kombineras med två eller flera roller i en tjänstdefinitionsfilen.

Standardfilnamnstillägget för tjänstdefinitionsfilen är .csdef.

> [!NOTE]
>  Den `NetworkTrafficRules` noden är endast tillgängliga med hjälp av Azure SDK-version 1.3 eller senare.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Grundläggande service definition schemat för regler för nätverkstrafik
Det grundläggande formatet för en tjänstdefinitionsfilen som innehåller definitioner för nätverk trafiken är som följer.

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

[AllowAllTraffic Element](#AllowAllTraffic)

[WhenSource Element](#WhenSource)

[FromRole Element](#FromRole)

##  <a name="NetworkTrafficRules"></a>NetworkTrafficRules Element
Den `NetworkTrafficRules` element anger vilka roller kan kommunicera med vilken slutpunkt på en annan roll. En tjänst kan innehålla en `NetworkTrafficRules` definition.

##  <a name="OnlyAllowTrafficTo"></a>OnlyAllowTrafficTo Element
Den `OnlyAllowTrafficTo` element beskriver en uppsättning slutpunkter för målet och de roller som kan kommunicera med dem. Du kan ange flera `OnlyAllowTrafficTo` noder.

##  <a name="Destinations"></a>Mål-Element
Den `Destinations` element beskriver en uppsättning RoleEndpoints än vad som kan överföras med.

##  <a name="RoleEndpoint"></a>RoleEndpoint Element
Den `RoleEndpoint` element beskriver en slutpunkt för en roll som tillåter kommunikation med. Du kan ange flera `RoleEndpoint` element om det finns mer än en slutpunkt på rollen.

| Attribut      | Typ     | Beskrivning |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Krävs. Namnet på slutpunkten så att trafik till.|
| `roleName`     | `string` | Krävs. Namnet på rollen webbserver att tillåta kommunikation till.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic Element
Den `AllowAllTraffic` elementet är en regel som tillåter alla roller att kommunicera med slutpunkter som definierats i den `Destinations` nod.

##  <a name="WhenSource"></a>WhenSource Element
Den `WhenSource` element beskriver en samling av roller än vad som kan kommunicera med slutpunkter som definierats i den `Destinations` nod.

| Attribut | Typ     | Beskrivning |
| --------- | -------- | ----------- |
| `matches` | `string` | Krävs. Anger att regeln ska gälla när tillåter kommunikation. Det enda giltiga värdet är för närvarande `AnyRule`.|
  
##  <a name="FromRole"></a>FromRole Element
Den `FromRole` element anger de roller som kan kommunicera med slutpunkter som definierats i den `Destinations` nod. Du kan ange flera `FromRole` element om det finns fler än en roll som kan kommunicera med slutpunkter.

| Attribut  | Typ     | Beskrivning |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Krävs. Namn på rollen som du vill tillåta kommunikation.|

## <a name="see-also"></a>Se även
[Cloud Service (klassiskt) Definition av schemat](schema-csdef-file.md)