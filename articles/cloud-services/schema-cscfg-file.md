---
title: Azure Cloud Services-Definitionsschema (.cscfg-filen) | Microsoft Docs
ms.custom: ''
origin.date: 12/07/2016
ms.date: 11/06/2017
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: 35
author: thraka
ms.author: v-yiso
manager: timlt
ms.openlocfilehash: 424381e2c243420cc2a68dc776d249cb17574f98
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130321"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services, Config Schema (.cscfg-fil)
Tjänstkonfigurationsfilen anger antalet rollinstanser ska distribueras för varje roll i tjänsten, värdena för alla konfigurationsinställningar och tumavtryck för certifikat som är associerade med en roll. Om tjänsten är en del av ett virtuellt nätverk, måste konfigurationsinformation för det virtuella nätverket anges i tjänstekonfigurationsfilen och i virtuella nätverk konfigurationsfilen. Standardfilnamnstillägget för tjänstkonfigurationsfilen är .cscfg.

Tjänstmodellen beskrivs i den [molntjänst (klassisk)-Definitionsschema](schema-csdef-file.md).

Som standard installeras Azure Diagnostics schema konfigurationsfilen till den `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` directory. Ersätt `<version>` med den installerade versionen av den [Azure SDK](https://azure.microsoft.com/downloads/).

Läs mer om hur du konfigurerar roller i en tjänst, [vad är Cloud Service-modellen](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Grundläggande Tjänstkonfigurationens Schema
Det grundläggande formatet för tjänstkonfigurationsfilen är som följer.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Schemadefinitioner
I följande avsnitt beskrivs schemat för den `ServiceConfiguration` element:

- [Rollschema](schema-cscfg-role.md)
- [NetworkConfiguration-schema](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Service Configuration Namespace
XML-namnområdet för tjänstkonfigurationsfilen är: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> ServiceConfiguration Element
Den `ServiceConfiguration` elementet har det översta elementet i tjänstekonfigurationsfilen.

I följande tabell beskrivs attributen för den `ServiceConfiguration` element. Alla attribut värden är strängtyper.

| Attribut | Beskrivning |
| --------- | ----------- |
|serviceName|Krävs. Namnet på Molntjänsten. Det här namnet måste matcha namnet i tjänstdefinitionsfilen.|
|osFamily|Valfri. Anger det Gästoperativsystem som ska köras på rollinstanser i Molntjänsten. Information om stöds gäst-OS-versioner finns i [Azure Guest OS-versionerna och SDK-Kompatibilitetsöversikten](cloud-services-guestos-update-matrix.md).<br /><br /> Om du inte använder en `osFamily` värde och du inte har angett den `osVersion` -attribut till en viss gäst-OS-version, standardvärdet 1.|
|osVersion|Valfri. Anger vilken version av det Gästoperativsystem som ska köras på rollinstanser i Molntjänsten. Läs mer om gäst-OS-versioner, [Azure Gästoperativsystemversioner och SDK-Kompatibilitetsöversikten](cloud-services-guestos-update-matrix.md).<br /><br /> Du kan ange att gäst-OS bör uppgraderas automatiskt till den senaste versionen. Gör detta genom att ange värdet för den `osVersion` attributet `*`. När värdet `*`, rollinstanserna distribueras med den senaste versionen av Gästoperativsystemet för den angivna OS-familjen och uppgraderas automatiskt när nya versioner av gäst-OS släpps.<br /><br /> Ange en specifik version manuellt genom att använda den `Configuration String` från tabellen i den **framtiden, aktuellt och övergående gäst-OS-versioner** delen av [Azure Gästoperativsystemversioner och SDK-Kompatibilitetsöversikten](cloud-services-guestos-update-matrix.md) .<br /><br /> Standardvärdet för den `osVersion` attributet är `*`.|
|schemaVersion|Valfri. Anger vilken version av tjänstkonfiguration schemat. Schemaversion kan Visual Studio för att de rätt SDK-verktyg som ska användas för schemavalideringen om mer än en version av SDK: N installeras sida-vid-sida. Läs mer om schema- och version kompatibilitet [Azure Gästoperativsystemversioner och SDK-Kompatibilitetsöversikten](cloud-services-guestos-update-matrix.md)|

Tjänstkonfigurationsfilen måste innehålla ett `ServiceConfiguration` element. Den `ServiceConfiguration` element kan innehålla valfritt antal `Role` element och noll eller 1 `NetworkConfiguration` element.