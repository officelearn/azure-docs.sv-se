---
title: Uppgradera Azure Backup-agenten
description: Den här informationen beskriver varför du bör uppgradera Azure Backup-agenten och var du kan hämta uppgraderingen.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750338"
---
## <a name="upgrade-the-mars-agent"></a>Uppgradera MARS-agenten
Versioner av Microsoft Azure Recovery Service MARS-agenten nedan 2.0.9083.0 är beroende av Azure Access Control service (ACS). I 2018 Azure kommer att [avverka Azure Access Control service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Från och den 19 mars 2018, uppstår alla versioner av MARS-agenten nedan 2.0.9083.0 fel vid säkerhetskopiering. Att undvika eller lösa Säkerhetskopieringsfel, [uppgradera MARS-agenten till den senaste versionen](https://go.microsoft.com/fwlink/?linkid=229525). Om du vill identifiera servrar som kräver en uppgradering av MARS-agenten, följer du stegen i den [Backup-bloggen för att uppgradera Azure Backup-agenter](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). MARS-agenten används för att säkerhetskopiera följande datatyper till Azure:
- Filer 
- Systemtillstånd
- System Center DPM att säkerhetskopiera till Azure
- Azure Backup Server (MABS)
