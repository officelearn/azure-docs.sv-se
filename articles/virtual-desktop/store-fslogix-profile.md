---
title: Lagring FSLogix Profile container Windows Virtual Desktop – Azure
description: Alternativ för att lagra din Windows FSLogix-profil för virtuella skriv bord på Azure Storage.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
ms.openlocfilehash: 3577b459a1b6612d24346428512cfbc509515f52
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607414"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Lagrings alternativ för FSLogix profil behållare i Windows Virtual Desktop

Azure erbjuder flera lagrings lösningar som du kan använda för att lagra din FSLogix-profil behållare. I den här artikeln jämförs lagrings lösningar Azure Storage erbjudanden för användar profiler i Windows Virtual Desktop-FSLogix profil behållare.

Windows Virtual Desktop erbjuder FSLogix profil behållare som den rekommenderade användar profil lösningen. FSLogix är utformad för centrala profiler i fjärrdatorer, till exempel Windows Virtual Desktop. Vid inloggning är den här behållaren dynamiskt kopplad till dator miljön med hjälp av en inbyggd virtuell hård disk som stöds och en virtuell hård disk för Hyper-V (VHDX). Användar profilen är omedelbart tillgänglig och visas i systemet precis som en inbyggd användar profil.

I följande tabeller jämförs lagrings lösningarna Azure Storage erbjudanden för användar profiler i Windows Virtual Desktop-FSLogix profil behållare.

## <a name="azure-platform-details"></a>Information om Azure-plattformen

|Funktioner|Azure Files|Azure NetApp Files|Storage Spaces Direct|
|--------|-----------|------------------|---------------------|
|Plattforms tjänst|Ja, Azure – inbyggd lösning|Ja, Azure – inbyggd lösning|Ingen, egen hantering|
|Regional tillgänglighet|Alla regioner|[Välj regioner](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Alla regioner|
|Redundans|Lokalt redundant/zon-redundant/Geo-redundant|Lokalt redundant|Lokalt redundant/zon-redundant/Geo-redundant|
|Nivåer och prestanda|Standard<br>Premium<br>Upp till max 100 000 IOPS per resurs med 5 Gbit/s per resurs med cirka 3 MS svars tid|Standard<br>Premium<br>Extremt<br>Upp till 320k (16K) IOPS med 4,5 Gbit/s per volym vid ungefär 1 MS svars tid|Standard HDD: gränser för upp till 500 IOPS per disk<br>Standard SSD: upp till 4 000 IOPS-gränser per disk<br>Premium SSD: upp till 20 000 IOPS per disk<br>Vi rekommenderar Premium diskar för Lagringsdirigering|
|Kapacitet|100 TiB per resurs|100 TiB per volym, upp till 12,5 PiB per prenumeration|Maximalt 32 TiB per disk|
|Nödvändig infrastruktur|Minsta resurs storlek 1 GiB|Pool för minsta kapacitet 4 TiB, minsta volym storlek 100 GiB|Två virtuella datorer på Azure IaaS (+ Cloud vittne) eller minst tre virtuella datorer utan och kostnader för diskar|
|Protokoll|SMB 2.1/3. och REST|NFSv3, NFSv 4.1 (för hands version), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Information om Azure-hantering

|Funktioner|Azure Files|Azure NetApp Files|Storage Spaces Direct|
|--------|-----------|------------------|---------------------|
|Access|Moln, lokalt och hybrid (Azure File Sync)|Moln, lokalt (via ExpressRoute)|Moln, lokalt|
|Backup|Integrering av Azure Backup-ögonblicksbild|Azure NetApp Files ögonblicks bilder|Integrering av Azure Backup-ögonblicksbild|
|Säkerhet och efterlevnad|[Alla Azure-certifikat som stöds](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO slutförd|[Alla Azure-certifikat som stöds](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory-integrering|Azure Active Directory och Azure Active Directory Domain Services|[Azure Active Directory Domain Services och ursprunglig Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Interna Active Directory eller Azure Active Directory Domain Services endast stöd|

När du har valt din lagrings metod kan du titta på [priser för virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-desktop/) för information om våra prissättnings planer.

## <a name="next-steps"></a>Nästa steg

Mer information om FSLogix profil behållare, användar profil diskar och andra tekniker för användar profiler finns i tabellen i [FSLogix profil behållare och Azure Files](fslogix-containers-azure-files.md).

Kom igång med någon av de här självstudierna om du är redo att skapa dina egna FSLogix-profil behållare:

- [Komma igång med FSLogix profil behållare på Azure Files i det virtuella Windows-skrivbordet](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Skapa en FSLogix profil behållare för en värd-pool med Azure NetApp-filer](create-fslogix-profile-container.md)
- Anvisningarna i [distribuera en skalbar fil server med två noder Lagringsdirigering för UPD-lagring i Azure](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) gäller också när du använder en FSLogix profil behållare i stället för en användar profil disk

Du kan också börja från början och konfigurera din egen Windows-lösning för virtuella skriv bord genom att [skapa en klient i det virtuella Windows-skrivbordet](tenant-setup-azure-active-directory.md).
