---
title: NetworkConfiguration-Schema för Azure Cloud Services | Microsoft Docs
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
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: ed071d1da30a598eef830b4485c246ffae09c950
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004122"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services, Config NetworkConfiguration-Schema

Den `NetworkConfiguration` element i tjänstkonfigurationsfilen anger värden för virtuellt nätverk och DNS. De här inställningarna är valfria för molntjänster.

Du kan använda följande resurs om du vill veta mer om virtuella nätverk och associerade scheman:

- [Konfigurationsschema för cloud Service (klassisk)](schema-cscfg-file.md)
- [Molnet (klassisk) Tjänstdefinitionsschemat](schema-csdef-file.md)
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

I följande tabell beskrivs de underordnade elementen i den `NetworkConfiguration` element.

| Element       | Beskrivning |
| ------------- | ----------- |
| AccessControl | Valfri. Anger reglerna för åtkomst till slutpunkter i en molntjänst. Kontrollnamn åtkomst definieras av en sträng för `name` attribut. Den `AccessControl` elementet innehåller ett eller flera `Rule` element. Mer än en `AccessControl` element kan definieras.|
| Regel | Valfri. Anger den åtgärd som ska vidtas för en IP-adressintervall för undernätet. Ordningen på regeln definieras av ett strängvärde för den `order` attribut. Regeln-Ju lägre nummer desto högre prioritet. Regler kan till exempel anges med beställning nummer 100, 200 och 300. Regeln med ordningstalet 100 har företräde framför den regel som har en order på 200.<br /><br /> Åtgärden för regeln definieras av en sträng för den `action` attribut. Möjliga värden:<br /><br /> -   `permit` – Anger att endast paket från det angivna undernätsintervallet kan kommunicera med slutpunkten.<br />-   `deny` – Anger att åtkomst nekas till slutpunkterna i det angivna undernätsintervallet.<br /><br /> De IP-adressintervall för undernätet som påverkas av regeln definieras av en sträng för den `remoteSubnet` attribut. Beskrivningen av regeln definieras av en sträng för den `description` attribut.|
| EndpointAcl | Valfri. Anger tilldelningen av regler för åtkomstkontroll till en slutpunkt. Namnet på den roll som innehåller slutpunkten som definieras av en sträng för den `role` attribut. Namnet på slutpunkten som definieras av en sträng för den `endpoint` attribut. Namnet på uppsättningen `AccessControl` regler som ska tillämpas på slutpunkten som definieras i en sträng för den `accessControl` attribut. Mer än en `EndpointAcl` element kan definieras.|
| DnsServer | Valfri. Anger inställningar för en DNS-server. Du kan ange inställningar för DNS-servrar utan ett virtuellt nätverk. Namnet på DNS-servern definieras av en sträng för den `name` attribut. IP-adressen för DNS-servern definieras av en sträng för den `IPAddress` attribut. IP-adressen måste vara en giltig IPv4-adress.|
| VirtualNetworkSite | Valfri. Anger namnet på den virtuella nätverksplatsen som du vill distribuera din molntjänst. Den här inställningen skapar inte en virtuell nätverksplats. Den refererar till en plats som tidigare har definierats i nätverksfilen för det virtuella nätverket. En molnbaserad tjänst kan bara vara medlem i ett virtuellt nätverk. Om du inte anger den här inställningen kommer Molntjänsten inte distribueras till ett virtuellt nätverk. Namnet på den virtuella nätverksplatsen definieras av en sträng för den `name` attribut.|
| InstanceAddress | Valfri. Anger associationen mellan en roll till ett undernät eller uppsättning undernät i det virtuella nätverket. Du kan ange de undernät som du vill att den här rollen ska kopplas när du kopplar ett rollnamn till en instans-adress. Den `InstanceAddress` innehåller ett undernät-element. Namnet på den roll som är associerad med undernätet eller undernät definieras av en sträng för den `roleName` attribut.|
| Undernät | Valfri. Anger det undernät som motsvarar namnet på undernätet i nätverkskonfigurationsfilen. Namnet på undernätet definieras av en sträng för den `name` attribut.|
| ReservedIP | Valfri. Anger den reserverade IP-adressen som ska associeras med distributionen. Skapa reserverad IP-adress måste du använda för att skapa den reserverade IP-adressen. Varje distribution i en molntjänst kan associeras med en reserverad IP-adress. Namnet på den reserverade IP-adressen definieras av en sträng för den `name` attribut.|

## <a name="see-also"></a>Se även
[Konfigurationsschema för cloud Service (klassisk)](schema-cscfg-file.md)