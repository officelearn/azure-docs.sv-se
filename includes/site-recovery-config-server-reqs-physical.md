---
title: ta med fil
description: ta med fil
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187569"
---
**Serverkrav för konfiguration/process för fysisk serverreplikering**

**Komponent** | **Krav** 
--- | ---
**INSTÄLLNINGAR FÖR MASKINVARA** | 
Processorkärnor | 8 
RAM | 16 GB
Antal diskar | 3, inklusive OS-disken, processservercachedisken och kvarhållningsenheten för återställning av fel 
Ledigt diskutrymme (processservercache) | 600 GB
Ledigt diskutrymme (kvarhållningsdisk) | 600 GB
 | 
**INSTÄLLNINGAR FÖR PROGRAMVARA** | 
Operativsystem | Windows Server 2012 R2 <br> Windows Server 2016
Nationella inställningar för operativsystem | Engelska (en-us)
Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Grupprinciper | Aktivera inte dessa grupprinciper: <br> - Förhindra åtkomst till kommandotolken. <br> - Förhindra åtkomst till registerredigeringsverktyg. <br> - Förtroendelogik för bifogade filer. <br> - Aktivera skriptkörning. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Ingen befintlig standardwebbplats <br> - Ingen befintlig webbplats / ansökan lyssna på port 443 <br>- Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Aktivera [FastCGI-inställning.](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
IP-adresstyp | Statisk 
| 
**INSTÄLLNINGAR FÖR ÅTKOMST** | 
Mysql | MySQL bör installeras på konfigurationsservern. Du kan installera manuellt eller site recovery kan installera det under distributionen. Kontrollera att enheten kan nå http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
 URL:er | Konfigurationsservern behöver åtkomst till dessa webbadresser (direkt eller via proxy):<br/><br/> Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`;`*.accesscontrol.windows.net`<br/><br/> Överföring av `*.backup.windowsazure.com`replikeringsdata: ;`*.backup.windowsazure.us`<br/><br/> Replikeringshantering: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`;`*.services.visualstudio.com`<br/><br/> Tillgång till `*.blob.core.windows.net`lagring: ;`*.blob.core.usgovcloudapi.net`<br/><br/> Tidssynkronisering: `time.nist.gov`;`time.windows.com`<br/><br/> Telemetri (valfritt):`dc.services.visualstudio.com`
Brandvägg | IP-adressbaserade brandväggsregler bör tillåta kommunikation till Azure-url:er. För att förenkla och begränsa IP-intervallen rekommenderar vi att du använder URL-filtrering.<br/><br/>**För kommersiella IPs:**<br/><br/>- Tillåt [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653)och HTTPS-porten (443).<br/><br/> - Tillåt IP-adressintervall för västra USA (används för åtkomstkontroll och identitetshantering).<br/><br/> - Tillåt IP-adressintervall för Azure-regionen för din prenumeration, för att stödja de url:er som behövs för Azure Active Directory, säkerhetskopiering, replikering och lagring.<br/><br/> **För myndighets-IPs:**<br/><br/> - Tillåt Azure Government Datacenter IP Ranges och HTTPS-porten (443).<br/><br/> - Tillåt IP-adressintervall för alla amerikanska govregioner (Virginia, Texas, Arizona och Iowa) för att stödja de webbadresser som behövs för Azure Active Directory, säkerhetskopiering, replikering och lagring.
Portar | Tillåt 443 (Kontrollkanalorkestrering)<br/><br/> Tillåt 9443 (Datatransport) 


**Krav på storlek på konfigurations-/processserver**

**Cpu** | **Minne** | **Cachedisk** | **Dataändringshastighet** | **Replikerade datorer**
--- | --- | --- | --- | ---
8 virtuella processorer<br/><br/> 2 uttag * 4 \@ kärnor 2,5 GHz | 16 GB | 300 GB | 500 GB eller mindre | < 100 maskiner
12 virtuella processorer<br/><br/> 2 strumpor * 6 \@ kärnor 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 till 150 maskiner
16 virtuella processorer<br/><br/> 2 strumpor * 8 \@ kärnor 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 maskiner

