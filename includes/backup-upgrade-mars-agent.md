---
title: Uppgradera Azure Backup Agent
description: Den här informationen förklarar varför du bör uppgradera Azure Backup agenten och var du kan ladda ned uppgraderingen.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197136"
---
## <a name="upgrade-the-mars-agent"></a>Uppgradera MARS-agenten

Versioner av Microsoft Azure Recovery Services (MARS) agenten nedan 2.0.9083.0 har ett beroende av Azure Access Control-tjänsten. MARS-agenten kallas även för den Azure Backup agenten.

I 2018 har Microsoft [föråldrat Azure Access Control-tjänsten](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Från den 19 mars 2018 kommer alla versioner av MARS-agenten nedan att drabbas av säkerhets kopierings problem. [Uppgradera mars-agenten till den senaste versionen](https://go.microsoft.com/fwlink/?linkid=229525)för att undvika eller lösa säkerhets kopierings problem. Om du vill identifiera servrar som kräver en MARS-agent uppgradering följer du stegen i [säkerhets kopierings bloggen för att uppgradera mars-agenter](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/).

MARS-agenten används för att säkerhetskopiera filer och mappar och system tillstånds data till Azure. System Center DPM och Azure Backup Server använder MARS-agenten för att säkerhetskopiera data till Azure.
