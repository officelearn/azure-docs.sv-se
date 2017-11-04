---
title: Azure Service Fabric CLI - sfctl store | Microsoft Docs
description: Beskriver Service Fabric CLI sfctl store-kommandon.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 2af6dff4ffcdf295731f2d61b5f9e35af40615e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-store"></a>sfctl store
Utföra grundläggande nivån filåtgärder på klustret image store.

## <a name="commands"></a>Kommandon

|Kommando|Beskrivning|
| --- | --- |
|    ta bort| Tar bort befintliga avbildningen lagra innehållet.|
|    rot-info| Hämtar innehållsinformationen i roten för image store.|
|    stat  | Hämtar innehållsinformation för image store.|


## <a name="sfctl-store-delete"></a>ta bort sfctl-butik
Tar bort befintliga avbildningen lagra innehållet.

Tar bort befintliga image store innehåll som hittas i angivna bilden lagra relativ sökväg. Det här kommandot kan användas för att ta bort överförda programpaket när de har etablerats.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --innehåll sökväg [krävs]| Relativ sökväg till filen eller mappen i image store från dess rot.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Mer information och exempel finns i http://jmespath.org/.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="sfctl-store-stat"></a>sfctl store stat
Hämtar innehållsinformation för image store.

Returnerar information om image store innehållet på den angivna contentPath i förhållande till roten för image store.

### <a name="arguments"></a>Argument

|Argumentet|Beskrivning|
| --- | --- |
| --innehåll sökväg [krävs]| Relativ sökväg till filen eller mappen i image store från dess rot.|
| --timeout -t          | Servern tidsgräns i sekunder.  Standard: 60.|

### <a name="global-arguments"></a>Globala argument

|Argumentet|Beskrivning|
| --- | --- |
| – Felsökning               | Öka loggning detaljnivå om du vill visa alla debug-loggar.|
| --hjälp -h             | Visa den här hjälpmeddelandet och avsluta.|
| --utdata -o           | Format för utdata.  Tillåtna värden: json jsonc, tabell, TVs.  Standard: json.|
| --fråga               | JMESPath frågesträngen. Se http://jmespath.org/ för mer information och exempel.|
| -verbose             | Öka loggning detaljnivå. Använd--debug för fullständig felsökningsloggar.|

## <a name="next-steps"></a>Nästa steg
- [Installationsprogrammet](service-fabric-cli.md) Service Fabric CLI.
- Lär dig att använda Service Fabric CLI med hjälp av den [exempel på skript](/azure/service-fabric/scripts/sfctl-upgrade-application).