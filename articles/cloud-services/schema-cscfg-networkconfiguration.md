---
title: Nätverkskonfigurationsschema för Azure Cloud Services | Microsoft-dokument
description: Lär dig mer om de underordnade elementen i networkconfiguration-elementet i tjänstkonfigurationsfilen, som anger värden för virtuellt nätverk och DNS.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: 695ba3acfd5af8797de6e6f7454e493d7863627c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529296"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Konfigurationsschema för Azure Cloud Services Config NetworkConfiguration

Elementet `NetworkConfiguration` i tjänstkonfigurationsfilen anger virtuella nätverk och DNS-värden. Dessa inställningar är valfria för molntjänster.

Du kan använda följande resurs för att lära dig mer om virtuella nätverk och tillhörande scheman:

- [Konfigurationsschema för molntjänst (klassiskt)](schema-cscfg-file.md)
- [Molntjänst (klassiskt) definitionsschema](schema-csdef-file.md)
- [Skapa ett virtuellt nätverk (klassiskt)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Element för nätverkskonfiguration
I följande exempel `NetworkConfiguration` visas elementet och dess underordnade element.

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
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
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

I följande tabell beskrivs elementets `NetworkConfiguration` underordnade element.

| Element       | Beskrivning |
| ------------- | ----------- |
| AccessControl (åtkomstkontroll) | Valfri. Anger reglerna för åtkomst till slutpunkter i en molntjänst. Åtkomstkontrollnamnet definieras av en `name` sträng för attribut. Elementet `AccessControl` innehåller ett `Rule` eller flera element. Mer än `AccessControl` ett element kan definieras.|
| Regel | Valfri. Anger vilken åtgärd som ska vidtas för ett angivet undernätsintervall med IP-adresser. Regelns ordning definieras av ett strängvärde `order` för attributet. Ju lägre regelnummer desto högre prioritet. Regler kan till exempel anges med ordernummer på 100, 200 och 300. Regeln med ordernumret 100 har företräde framför regeln som har en order på 200.<br /><br /> Åtgärden för regeln definieras av en `action` sträng för attributet. Möjliga värden:<br /><br /> -   `permit`– Anger att endast paket från det angivna undernätsområdet kan kommunicera med slutpunkten.<br />-   `deny`– Anger att åtkomst nekas slutpunkterna i det angivna undernätsområdet.<br /><br /> Undernätsintervallet för IP-adresser som påverkas av regeln `remoteSubnet` definieras av en sträng för attributet. Beskrivningen för regeln definieras av en `description` sträng för attributet.|
| SlutpunktAcl | Valfri. Anger tilldelning av åtkomstkontrollregler till en slutpunkt. Namnet på den roll som innehåller slutpunkten definieras av `role` en sträng för attributet. Namnet på slutpunkten definieras av en `endpoint` sträng för attributet. Namnet på den `AccessControl` uppsättning regler som ska tillämpas på slutpunkten definieras `accessControl` i en sträng för attributet. Mer än `EndpointAcl` ett element kan definieras.|
| DnsServer (1) | Valfri. Anger inställningarna för en DNS-server. Du kan ange inställningar för DNS-servrar utan virtuellt nätverk. Namnet på DNS-servern definieras av en `name` sträng för attributet. DNS-serverns IP-adress definieras av en `IPAddress` sträng för attributet. IP-adressen måste vara en giltig IPv4-adress.|
| VirtualNetworkSite | Valfri. Anger namnet på den plats för virtuellt nätverk där du vill distribuera molntjänsten. Den här inställningen skapar inte en webbplats för virtuellt nätverk. Den refererar till en plats som tidigare har definierats i nätverksfilen för det virtuella nätverket. En molntjänst kan bara vara medlem i ett virtuellt nätverk. Om du inte anger den här inställningen kommer molntjänsten inte att distribueras till ett virtuellt nätverk. Namnet på webbplatsen virtuellt nätverk definieras av `name` en sträng för attributet.|
| InstansAdress | Valfri. Anger associering av en roll till ett undernät eller en uppsättning undernät i det virtuella nätverket. När du associerar ett rollnamn till en förekomstadress kan du ange de undernät som du vill att rollen ska associeras till. Innehåller `InstanceAddress` ett undernätselement. Namnet på rollen som är associerad med undernätet eller undernäten `roleName` definieras av en sträng för attributet.|
| Undernät | Valfri. Anger det undernät som motsvarar undernätsnamnet i nätverkskonfigurationsfilen. Namnet på undernätet definieras av en `name` sträng för attributet.|
| ReserveratIP | Valfri. Anger den reserverade IP-adress som ska associeras med distributionen. Du måste använda Skapa reserverad IP-adress för att skapa den reserverade IP-adressen. Varje distribution i en molntjänst kan associeras med en reserverad IP-adress. Namnet på den reserverade IP-adressen definieras `name` av en sträng för attributet.|

## <a name="see-also"></a>Se även
[Konfigurationsschema för molntjänst (klassiskt)](schema-cscfg-file.md)
