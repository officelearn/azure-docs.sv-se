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
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 2a969240da6e16f5a5ba76605a6efa2a96f9e4dd
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285654"
---
## <a name="upgrade-the-mars-agent"></a>Uppgradera MARS-agenten

Versioner av Microsoft Azure Recovery Service MARS-agenten nedan 2.0.9083.0 är beroende av Azure Access Control service (ACS). MARS-agenten kallas även för Azure Backup-agenten. I 2018 Azure [inaktuella Azure Access Control service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Från och den 19 mars 2018, uppstår alla versioner av MARS-agenten nedan 2.0.9083.0 fel vid säkerhetskopiering. Att undvika eller lösa Säkerhetskopieringsfel, [uppgradera MARS-agenten till den senaste versionen](https://go.microsoft.com/fwlink/?linkid=229525). Om du vill identifiera servrar som kräver en uppgradering av MARS-agenten, följer du stegen i den [Backup-bloggen för att uppgradera MARS agenter](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). MARS-agenten används för att säkerhetskopiera filer och mappar och systemtillstånd till Azure. System Center DPM och Azure Backup Server kan du använda MARS-agenten för att säkerhetskopiera data till Azure.
