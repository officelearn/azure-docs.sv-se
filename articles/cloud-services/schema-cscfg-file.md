---
title: Definitionsschema för Azure Cloud Services (.cscfg-fil) | Microsoft-dokument
description: En tjänstkonfigurationsfil (.cscfg) anger hur många rollinstanser som ska distribueras för varje roll, konfigurationsvärden och certifikattumavtryck för en roll.
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: tgore03
ms.author: tagore
ms.openlocfilehash: cb77181e00c97b7f426429793f17af3cb5e84ebe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534753"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services Config-schema (.cscfg-fil)
Tjänstkonfigurationsfilen anger antalet rollinstanser som ska distribueras för varje roll i tjänsten, värdena för alla konfigurationsinställningar och tumavtryck för alla certifikat som är associerade med en roll. Om tjänsten ingår i ett virtuellt nätverk måste konfigurationsinformation för nätverket anges i tjänstkonfigurationsfilen samt i konfigurationsfilen för virtuella nätverk. Standardtillägget för tjänstkonfigurationsfilen är CSCFG.

Tjänstmodellen beskrivs av [Cloud Service (klassiskt) definitionsschema](schema-csdef-file.md).

Som standard installeras konfigurationsschemafilen för Azure `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` Diagnostics i katalogen. Ersätt `<version>` med den installerade versionen av [Azure SDK](https://azure.microsoft.com/downloads/).

Mer information om hur du konfigurerar roller i en tjänst finns i [Vad är cloud service-modellen](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Schema för konfiguration av grundläggande tjänst
Det grundläggande formatet för tjänstkonfigurationsfilen är följande.

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
I följande avsnitt beskrivs `ServiceConfiguration` schemat för elementet:

- [Role-schema](schema-cscfg-role.md)
- [NetworkConfiguration-schema](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Namnområde för tjänstkonfiguration
XML-namnområdet för tjänstkonfigurationsfilen `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`är: .

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a>Element för servicekonfiguration
Elementet `ServiceConfiguration` är elementet på den översta nivån i tjänstkonfigurationsfilen.

I följande tabell beskrivs elementets `ServiceConfiguration` attribut. Alla attributvärden är strängtyper.

| Attribut | Beskrivning |
| --------- | ----------- |
|Tjänstnamn|Krävs. Namnet på molntjänsten. Namnet som anges här måste stämma överens med det namn som anges i tjänstdefinitionsfilen.|
|osFamily|Valfri. Anger det gästoperativsystem som ska köras på rollinstanser i molntjänsten. Information om gästoperativsystemsläpp som stöds finns i [Azure Guest OS Releases och SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).<br /><br /> Om du inte `osFamily` inkluderar ett värde och `osVersion` du inte har angett attributet till en viss Gäst OS-version, används ett standardvärde på 1.|
|osVersion|Valfri. Anger den version av gästoperativsystemet som ska köras på rollinstanser i molntjänsten. Mer information om gästoperativsystemversioner finns i [Azure Guest OS Releases och SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).<br /><br /> Du kan ange att gästoperativsystemet ska uppgraderas automatiskt till den senaste versionen. Det gör du genom att `osVersion` ange `*`värdet för attributet på . När den `*`är inställd på distribueras rollinstanserna med den senaste versionen av gästoperativsystemet för den angivna OS-familjen och uppgraderas automatiskt när nya versioner av gästoperativsystemet släpps.<br /><br /> Om du vill ange en `Configuration String` viss version manuellt använder du avsnittet från tabellen i avsnittet **Framtida, Aktuella och Tillfälliga gästoperativsystemversioner** i [Azure Guest OS Releases och SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md).<br /><br /> Standardvärdet för `osVersion` attributet `*`är .|
|schemaVersion|Valfri. Anger versionen av schemat Tjänstkonfiguration. Schemaversionen gör det möjligt för Visual Studio att välja rätt SDK-verktyg som ska användas för schemavalidering om mer än en version av SDK installeras sida vid sida. Mer information om schema- och versionskompatibilitet finns i [Azure Guest OS Releases och SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md)|

Tjänstkonfigurationsfilen måste `ServiceConfiguration` innehålla ett element. Elementet `ServiceConfiguration` kan innehålla `Role` valfritt antal element `NetworkConfiguration` och noll eller 1 element.