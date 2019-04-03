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
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887023"
---
**Configuration/Process server-krav för fysisk serverreplikering**

**Komponent** | **Krav** 
--- | ---
**MASKINVARUINSTÄLLNINGAR** | 
Processorkärnor | 8 
RAM | 16 GB
Antal diskar | 3, inklusive OS-disk, processerverns cachedisk och kvarhållningsenhet för återställning efter fel 
Ledigt diskutrymme (processerverns cacheminne) | 600 GB
Ledigt diskutrymme (kvarhållningsdisken) | 600 GB
 | 
**PROGRAMINSTÄLLNINGAR** | 
Operativsystem | Windows Server 2012 R2 <br> Windows Server 2016
Nationella inställningar för operativsystem | Engelska (en-us)
Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Grupprinciper | Aktivera inte dessa grupp-principer: <br> -Förhindra åtkomst till Kommandotolken. <br> -Förhindra åtkomst till registerredigeringsverktygen. <br> -Förtroende för bifogade filer. <br> -Aktivera körning av skript. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Ingen befintlig standardwebbplatsen <br> – Ingen befintlig webbplats/program lyssnar på port 443 <br>-Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställningen.
IP-adresstyp | Statisk 
| 
**ÅTKOMSTINSTÄLLNINGAR** | 
MYSQL | MySQL bör vara installerad på konfigurationsservern. Du kan installera manuellt eller Site Recovery kan du installera det under distributionen. Kontrollera att datorn kan nå för Site Recovery att installera, http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
URL: er | Konfigurationsservern behöver åtkomst till dessa URL: er (direkt eller via proxy):<br/><br/> Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`; `*.accesscontrol.windows.net`<br/><br/> Överföring av replikeringsdata: `*.backup.windowsazure.com`; `*.backup.windowsazure.us`<br/><br/> Replikeringshantering: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`; `*.services.visualstudio.com`<br/><br/> Åtkomst till lagringsutrymmen: `*.blob.core.windows.net`; `*.blob.core.usgovcloudapi.net`<br/><br/> Tidssynkronisering: `time.nist.gov`; `time.windows.com`<br/><br/> Telemetri (valfritt): `dc.services.visualstudio.com`
Brandvägg | IP-adressbaserade brandväggsregler ska tillåta kommunikation till Azure-URL: er. För att förenkla och begränsa IP-adressintervall, bör du använda URL-filtrering.<br/><br/>**För kommersiella IP-adresser:**<br/><br/>-Tillåt den [Azure Datacenter IP-intervall](https://www.microsoft.com/download/confirmation.aspx?id=41653), och HTTPS-port (443).<br/><br/> -Tillåt IP-adressintervall för västra USA (används för Access Control och Identitetshantering).<br/><br/> -Tillåt IP-adressintervall för Azure-regionen för din prenumeration, som stöd för URL: er som behövs för Azure Active Directory, säkerhetskopiering, replikering och lagring.<br/><br/> **För myndigheter IP-adresser:**<br/><br/> -Tillåt den offentliga IP-intervallen för Azures Datacenter och HTTPS-port (443).<br/><br/> -Tillåt IP-adressintervall för alla US Gov-regioner (Virginia, Texas, Arizona och Iowa), som stöd för URL: er som behövs för Azure Active Directory, säkerhetskopiering, replikering och lagring.
Portar | Tillåt 443 (kontrolkanalsorchestration)<br/><br/> Tillåt 9443 (dataöverföring) 


**Konfigurations/processervern ändrar storlek på krav**

**Processor** | **Minne** | **Cachedisk** | **Dataändringshastigheten** | **Replikerade datorer**
--- | --- | --- | --- | ---
8 virtuella processorer<br/><br/> 2 platser * 4 kärnor \@ 2,5 GHz | 16GB | 300 GB | 500 GB eller mindre | < 100 datorer
12 virtuella processorer<br/><br/> 2 socks * 6 kärnor \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100-150 datorer
16 vcpu: er<br/><br/> 2 socks * 8 kärnor \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 datorer

