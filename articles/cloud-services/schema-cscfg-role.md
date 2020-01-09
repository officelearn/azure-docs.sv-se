---
title: Schema för Azure Cloud Services-roller | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: 0f0e79b462726b1aa6a953a4b8c92334d6b16492
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449091"
---
# <a name="azure-cloud-services-config-role-schema"></a>Roll schema för Azure Cloud Services-konfiguration

I konfigurations filens `Role`-element anges antalet roll instanser som ska distribueras för varje roll i tjänsten, värdena för eventuella konfigurations inställningar och tumavtrycken för alla certifikat som är associerade med en roll.

Mer information om konfigurations schema för Azure-tjänsten finns i [konfigurations schema för moln tjänst (klassisk)](schema-cscfg-file.md). Mer information om definitions schema för Azure-tjänsten finns i [definitions schema för moln tjänst (klassisk)](schema-csdef-file.md).

##  <a name="Role"></a>Roll element
I följande exempel visas `Role`-elementet och dess underordnade element.

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

I följande tabell beskrivs attributen för `Role`-elementet.

| Attribut | Beskrivning |
| --------- | ----------- |
| namn   | Krävs. Anger namnet på rollen. Namnet måste matcha det angivna namnet för rollen i tjänst definitions filen.|
| vmName | Valfri. Anger DNS-namnet för en virtuell dator. Namnet får innehålla högst 10 tecken.|

I följande tabell beskrivs de underordnade elementen för `Role`-elementet.

| Element | Beskrivning |
| ------- | ----------- |
| Instanser | Krävs. Anger antalet instanser som ska distribueras för rollen. Antalet instanser definieras av ett heltal för attributet `count`.|
| Inställning   | Valfri. Anger ett inställnings namn och-värde i en samling inställningar för en roll. Inställnings namnet definieras av en sträng för attributet `name` och Setting-värdet definieras av en sträng för attributet `value`.|
| Certifikat | Valfri. Anger namn, tumavtryck och algoritm för ett tjänst certifikat som ska associeras med rollen. Certifikat namnet definieras av en sträng för attributet `name`. Tumavtryck för certifikatet definieras av en sträng med hexadecimala tal som inte innehåller några blank steg för attributet `thumbprint`. De hexadecimala talen måste representeras med siffror och versaler i alfabetet. Certifikatets algoritm definieras av en sträng för attributet `thumbprintAlgorithm`.|

## <a name="see-also"></a>Se även
[Konfigurations schema för moln tjänst (klassisk)](schema-cscfg-file.md)