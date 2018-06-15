---
title: Azure Cloud Services NetworkConfiguration schemat | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: c1b94a9e-46e8-4a18-ac99-343c94b1d4bd
caps.latest.revision: 28
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: ebe81b2e4dea347eb22b173ff1e9baf1ee6bb75d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359653"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services Config NetworkConfiguration Schema

Den `NetworkConfiguration` element i tjänstkonfigurationsfilen anger värden för virtuella nätverk och DNS. De här inställningarna är valfria för molntjänster.

Du kan använda följande resurs för att lära dig mer om virtuella nätverk och associerade scheman:

- [Cloud Service (klassiskt) Konfigurationsschemat](schema-cscfg-file.md)
- [Cloud Service (klassiskt) Definition av schemat](schema-csdef-file.md)
- [Skapa ett virtuellt nätverk (klassisk)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration-elementet
I följande exempel visas den `NetworkConfiguration` elementet och dess underordnade element.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="<site-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

I följande tabell beskrivs underordnade element i `NetworkConfiguration` element.

| Element       | Beskrivning |
| ------------- | ----------- |
| AccessControl | Valfri. Anger regler för åtkomst till slutpunkter i en tjänst i molnet. Kontrollnamnet åtkomst definieras av en sträng för `name` attribut. Den `AccessControl` elementet innehåller ett eller flera `Rule` element. Mer än en `AccessControl` element kan definieras.|
| Regel | Valfri. Anger den åtgärd som ska vidtas för ett intervall med IP-adresser för angivna undernätet. Ordningen på regeln definieras av ett strängvärde för den `order` attribut. Regeln nummer Ju lägre nummer desto högre prioritet. Regler kan till exempel anges med ordernummer 100, 200 och 300. Regeln med ordern antalet 100 företräde framför en regel med en order 200.<br /><br /> Åtgärden för regeln definieras av en sträng för den `action` attribut. Möjliga värden:<br /><br /> -   `permit` – Anger att endast paket från det angivna undernät intervallet kan kommunicera med slutpunkten.<br />-   `deny` – Anger att åtkomst nekas till slutpunkterna i det angivna undernät intervallet.<br /><br /> Undernät intervallet av IP-adresser som påverkas av regeln definieras av en sträng för den `remoteSubnet` attribut. Beskrivning av regeln definieras av en sträng för den `description` attribut.|
| EndpointAcl | Valfri. Anger tilldelning av regler för åtkomstkontroll till en slutpunkt. Namnet på rollen som innehåller slutpunkten definieras av en sträng för den `role` attribut. Namnet på slutpunkten definieras av en sträng för den `endpoint` attribut. Namnet på uppsättningen `AccessControl` regler som ska kopplas till slutpunkten har definierats i en sträng för den `accessControl` attribut. Mer än en `EndpointAcl` element kan definieras.|
| DnsServer | Valfri. Anger inställningar för en DNS-server. Du kan ange inställningar för DNS-servrar utan ett virtuellt nätverk. Namnet på DNS-servern har definierats som en sträng för den `name` attribut. IP-adressen för DNS-server har definierats som en sträng för den `IPAddress` attribut. IP-adressen måste vara en giltig IPv4-adress.|
| VirtualNetworkSite | Valfri. Anger namnet på den virtuella nätverksplatsen där du vill distribuera din tjänst i molnet. Den här inställningen kan inte skapa en virtuell nätverksplats. Den refererar till en plats som tidigare har definierats i nätverksfilen för det virtuella nätverket. En molnbaserad tjänst kan bara vara medlem i ett virtuellt nätverk. Om du inte anger den här inställningen distribueras Molntjänsten inte till ett virtuellt nätverk. Namnet på den virtuella nätverksplatsen definieras av en sträng för den `name` attribut.|
| InstanceAddress | Valfri. Anger association av en roll till ett undernät eller uppsättning undernät i det virtuella nätverket. Du kan ange de undernät som du vill att rollen ska kopplas när du kopplar ett rollnamn till en instans-adress. Den `InstanceAddress` innehåller ett undernät-element. Namnet på rollen som associeras med undernät eller undernät definieras av en sträng för den `roleName` attribut.|
| Undernät | Valfri. Anger det undernät som motsvarar undernätsnamn i konfigurationsfilen på nätverket. Namnet på undernätet definieras av en sträng för den `name` attribut.|
| Reserverad IP-adress | Valfri. Anger den reserverade IP-adressen som ska associeras med distributionen. Skapa reserverad IP-adress måste du använda för att skapa den reserverade IP-adressen. Varje distribution i en molnbaserad tjänst kan associeras med en reserverad IP-adress. Namnet på den reserverade IP-adressen definieras av en sträng för den `name` attribut.|

## <a name="see-also"></a>Se även
[Cloud Service (klassiskt) Konfigurationsschemat](schema-cscfg-file.md)