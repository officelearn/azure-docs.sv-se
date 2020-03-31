---
title: Versionshistorik
titleSuffix: Microsoft Genomics
description: Utgivningshistoriken för uppdateringar av Microsoft Genomics Python-klienten för korrigeringar och nya funktioner.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76991090"
---
# <a name="version-release-history"></a>Versionshistorik
Microsoft Genomics-teamet uppdaterar regelbundet Microsoft Genomics Python-klienten för korrigeringar och nya funktioner. 

## <a name="latest-release"></a>Senaste utgåvan
Den aktuella Python-klienten är version 0.9.0. Den släpptes 6 februari 2019 och stöder löpande arbetsflöden med GATK 3.5 och GATK4. Den stöder gVCF-utgång och kan acceptera ett valfritt argument för utdatakomprimering.


## <a name="release-history"></a>Utgivningshistorik 
Nya versioner av Microsoft Genomics Python-klienten släpps ungefär en gång per år. När nya versioner av Microsoft Genomics Python-klienten släpps uppdateras här en lista med korrigeringar och funktioner. När nya versioner släpps bör tidigare versioner fortsätta att stödjas i minst 90 dagar. När tidigare versioner inte längre stöds visas det på den här sidan. 

### <a name="version-090"></a>Version 0.9.0
Version 0.9.0 innehåller stöd för utdatakomprimering. Detta motsvarar körning `-bgzip` följt `-tabix` av vcf- eller gvcf-utdata. Mer information finns i [Vanliga frågor](frequently-asked-questions-genomics.md)och svar . 

### <a name="version-081"></a>Version 0.8.1
Version 0.8.1 innehåller mindre buggfixar.  

### <a name="version-080"></a>Version 0.8.0
Version 0.8.0 innehåller stöd för GATK4 och utdataning av gVCFs.  

### <a name="version-074"></a>Version 0.7.4
Version 0.7.4 innehåller stöd för att acceptera SAS-token i stället för kontonycklar i `config.txt` indata. Mer information finns i [Snabbstart för Indata-SAS-token](quickstart-input-sas.md). 

### <a name="version-073"></a>Version 0.7.3
Version 0.7.3 innehåller mindre buggfixar.

### <a name="version-072"></a>Version 0.7.2
Version 0.7.2 är den ursprungliga versionen. Det släpptes 1 november 2017.
