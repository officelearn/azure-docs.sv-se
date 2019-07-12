---
title: Azure Backup Server och DPM vanliga frågor och svar
description: 'Svar på vanliga frågor om: Azure Backup Server och DPM.'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 7a598038ee435b67b9ad8f06bdec2490bc1c53c3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705110"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server och DPM – vanliga frågor och svar
Den här artikeln får du svar på vanliga frågor och svar om Azure Backup Server och DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Kan jag använda Azure Backup Server för att skapa en BMR-säkerhetskopia (Bare Metal Recovery) för en fysisk server? <br/>
Ja.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Kan jag registrera servern till flera valv?
Nej. En DPM eller Azure Backup server kan registreras till ett enda valv.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Kan jag använda DPM för säkerhetskopiering av appar i Azure Stack?
Nej. Du kan använda Azure Backup för att skydda Azure Stack, Azure Backup stöder inte använder DPM för att säkerhetskopiera appar i Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Om jag har installerat Azure Backup-agenten för att skydda Mina filer och mappar kan jag installera System Center DPM att säkerhetskopiera lokala arbetsbelastningar till Azure?
Ja. Men du bör konfigurera DPM först och sedan installera Azure Backup-agenten.  Installera komponenterna i den här ordningen säkerställer att Azure Backup-agenten fungerar med DPM. Installera agenten innan du installerar DPM inte detta varken rekommenderas eller stöds.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Varför kan jag lägga till en extern DPM-server när du har installerat UR7 och senaste Azure Backup-agenten?
För DPM-servrar med datakällor som skyddas till molnet (med hjälp av en samlad uppdatering tidigare än Update Rollup-7) måste du vänta minst en dag efter att ha installerat UR7 och senaste Azure Backup-agenten för att starta **Lägg till extern DPM server**. Endagsevenemang tidsperiod behövs för att ladda upp metadata för DPM-skyddsgrupper till Azure. Skydd grupp metadata har laddats upp första gången via ett jobb varje natt.

## <a name="vmware-and-hyper-v-backup"></a>VMware och Hyper-V-säkerhetskopiering

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Kan jag säkerhetskopiera VMware vCenter-servrar till Azure?
Ja. Du kan använda Azure Backup Server för att säkerhetskopiera VMware vCenter-Server och ESXi-värdar till Azure.

- [Läs mer](backup-mabs-protection-matrix.md) om versioner som stöds.
- [Följ dessa steg](backup-azure-backup-server-vmware.md) att säkerhetskopiera en VMware-server.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Behöver jag en separat licens för att återställa en fullständig lokala VMware/Hyper-V-kluster?
Du behöver inte separata licensiering för VMware/Hyper-V-skydd.

- Om du är en System Center-kund kan du använda System Center Data Protection Manager (DPM) för att skydda virtuella VMware-datorer.
- Om du inte är en System Center-kund kan använda du Azure Backup Server (betala per användning) för att skydda virtuella VMware-datorer.


## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Kan jag återställa ett SharePoint-objekt till den ursprungliga platsen om SharePoint konfigureras med hjälp av SQL AlwaysOn (med skydd på disk)?
Ja, objektet kan återställas till den ursprungliga SharePoint-webbplatsen.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Kan jag återställa en SharePoint-databas till den ursprungliga platsen om SharePoint konfigureras med hjälp av SQL AlwaysOn?
Eftersom SharePoint-databaserna har konfigurerats i SQL AlwaysOn, kan inte de ändras om inte tillgänglighetsgruppen tas bort. Därför kan kan inte DPM återställa en databas till den ursprungliga platsen. Du kan återställa en SQL Server-databas till en annan SQL Server-instans.

## <a name="next-steps"></a>Nästa steg

Läs de andra vanliga frågor och svar:

- [Läs mer](backup-support-matrix-mabs-dpm.md) om Azure Backup Server och DPM har stöd för matrisen.
- [Läs mer](backup-azure-mabs-troubleshoot.md) om Azure Backup Server och DPM riktlinjer för felsökning.
