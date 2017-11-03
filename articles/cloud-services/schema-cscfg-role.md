---
title: Azure Cloud Services rollen schemat | Microsoft Docs
ms.custom: 
ms.date: 12/07/2016
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: "12"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: c25e7d139c7b7fd7c5da6bde8cfb9050eec8a88e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure Cloud Services, Schema för Config-roll

Den `Role` element i konfigurationsfilen anger antalet rollinstanser som ska distribueras för varje roll i tjänsten, värdena för alla konfigurationsinställningar och tumavtryck för certifikat som är kopplade till en roll.

Läs mer om Azure Service Konfigurationsschemat [molntjänst (klassisk) Konfigurationsschemat](schema-cscfg-file.md). Läs mer om Azure Service Definition schemat [molntjänst (klassisk) Definition schemat](schema-csdef-file.md).

##  <a name="Role"></a>Rollen Element
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
| vmName | Valfri. Anger DNS-namnet för en virtuell dator. Namnet måste innehålla 10 tecken eller mindre.|

I följande tabell beskrivs underordnade element i `Role` element.

| Element | Beskrivning |
| ------- | ----------- |
| Instanser | Krävs. Anger antalet instanser som ska distribueras för rollen. Antalet instanser som definieras av ett heltal för den `count` attribut.|
| Inställning   | Valfri. Anger ett namn och värde i en samling inställningar för en roll. Inställningens namn har definierats som en sträng för den `name` attribut och Inställningsvärdet definieras av en sträng för den `value` attribut.|
| Certifikat | Valfri. Anger namn, stämpel och algoritmen för ett tjänstcertifikat som ska associeras med rollen. Certifikatets namn har definierats som en sträng för den `name` attribut. Certifikatets tumavtryck definieras av en sträng med hexadecimala siffror som innehåller inga blanksteg för den `thumbprint` attribut. Hexadecimala siffror representeras med hjälp av siffror och gemener alfanumeriska tecken. Certifikat-algoritmen definieras av en sträng för den `thumbprintAlgorithm` attribut.|

## <a name="see-also"></a>Se även
[Cloud Service (klassiskt) Konfigurationsschemat](schema-cscfg-file.md)