---
title: Uppdatera Azure Backup centrala Reporting Innehållspaketet
description: Information om uppdateringar av Azure Backup-innehållspaket i Power BI
services: backup
documentationcenter: ''
author: adigan
manager: shivamg
editor: ''
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: adigan;
ms.openlocfilehash: 07774234849d96b9a44678b68ca7b13b6b4830f9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266076"
---
# <a name="updating-azure-backup-central-reporting-content-pack"></a>Uppdatera Azure Backup centrala Reporting Innehållspaketet 

[Azure Backup-Innehållspaketet](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi) kan användas för att visa rapporter om backup centralt. Innehållspaketet uppdateras regelbundet för att lägga till fler funktioner och bugg korrigeringar. Den här artikeln leder dig genom stegen för att uppdatera Innehållspaketet om du vill fördröja uppdateringen och de uppdateringar som görs över tid.

## <a name="how-to-get-updates-to-the-content-pack"></a>Så här hämtar du uppdateringar till Innehållspaketet

### <a name="to-get-the-updated-content-pack"></a>Att hämta det uppdaterade Innehållspaketet
Din kopia av Innehållspaketet uppdateras automatiskt om du inte har gjort några ändringar till den. Om Innehållspaketet har ändrats, får du ett meddelande i Power BI och ett e-postmeddelande om samma. Du kan välja att få det uppdaterade Innehållspaketet används för att underlätta för dig. 

### <a name="to-delay-the-update"></a>Att fördröja uppdateringen
Det bästa sättet är att importera Innehållspaketet till en [anpassade arbetsytan](https://youtu.be/26zyOtyHPJM?t=1m57s). Nu har du möjligheten att redigera rapporter.
Som nämns ovan, om Innehållspaketet ändras kan se du ett meddelande i PowerBI. Du kan välja att hämta Innehållspaketet senare. 

## <a name="coming-soon"></a>Kommer snart
   
Azure Backup-Innehållspaketet uppdateras för att stödja flera arbetsbelastningar som SQL i IaaS VM-säkerhetskopiering och SC DPM, förutom det aktuella stödet för MAB och Azure VM Backup. Det innebär att du kommer snart kommer att kunna visa och analysera data dina säkerhetskopierade data på en central plats. Den [rapporter kan även anpassas](https://youtu.be/26zyOtyHPJM) som passar organisationens behov.

För att göra rapporter mer meningsfulla i arbetsbelastningar, ändras uppsättningen rapporter som är förkonfigurerade med Azure Backup-Innehållspaketet. En förhandstitt i kommande uppsättning rapporter finns här:

### <a name="summary"></a>Sammanfattning
   
![Sammanfattning](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>Fakturering

![Fakturering](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>Efterlevnad

![Efterlevnad](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Storage

![Storage](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Säkerhetskopieringsobjekt
![BackupItems](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>Aviseringar

![Aviseringar](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>Jobb

![Jobb](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>Nästa steg:

* [Dela rapporter för hela organisationen](https://youtu.be/26zyOtyHPJM)
* [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
