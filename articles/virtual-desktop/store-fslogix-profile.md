---
title: Lagring FSLogix-profilbehållare Windows Virtual Desktop - Azure
description: Alternativ för lagring av din Windows Virtual Desktop FSLogix-profil på Azure Storage.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127530"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Lagringsalternativ för FSLogix-profilbehållare i Windows Virtual Desktop

Azure erbjuder flera lagringslösningar som du kan använda för att lagra din FSLogix-profilbehållare. I den här artikeln jämförs lagringslösningar som Azure erbjuder för Windows Virtual Desktop FSLogix-användarprofilbehållare.

Windows Virtual Desktop erbjuder FSLogix profilbehållare som den rekommenderade användarprofillösningen. FSLogix är utformat för att flytta profiler i fjärrdatormiljöer, till exempel Windows Virtual Desktop. Vid inloggning är den här behållaren dynamiskt ansluten till datormiljön med hjälp av en inbyggd virtuell hårddisk (VHD) och en Hyper-V Virtual Hard Disk (VHDX). Användarprofilen är omedelbart tillgänglig och visas i systemet precis som en inbyggd användarprofil.

I följande tabeller jämförs de lagringslösningar som Azure Storage erbjuder för användarprofiler för Windows Virtual Desktop FSLogix-profilbehållare.

## <a name="azure-platform-details"></a>Information om Azure-plattformen

|Funktioner|Azure Files|Azure NetApp Files|Lagringsutrymmen direkt|
|--------|-----------|------------------|---------------------|
|Användningsfall|Generellt syfte|Ultraprestanda eller migrering från NetApp lokalt|Plattformsoberoende|
|Plattformstjänst|Ja, Azure-inbyggd lösning|Ja, Azure-inbyggd lösning|Nej, självförvaltad|
|Regional tillgänglighet|Alla regioner|[Välj områden](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Alla regioner|
|Redundans|Lokalt redundant/zon-redundant/geo-redundant|Lokalt redundant|Lokalt redundant/zon-redundant/geo-redundant|
|Nivåer och prestanda|Standard<br>Premium<br>Upp till max 100k IOPS per aktie med 5 GBps per aktie vid ca 3 ms latens|Standard<br>Premium<br>Ultra<br>Upp till 320k (16K) IOPS med 4,5 GBps per volym vid ca 1 ms latens|Standard HDD: upp till 500 IOPS per disk-gränser<br>Standard SSD: upp till 4k IOPS per disk-gränser<br>Premium SSD: upp till 20 k IOPS per disk-gränser<br>Vi rekommenderar Premium-diskar för Storage Spaces Direct|
|Kapacitet|100 TiB per aktie|100 TiB per volym, upp till 12,5 PiB per abonnemang|Max 32 TiB per disk|
|Nödvändig infrastruktur|Minsta andelsstorlek 1 GiB|Minsta kapacitet pool 4 TiB, min volymstorlek 100 GiB|Två virtuella datorer på Azure IaaS (+ Cloud Witness) eller minst tre virtuella datorer utan och kostnader för diskar|
|Protokoll|SMB 2.1/3. och REST|NFSv3, NFSv4.1 (förhandsgranskning), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3,1|

## <a name="azure-management-details"></a>Information om Azure-hantering

|Funktioner|Azure Files|Azure NetApp Files|Lagringsutrymmen direkt|
|--------|-----------|------------------|---------------------|
|Åtkomst|Moln, lokalt och hybrid (Azure-filsynkronisering)|Moln, lokalt (via ExpressRoute)|Moln, lokalt|
|Säkerhetskopiering|Integrering av ögonblicksbilder för Azure-säkerhetskopiering|Ögonblicksbilder av Azure NetApp-filer|Integrering av ögonblicksbilder för Azure-säkerhetskopiering|
|Säkerhet och efterlevnad|[Alla Azure-certifikat som stöds](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO klar|[Alla Azure-certifikat som stöds](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory-integrering|[Native Active Directory och Azure Active Directory Domain Services](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure Active Directory Domain Services och Inbyggd Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Native Active Directory eller Azure Active Directory Domain Services endast stöd för Active Directory|

När du har valt din lagringsmetod kan du läsa priser för [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) för information om våra prisplaner.

## <a name="next-steps"></a>Nästa steg

Mer information om FSLogix-profilbehållare, användarprofildiskar och andra användarprofiltekniker finns i tabellen i [FSLogix-profilbehållare och Azure-filer](fslogix-containers-azure-files.md).

Om du är redo att skapa dina egna FSLogix-profilbehållare kan du komma igång med någon av följande självstudier:

- [Komma igång med FSLogix-profilbehållare på Azure-filer i Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Skapa en FSLogix-profilbehållare för en värdpool med Azure NetApp-filer](create-fslogix-profile-container.md)
- Instruktionerna i [Distribuera en tvånodslagringsplats Direkt utskalningsfilserver för UPD-lagring i Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) gäller även när du använder en FSLogix-profilbehållare i stället för en användarprofildisk

Du kan också börja från början och konfigurera din egen Windows Virtual Desktop-lösning på [Skapa en klient i Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
