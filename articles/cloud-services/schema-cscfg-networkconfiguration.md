---
title: Azure Cloud Services NetworkConfiguration-schema | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: cacc8b1f2909965594fdf0d841963e792acf648c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385432"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>NetworkConfiguration-schema för Azure Cloud Services config

`NetworkConfiguration`-elementet i tjänst konfigurations filen anger Virtual Network-och DNS-värden. De här inställningarna är valfria för moln tjänster.

Du kan använda följande resurs för att lära dig mer om virtuella nätverk och associerade scheman:

- [Konfigurations schema för moln tjänst (klassisk)](schema-cscfg-file.md)
- [Definitions schema för moln tjänst (klassisk)](schema-csdef-file.md)
- [Skapa en Virtual Network (klassisk)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration Element
I följande exempel visas `NetworkConfiguration`-elementet och dess underordnade element.

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

I följande tabell beskrivs de underordnade elementen för `NetworkConfiguration`-elementet.

| Element       | Beskrivning |
| ------------- | ----------- |
| AccessControl | Valfri. Anger reglerna för åtkomst till slut punkter i en moln tjänst. Namnet på åtkomst kontrollen definieras av en sträng för `name` attribut. `AccessControl`-elementet innehåller ett eller flera `Rule` element. Fler än ett `AccessControl`-element kan definieras.|
| Regel | Valfri. Anger den åtgärd som ska vidtas för ett angivet undernät med IP-adresser. Regelns ordning definieras av ett sträng värde för attributet `order`. Ju lägre regel numret är desto högre prioritet. Regler kan till exempel anges med ordnings nummer på 100, 200 och 300. Regeln med ordnings numret 100 har företräde framför regeln som har en ordning på 200.<br /><br /> Åtgärden för regeln definieras av en sträng för attributet `action`. Möjliga värden:<br /><br /> -   `permit` – anger att endast paket från det angivna under näts intervallet kan kommunicera med slut punkten.<br />-   `deny` – anger att åtkomst nekas till slut punkterna i det angivna under näts intervallet.<br /><br /> Under näts intervallet för IP-adresser som påverkas av regeln definieras av en sträng för attributet `remoteSubnet`. Beskrivningen för regeln definieras av en sträng för attributet `description`.|
| EndpointAcl | Valfri. Anger tilldelning av åtkomst kontroll regler till en slut punkt. Namnet på rollen som innehåller slut punkten definieras av en sträng för attributet `role`. Namnet på slut punkten definieras av en sträng för attributet `endpoint`. Namnet på den uppsättning `AccessControl` regler som ska användas för slut punkten definieras i en sträng för attributet `accessControl`. Fler än ett `EndpointAcl` element kan definieras.|
| DnsServer | Valfri. Anger inställningarna för en DNS-server. Du kan ange inställningar för DNS-servrar utan Virtual Network. Namnet på DNS-servern definieras av en sträng för attributet `name`. IP-adressen för DNS-servern definieras av en sträng för attributet `IPAddress`. IP-adressen måste vara en giltig IPv4-adress.|
| VirtualNetworkSite | Valfri. Anger namnet på den Virtual Network plats där du vill distribuera moln tjänsten. Den här inställningen skapar inte en Virtual Network webbplats. Den refererar till en plats som redan har definierats i nätverks filen för din Virtual Network. En moln tjänst kan bara vara medlem i en Virtual Network. Om du inte anger den här inställningen kommer moln tjänsten inte att distribueras till en Virtual Network. Namnet på den Virtual Network webbplatsen definieras av en sträng för attributet `name`.|
| InstanceAddress | Valfri. Anger associationen för en roll till ett undernät eller en uppsättning undernät i Virtual Network. När du associerar ett rollnamn till en instans adress kan du ange de undernät som du vill att rollen ska kopplas till. `InstanceAddress` innehåller ett undernät-element. Namnet på den roll som är associerad med under nätet eller under näten definieras av en sträng för attributet `roleName`.|
| Undernät | Valfri. Anger det undernät som motsvarar under nätets namn i nätverks konfigurations filen. Namnet på under nätet definieras av en sträng för attributet `name`.|
| ReservedIP | Valfri. Anger den reserverade IP-adress som ska associeras med distributionen. Du måste använda Create Reserverad IP-adress för att skapa den reserverade IP-adressen. Varje distribution i en moln tjänst kan associeras med en reserverad IP-adress. Namnet på den reserverade IP-adressen definieras av en sträng för attributet `name`.|

## <a name="see-also"></a>Se även
[Konfigurations schema för moln tjänst (klassisk)](schema-cscfg-file.md)
