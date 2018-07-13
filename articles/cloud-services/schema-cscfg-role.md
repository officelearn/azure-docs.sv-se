---
title: Azure Cloud Services, Rollschema | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: 12
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 20f4186426152d2dc9b445981a69881c35587eb6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005789"
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure Cloud Services, Config Rollschema

Den `Role` element i konfigurationsfilens anger antalet rollinstanser ska distribueras för varje roll i tjänsten, värdena för alla konfigurationsinställningar och tumavtryck för certifikat som är associerade med en roll.

Läs mer om den Azure-Tjänstkonfigurationens Schema [molntjänst (klassisk) konfigurationsschema](schema-cscfg-file.md). Läs mer om Azure Tjänstdefinitionsschemat [molntjänst (klassisk)-Definitionsschema](schema-csdef-file.md).

##  <a name="Role"></a> Rollen Element
I följande exempel visas den `Role` elementet och dess underordnade element.

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

I följande tabell beskrivs attributen för den `Role` element.

| Attribut | Beskrivning |
| --------- | ----------- |
| namn   | Krävs. Anger namnet på rollen. Namnet måste matcha namnet som angetts för rollen i tjänstdefinitionsfilen.|
| vmName | Valfri. Anger det DNS-namnet för en virtuell dator. Namnet måste vara 10 tecken eller mindre.|

I följande tabell beskrivs de underordnade elementen i den `Role` element.

| Element | Beskrivning |
| ------- | ----------- |
| Instanser | Krävs. Anger antalet instanser som ska distribueras för rollen. Antalet instanser som definieras av ett heltal för den `count` attribut.|
| Inställning   | Valfri. Anger ett namn och värde i en samling inställningar för en roll. Inställningens namn definieras av en sträng för den `name` attribut och Inställningsvärdet definieras av en sträng för den `value` attribut.|
| Certifikat | Valfri. Anger namn, tumavtryck och algoritmen av en service-certifikat som ska associeras med rollen. Certifikatets namn definieras av en sträng för den `name` attribut. Certifikatets tumavtryck definieras av en sträng med hexadecimala siffror som innehåller inga blanksteg för den `thumbprint` attribut. Den hexadecimala siffror representeras med hjälp av siffror och versaler alfanumeriska tecken. Certifikat-algoritmen definieras av en sträng för den `thumbprintAlgorithm` attribut.|

## <a name="see-also"></a>Se även
[Konfigurationsschema för cloud Service (klassisk)](schema-cscfg-file.md)