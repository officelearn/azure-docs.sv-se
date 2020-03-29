---
title: VM-SKU:er som stöds för Azure Policy
description: En artikel som beskriver de virtuella datorerna som stöds (av Publisher, Image Offer och Image SKU) som stöds för de inbyggda Azure-principer som tillhandahålls av säkerhetskopiering
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980119"
---
# <a name="supported-vm-skus-for-azure-policy"></a>VM-SKU:er som stöds för Azure Policy

Azure Backup innehåller en inbyggd princip (med Azure-princip) som kan tilldelas **alla virtuella Azure-datorer på en angiven plats i en prenumeration eller resursgrupp**. När den här principen tilldelas ett visst scope konfigureras alla nya virtuella datorer som skapas i det omfånget automatiskt för säkerhetskopiering till ett **befintligt valv på samma plats och prenumeration**. I tabellen nedan visas alla SKU:er för virtuella datorer som stöds av den här principen.

### <a name="supported-vms"></a>**Virtuella datorer som stöds**

**Principens namn:** Konfigurera säkerhetskopiering på virtuella datorer på en plats till ett befintligt centralt valv på samma plats

Bildutgivare | Bild erbjudande | Bild SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | - Jag vet inte vad du går för. Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | - Jag vet inte vad du går för. Windows Server 2012 Datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | - Jag vet inte vad du går för. Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter (2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter – Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | - Jag vet inte vad du går för. Windows Server 2016 Datacenter – Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | - Jag vet inte vad du går för. Windows Server 2016 Datacenter (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core med behållare (2016-Datacenter-med-behållare)
MicrosoftWindowsServer | WindowsServer | Värd för Windows Server 2016-fjärrskrivbordssession 2016 (2016-Datacenter-med-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | - Jag vet inte vad du går för. Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core med behållare (2019-Datacenter-Core-med-behållare)
MicrosoftWindowsServer | WindowsServer | - Jag vet inte vad du går för. Windows Server 2019 Datacenter Server Core med behållare (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | - Jag vet inte vad du går för. Windows Server 2019 Datacenter (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter med behållare (2019-Datacenter-med-behållare)
MicrosoftWindowsServer | WindowsServer | - Jag vet inte vad du går för. Windows Server 2019 Datacenter med behållare (2019-Datacenter-med-behållare-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-med-behållare-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-med-behållare-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Alla bildsckuser
MicrosoftSQLServer | SQL2016SP1-WS2016 | Alla bildsckuser
MicrosoftSQLServer | SQL2016-WS2016  | Alla bildsckuser
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Alla bildsckuser
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Alla bildsckuser
MicrosoftSQLServer | SQL2016-WS2012R2 | Alla bildsckuser
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Alla bildsckuser
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Alla bildsckuser
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Alla bildsckuser
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Alla bildsckuser
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Alla bildsckuser
MicrosoftRServer (MicrosoftRServer) | MLServer-WS2016 | Alla bildsckuser
MicrosoftVisualStudio | Visualstudio | Alla bildsckuser
MicrosoftVisualStudio | Windows | Alla bildsckuser
MicrosoftDynamicsAX | Dynamics  | Pre-Req-AX7-Onebox-U8
Microsoft-annonser | windows-data-science-vm | Alla bildsckuser
MicrosoftWindowsDesktop | Windows-10 | Alla bildsckuser
Redhat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
Redhat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X (på andra) sätt
SUSE | SLES-HPC | 12.X (på andra) sätt
SUSE | SLES-HPC-Prioritet | 12.X (på andra) sätt
SUSE | SLES-SAP | 12.X (på andra) sätt
SUSE | SLES-SAP-BYOS | 12.X (på andra) sätt
SUSE | SLES-Prioritet | 12.X (på andra) sätt
SUSE | SLES-BYOS | 12.X (på andra) sätt
SUSE | SLES-SAPCAL | 12.X (på andra) sätt
SUSE | SLES-standard | 12.X (på andra) sätt
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DAGLIGEN-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16,04-DAGLIGEN-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18,04-DAGLIGEN-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | CentOS–LVM | 6.X, 7.X
OpenLogic | CentOS–SRIOV | 6.X, 7.X
cloudera (molnig) | cloudera-centos-os | 7.X (på andra)
