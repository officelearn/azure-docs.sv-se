---
title: Uppgradera Azure Backup-agenten
description: Den här informationen beskriver varför du ska uppgradera Azure Backup-agenten och var du hämtar uppgraderingen.
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
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
ms.locfileid: "29973307"
---
## <a name="upgrade-the-mars-agent"></a>Uppgradera MARS-agenten
Versioner av Microsoft Azure Recovery Service MARS-agenten nedan 2.0.9083.0 är beroende av Azure Access Control service (ACS). I 2018 Azure kommer [inaktualisera Azure Access Control service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Från 19 mars 2018, får alla versioner av MARS-agenten nedan 2.0.9083.0 Säkerhetskopieringsfel. Att undvika eller lösa Säkerhetskopieringsfel, [uppgradera MARS-agenten till den senaste versionen](https://go.microsoft.com/fwlink/?linkid=229525). Om du vill identifiera servrar som kräver en MARS-agenten uppgradering följer du stegen i den [säkerhetskopiering blogg för uppgradering av Azure Backup Agent](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). MARS-agenten används för att säkerhetskopiera följande datatyper till Azure:
- Filer 
- Systemtillstånd
- Säkerhetskopiera System Center DPM till Azure
- Azure Backup Server (MABS)
