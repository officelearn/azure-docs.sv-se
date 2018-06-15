---
title: Azure Cloud Services Definition Schema (.cscfg-fil) | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: 35
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 6347314e7f279356f4f3944f3238deda84f10fc0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358225"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services Config Schema (.cscfg-fil)
Tjänstkonfigurationsfilen anger antalet rollinstanser som ska distribueras för varje roll i tjänsten, värdena för alla konfigurationsinställningar och tumavtryck för certifikat som är kopplade till en roll. Om tjänsten är en del av ett virtuellt nätverk, måste konfigurationsinformation för nätverket anges i tjänstkonfigurationsfilen, såväl som virtuella nätverk konfigurationsfilen. Standardfilnamnstillägget för tjänstkonfigurationsfilen är .cscfg.

Tjänstmodellen beskrivs av den [molntjänst (klassisk) Definition schemat](schema-csdef-file.md).

Som standard installeras schemat för Azure-diagnostik konfigurationsfilen till den `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` directory. Ersätt `<version>` med den installerade versionen av den [Azure SDK](https://azure.microsoft.com/downloads/).

Mer information om hur du konfigurerar roller i en tjänst finns [vad är Cloud Service model](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Grundläggande Service Configuration Schema
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
Den `ServiceConfiguration` elementet är det översta elementet i tjänstekonfigurationsfilen.

I följande tabell beskrivs attributen för den `ServiceConfiguration` element. Alla värdena attribut strängtyper.

| Attribut | Beskrivning |
| --------- | ----------- |
|Tjänstnamn|Krävs. Namnet på Molntjänsten. Det här namn måste matcha namnet som angetts i tjänstdefinitionsfilen.|
|osFamily|Valfri. Anger de Gästoperativsystem som körs på rollinstanser i Molntjänsten. Information om stöds gäst-OS-versioner finns [Azure gäst OS-versioner och SDK-Kompatibilitetsmatris](cloud-services-guestos-update-matrix.md).<br /><br /> Om du inte använder en `osFamily` värdet och du inte har ställt in den `osVersion` attribut till en specifik gäst-OS-version, standardvärdet 1 används.|
|OsVersion|Valfri. Anger versionen av det Gästoperativsystem som körs på rollinstanser i Molntjänsten. Läs mer om Gästoperativsystem versioner [versioner för gästoperativsystem till Azure och SDK-Kompatibilitetsmatris](cloud-services-guestos-update-matrix.md).<br /><br /> Du kan ange att gäst-OS bör uppgraderas automatiskt till den senaste versionen. Det gör du genom att ange värdet för den `osVersion` attributet `*`. Om värdet är `*`, rollinstanserna distribueras med den senaste versionen av gäst-OS för den angivna OS-familjen och uppgraderas automatiskt när nya versioner av gäst-OS släpps.<br /><br /> Om du vill ange en viss version manuellt, använder den `Configuration String` från tabellen i den **framtiden, aktuella och övergående gäst-OS-versioner** avsnitt i [versioner för gästoperativsystem till Azure och SDK-Kompatibilitetsmatris](cloud-services-guestos-update-matrix.md) .<br /><br /> Standardvärdet för den `osVersion` attributet `*`.|
|schemaVersion|Valfri. Anger versionen av schemat tjänstkonfigurationen. Schemaversionen kan Visual Studio för att välja rätt SDK-verktyg ska användas för schemavalidering om mer än en version av SDK är installerat sida-vid-sida. Mer information om kompatibilitet för schemat och versionen finns [versioner för gästoperativsystem till Azure och SDK-Kompatibilitetsmatris](cloud-services-guestos-update-matrix.md)|

Tjänstkonfigurationsfilen måste innehålla ett `ServiceConfiguration` element. Den `ServiceConfiguration` element kan innehålla valfritt antal `Role` element och noll eller 1 `NetworkConfiguration` element.