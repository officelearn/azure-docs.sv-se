---
title: Schema för Azure Cloud Services-roller | Microsoft Docs
description: Roll elementet i en tjänst konfigurations fil anger hur många roll instanser som ska distribueras för varje roll, konfigurations värden och certifikat tumavtrycken.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79528429"
---
# <a name="azure-cloud-services-config-role-schema"></a>Roll schema för Azure Cloud Services-konfiguration

`Role`Elementet i konfigurations filen anger antalet roll instanser som ska distribueras för varje roll i tjänsten, värdena för alla konfigurations inställningar och tumavtrycken för alla certifikat som är associerade med en roll.

Mer information om konfigurations schema för Azure-tjänsten finns i [konfigurations schema för moln tjänst (klassisk)](schema-cscfg-file.md). Mer information om definitions schema för Azure-tjänsten finns i [definitions schema för moln tjänst (klassisk)](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a>Roll element
I följande exempel visas `Role` elementet och dess underordnade element.

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

I följande tabell beskrivs attributen för `Role` elementet.

| Attribut | Beskrivning |
| --------- | ----------- |
| name   | Krävs. Anger namnet på rollen. Namnet måste matcha det angivna namnet för rollen i tjänst definitions filen.|
| vmName | Valfritt. Anger DNS-namnet för en virtuell dator. Namnet får innehålla högst 10 tecken.|

I följande tabell beskrivs `Role` elementets underordnade element.

| Element | Beskrivning |
| ------- | ----------- |
| Instanser | Krävs. Anger antalet instanser som ska distribueras för rollen. Antalet instanser definieras av ett heltal för `count` attributet.|
| Inställningen   | Valfritt. Anger ett inställnings namn och-värde i en samling inställningar för en roll. Inställnings namnet definieras av en sträng för `name` attributet och värdet Setting definieras av en sträng för `value` attributet.|
| Certifikat | Valfritt. Anger namn, tumavtryck och algoritm för ett tjänst certifikat som ska associeras med rollen. Certifikat namnet definieras av en sträng för `name` attributet. Tumavtryck för certifikatet definieras av en sträng med hexadecimala tal som inte innehåller några blank steg för `thumbprint` attributet. De hexadecimala talen måste representeras med siffror och versaler i alfabetet. Certifikatets algoritm definieras av en sträng för `thumbprintAlgorithm` attributet.|

## <a name="see-also"></a>Se även
[Konfigurations schema för moln tjänst (klassisk)](schema-cscfg-file.md)