---
title: ta med fil
description: ta med fil
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 601819756b78ffe8762bdfbfd5f802bc2d76e9c5
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35268061"
---
**Konfigurationsprocessen/server-krav**

**Komponent** | **Krav** 
--- | ---
**MASKINVARUINSTÄLLNINGAR** | 
Processorkärnor | 8 
RAM | 16 GB
Antal diskar | 3, inklusive OS-disk, disk för processen server cache och kvarhållningsenhetens för återställning efter fel 
Ledigt diskutrymme (processen servercache) | 600 GB
Ledigt diskutrymme (kvarhållningsdisken) | 600 GB
 | 
**PROGRAMINSTÄLLNINGAR** | 
Operativsystem | Windows Server 2012 R2 <br> Windows Server 2016
Nationella inställningar för operativsystem | Engelska (en-us)
Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Grupprinciper | Aktivera inte dessa grupp-principer: <br> -Förhindra åtkomst till Kommandotolken. <br> -Förhindra åtkomst till verktyg för redigering av registret. <br> -Förtroende för bifogade filer. <br> -Aktivera körning av skript. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Ingen redan befintliga standardwebbplatsen <br> -Ingen befintliga webbplats/programmet lyssnar på port 443 <br>-Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställning 
| 
**NÄTVERKSINSTÄLLNINGAR** | 
IP-adresstyp | Statisk 
Internet-åtkomst | Servern behöver åtkomst till dessa URL: er (direkt eller via proxy) <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (om du konfigurerar en server configuration) <br> - time.nist.gov <br> - time.windows.com 
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring) 
NIC-typ | VMXNET3 (om konfigurationsservern är en VMware-VM)
 | 
**PROGRAMVARA FÖR ATT INSTALLERA** | 
VMware vSphere PowerCLI | [PowerCLI version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) bör installeras om konfigurationsservern körs på en VMware-VM.
MYSQL | MySQL ska installeras. Du kan installera manuellt eller Site Recovery kan du installera den.

**Konfigurationsprocessen/server storleksanpassa krav**

**CPU** | **Minne** | **Cache-disk** | **Dataändringshastighet** | **Replikerade datorer**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 sockets * 4 kärnor @ 2,5 GHz | 16GB | 300 GB | 500 GB eller mindre | < 100 datorer
12 vCPUs<br/><br/> 2 socks * 6 kärnor @ 2,5 GHz | 18 GB | 600 GB | 500 GB - 1 TB | 100-150 datorer
16 vCPUs<br/><br/> 2 socks * 8 kärnor @ 2,5 GHz | 32 GB | 1 TB | 1 – 2 TB | 150-200 datorer

