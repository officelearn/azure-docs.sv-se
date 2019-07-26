---
title: Schema för Azure Cloud Services-roller | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 481301333ada39297bf2813bbea5f096c2abd3ad
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360650"
---
# <a name="azure-cloud-services-config-role-schema"></a>Roll schema för Azure Cloud Services-konfiguration

`Role` Elementet i konfigurations filen anger antalet roll instanser som ska distribueras för varje roll i tjänsten, värdena för alla konfigurations inställningar och tumavtrycken för alla certifikat som är associerade med en roll.

Mer information om konfigurations schema för Azure-tjänsten finns i [konfigurations schema för moln tjänst (klassisk)](schema-cscfg-file.md). Mer information om definitions schema för Azure-tjänsten finns i [definitions schema för moln tjänst (klassisk)](schema-csdef-file.md).

##  <a name="Role"></a>Roll element
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
| name   | Obligatoriskt. Anger namnet på rollen. Namnet måste matcha det angivna namnet för rollen i tjänst definitions filen.|
| vmName | Valfri. Anger DNS-namnet för en virtuell dator. Namnet får innehålla högst 10 tecken.|

I följande tabell beskrivs `Role` elementets underordnade element.

| Element | Beskrivning |
| ------- | ----------- |
| instanser | Obligatoriskt. Anger antalet instanser som ska distribueras för rollen. Antalet instanser definieras av ett heltal för `count` attributet.|
| Inställning   | Valfri. Anger ett inställnings namn och-värde i en samling inställningar för en roll. Inställnings namnet definieras av en sträng för `name` attributet och värdet Setting definieras av en sträng `value` för attributet.|
| Certifikat | Valfri. Anger namn, tumavtryck och algoritm för ett tjänst certifikat som ska associeras med rollen. Certifikat namnet definieras av en sträng för `name` attributet. Tumavtryck för certifikatet definieras av en sträng med hexadecimala tal som inte innehåller några blank `thumbprint` steg för attributet. De hexadecimala talen måste representeras med siffror och versaler i alfabetet. Certifikatets algoritm definieras av en sträng för `thumbprintAlgorithm` attributet.|

## <a name="see-also"></a>Se även
[Konfigurations schema för moln tjänst (klassisk)](schema-cscfg-file.md)