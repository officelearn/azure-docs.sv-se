---
title: Uppgradera Azure Backup Agent
description: Den här informationen förklarar varför du bör uppgradera Azure Backup Agent och var du kan hämta uppgraderingen.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673198"
---
## <a name="upgrade-the-mars-agent"></a>Uppgradera MARS-agenten

Versioner av MICROSOFT Azure Recovery Services (MARS)-agenten under 2.0.9083.0 har ett beroende av Azure Access Control-tjänsten. MARS-agenten kallas även Azure Backup Agent.

Under 2018 [inaktuella Azure Access Control-tjänsten](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Från och med den 19 mars 2018 kommer alla versioner av MARS-agenten under 2.0.9083.0 att uppleva säkerhetskopieringsfel. För att undvika eller lösa säkerhetskopieringsfel [uppgraderar du DIN MARS-agent till den senaste versionen](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Om du vill identifiera servrar som kräver en MARS-agentuppgradering följer du stegen i [Uppgradera MARS-agenten (Microsoft Azure Recovery Services).](../articles/backup/upgrade-mars-agent.md)

MARS-agenten används för att säkerhetskopiera filer och mappar och systemtillståndsdata till Azure. System Center DPM och Azure Backup Server använder MARS-agenten för att säkerhetskopiera data till Azure.
