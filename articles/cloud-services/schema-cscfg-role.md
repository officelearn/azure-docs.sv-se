---
title: Rollschema för Azure Cloud Services | Microsoft-dokument
description: Rollelementet i en tjänstkonfigurationsfil anger hur många rollinstanser som ska distribueras för varje roll, konfigurationsvärden och certifikattumavtryck.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528429"
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure Cloud Services config-rollschema

Elementet `Role` i konfigurationsfilen anger antalet rollinstanser som ska distribueras för varje roll i tjänsten, värdena för eventuella konfigurationsinställningar och tumavtryck för alla certifikat som är associerade med en roll.

Mer information om Azure Service Configuration Schema finns i [Cloud Service (klassiskt) konfigurationsschema](schema-cscfg-file.md). Mer information om Azure Service Definition Schema finns i [Cloud Service (klassiskt) definitionsschema](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a>Rollelement
I följande exempel `Role` visas elementet och dess underordnade element.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

I följande tabell beskrivs elementets `Role` attribut.

| Attribut | Beskrivning |
| --------- | ----------- |
| namn   | Krävs. Anger namnet på rollen. Namnet måste matcha namnet som anges för rollen i tjänstdefinitionsfilen.|
| vmName | Valfri. Anger DNS-namnet för en virtuell dator. Namnet måste vara högst 10 tecken.|

I följande tabell beskrivs elementets `Role` underordnade element.

| Element | Beskrivning |
| ------- | ----------- |
| Instanser | Krävs. Anger antalet instanser som ska distribueras för rollen. Antalet instanser definieras av ett heltal `count` för attributet.|
| Inställning   | Valfri. Anger ett inställningsnamn och värde i en samling inställningar för en roll. Inställningsnamnet definieras av en `name` sträng för attributet och inställningsvärdet `value` definieras av en sträng för attributet.|
| Certifikat | Valfri. Anger namn, tumavtryck och algoritm för ett tjänstcertifikat som ska associeras med rollen. Certifikatnamnet definieras av en sträng `name` för attributet. Certifikatets tumavtryck definieras av en sträng med hexadecimala tal som inte innehåller några blanksteg för `thumbprint` attributet. De hexadecimala talen måste representeras med siffror och versaler alfatecken. Certifikatalgoritmen definieras av en `thumbprintAlgorithm` sträng för attributet.|

## <a name="see-also"></a>Se även
[Konfigurationsschema för molntjänst (klassiskt)](schema-cscfg-file.md)