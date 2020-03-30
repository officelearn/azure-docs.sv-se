---
title: Vanliga frågor och svar om Azure Backup Server och DPM
description: I den här artikeln kan du hitta svar på vanliga frågor om Microsoft Azure Backup Server (MABS) och DPM (Data Protection Manager).
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 35957a1e8a3d6c3d9be06d9d44dbcd47efa0e6ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173164"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server och DPM – Vanliga frågor och svar

## <a name="general-questions"></a>Allmänna frågor

Den här artikeln besvarar vanliga frågor om Azure Backup Server och DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Kan jag använda Azure Backup Server för att skapa en BMR-säkerhetskopia (Bare Metal Recovery) för en fysisk server?

Ja.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Kan jag registrera servern till flera valv?

Nej. En DPM- eller Azure Backup-server kan bara registreras på ett valv.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Kan jag använda DPM för att säkerhetskopiera appar i Azure Stack?

Nej. Du kan använda Azure Backup för att skydda Azure Stack, Azure Backup stöder inte att använda DPM för att säkerhetskopiera appar i Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Om jag har installerat Azure Backup-agent för att skydda mina filer och mappar, kan jag installera System Center DPM för att säkerhetskopiera lokala arbetsbelastningar till Azure?

Ja. Men du bör konfigurera DPM först och sedan installera Azure Backup-agenten.  Installera komponenter i den här ordningen säkerställer att Azure Backup agent fungerar med DPM. Det rekommenderas inte eller stöds inte att installera agenten innan DPM installeras.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Varför kan jag inte lägga till en extern DPM-server när jag har installerat UR7 och den senaste Azure Backup-agenten?

För DPM-servrar med datakällor som är skyddade i molnet (med hjälp av en samlad uppdatering tidigare än samlad uppdatering 7) måste du vänta minst en dag efter installation av UR7 och senaste Azure Backup-agent för att starta Lägg till **extern DPM-server**. Endagsperioden behövs för att överföra metadata för DPM-skyddsgrupperna till Azure. Metadata för skyddsgrupp överförs första gången genom ett nattligt jobb.

## <a name="vmware-and-hyper-v-backup"></a>VMware och Hyper-V-säkerhetskopiering

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Kan jag säkerhetskopiera VMware vCenter-servrar till Azure?

Ja. Du kan använda Azure Backup Server för att säkerhetskopiera VMware vCenter Server- och ESXi-värdar till Azure.

- [Läs mer](backup-mabs-protection-matrix.md) om versioner som stöds.
- [Följ dessa steg](backup-azure-backup-server-vmware.md) för att säkerhetskopiera en VMware-server.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Behöver jag en separat licens för att återställa ett komplett lokalt VMware/Hyper-V-kluster?

Du behöver inte separat licensiering för VMware/Hyper-V-skydd.

- Om du är systemcenterkund använder du System Center Data Protection Manager (DPM) för att skydda virtuella datorer med VMware.
- Om du inte är systemcenterkund kan du använda Azure Backup Server (pay-as-you-go) för att skydda virtuella datorer med VMware.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Kan jag återställa ett SharePoint-objekt till den ursprungliga platsen om SharePoint konfigureras med SQL AlwaysOn (med skydd på disk)?

Ja, objektet kan återställas till den ursprungliga SharePoint-webbplatsen.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Kan jag återställa en SharePoint-databas till den ursprungliga platsen om SharePoint konfigureras med SQL AlwaysOn?

Eftersom SharePoint-databaser är konfigurerade i SQL AlwaysOn kan de inte ändras om inte tillgänglighetsgruppen tas bort. DPM kan därför inte återställa en databas till den ursprungliga platsen. Du kan återställa en SQL Server-databas till en annan SQL Server-instans.

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

- [Läs mer](backup-support-matrix-mabs-dpm.md) om Azure Backup Server och DPM-supportmatris.
- [Läs mer](backup-azure-mabs-troubleshoot.md) om felsökningsriktlinjerna för Azure Backup Server och DPM.
