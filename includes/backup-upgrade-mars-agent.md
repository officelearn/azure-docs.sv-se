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
ms.openlocfilehash: ea1295b08aa77a3e1a03d944ddbcbf37b6d17702
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71251546"
---
## <a name="upgrade-the-mars-agent"></a>Uppgradera MARS-agenten

Versioner av MARS-agenten (Microsoft Azure Recovery Service) nedan 2.0.9083.0 har ett beroende av Azure Access Control Service (ACS). MARS-agenten kallas även för den Azure Backup agenten. I 2018 är Azure [föråldrad azure Access Control Service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Från den 19 mars 2018 kommer alla versioner av MARS-agenten nedan att drabbas av säkerhets kopierings problem. [Uppgradera mars-agenten till den senaste versionen](https://go.microsoft.com/fwlink/?linkid=229525)för att undvika eller lösa säkerhets kopierings problem. Om du vill identifiera servrar som kräver en MARS-agent uppgradering följer du stegen i [säkerhets kopierings bloggen för att uppgradera mars-agenter](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). MARS-agenten används för att säkerhetskopiera filer och mappar och system tillstånds data till Azure. System Center DPM och Azure Backup Server använder MARS-agenten för att säkerhetskopiera data till Azure.
