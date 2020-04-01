---
title: Alternativ för datamigrering från StorSimple 8000-seriens enheter
description: Ger en översikt över alternativen för att migrera data från StorSimple 8000-serien.
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: f712486c8035bbc913fbd229759f3415a1005449
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438325"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Alternativ för att migrera data från StorSimple 8000-serien

> [!IMPORTANT]
> Den 31 december 2022 kommer StorSimple 8000-serien att nå slutet av supportstatus (EOS). Vi rekommenderar att Kunder i StorSimple 8000-serien migrerar till ett av alternativen som beskrivs i dokumentet.

StorSimple 8000-serien når [slutet av supporten](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) i december 2022. De kunder som kör StorSimple 8000-serien har möjlighet att uppgradera till andra Azure-hybridtjänster från första part. I den här artikeln beskrivs de Azure-hybridalternativ som är tillgängliga för att migrera data.

## <a name="migration-options"></a>Migreringsalternativ

Kunderna som använder StorSimple 8000-serien har Azure- eller tredjepartsalternativ.

### <a name="azure-options"></a>Azure-alternativ

#### <a name="migrate-to-azure-file-sync"></a>Migrera till Azure File Sync

Med det här helt nya migreringsalternativet kan kunder lagra organisationens filresurser i Azure-filerna. Dessa filresurser centraliseras sedan för lokal åtkomst med hjälp av Azure File Sync (AFS). AFS kan distribueras på en Windows Server-värd. Den faktiska datamigrering utförs sedan som en värdkopia eller med migreringsverktyget.

Mer information om hur du migrerar data till Azure File Sync finns i [StorSimple 8100 och 8600-migreringen till Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000).

<!-- 03/25/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
### Third-party options

#### Migrate to Panzura Freedom NAS

StorSimple 5000-7000 customers can choose to migrate to Panzura Freedom NAS to keep their data in Azure. Panzura Freedom solution provides a NAS solution that spans datacenters, offices, public and private clouds. The solution enables local, hybrid, and in-cloud data workflows for NFS, SMB, and mobile clients. 

This migration is supported by Panzura and customers can get started by requesting migration support from the [Panzura website](https://panzura.com/storsimple-migration/).

#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migration - Vanliga frågor och svar

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>F. När når StorSimple 8000-seriens enheter tjänsten?

A. StorSimple 8000-serien når [supportens slut](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) i december 2022. Supporten upphör innebär att Microsoft inte längre kommer att kunna ge stöd för både maskinvara och programvara för dessa enheter efter december 2022. Vi rekommenderar starkt att du börjar formulera en plan för att migrera data från dina enheter nu.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>F. Vad händer med de data som jag har lagrat i Azure?  

A. Du kan fortsätta att använda data i Azure när du migrerar dem till en nyare tjänst.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>F. Vad händer med de data som jag har lagrat lokalt på min StorSimple-enhet?

A. Data som finns på den lokala enheten kan kopieras till den nyare tjänsten enligt beskrivningen i migreringsdokumenten.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>F. Vad händer om jag vill behålla min StorSimple 8000-serieapparat?

A. Tjänsterna kan fortsätta att fungera, men Microsoft kan inte längre tillhandahålla maskinvaru- och programvarusupport. Migrering rekommenderas starkt för kontinuitet i verksamheten.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>F. Vilka alternativ är tillgängliga för att migrera data från StorSimple 8000-seriens enheter?

A. Beroende på deras scenario har StorSimple 8000-seriens användare följande migreringsalternativ:

* **Migrera till Azure File Sync**: Använd det här alternativet när du vill växla till Azure-inbyggt format. Du kan använda Azure File Sync för centraliserad hantering av filresurser.

* **Andra alternativ:** Du kan kontakta Microsoft Support för att diskutera migreringsalternativ som inte finns med här.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>F. Stöds migrering till andra lagringslösningar?

A. Ja. Migrering till andra lagringslösningar med hjälp av värdkopia av data stöds.

### <a name="q-is-migration-supported-by-microsoft"></a>F. Stöds migreringen av Microsoft?

A. Migrering från 8000-serien är en fullt stödåtgärd. Faktum är att Microsoft rekommenderar att du når ut till supporten innan du startar migreringen. Migreringen är för närvarande en assisterad åtgärd. Om du tänker migrera data från storsimple 8000-serien kontaktar [du StorSimple-supporten](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>F. Vad är prismodellen för migrering till Azure File Sync?

A. När du använder Azure File Sync kan prenumerationsavgifterna för tjänsten tillkomma. Kunderna måste också betala löpande lagringskostnader. Se [AFS-priser]( https://azure.microsoft.com/pricing/details/storage/files/) för en uppskattning.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>F. Hur lång tid tar det att slutföra en migrering?

A. Tiden för att migrera data beror på mängden data och det uppgraderingsalternativ som valts.

## <a name="next-steps"></a>Nästa steg

* [Migrera data från en StorSimple 8000-serie till Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
