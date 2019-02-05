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
ms.openlocfilehash: 4346b347994f49774584caf31a96ff2f81fdc0e1
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701224"
---
**Configuration/Process server-krav**

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
IIS | – Ingen befintlig standardwebbplatsen <br> – Ingen befintlig webbplats/program lyssnar på port 443 <br>-Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställningen 
| 
**NÄTVERKSINSTÄLLNINGAR** | 
IP-adresstyp | Statisk 
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring) 
Typ av nätverkskort | VMXNET3 (om konfigurationsservern är en VMware-VM)
 |
**Internetåtkomst** (servern behöver åtkomst till följande URL: er – direkt eller via proxy):|
\*.backup.windowsazure.com | Används för överföring av replikerade data och samordning
\*.store.core.windows.net | Används för överföring av replikerade data och samordning
\*.blob.core.windows.net | Används för åtkomst till lagringskontot som lagrar replikerade data
\*.hypervrecoverymanager.windowsazure.com | Används för replikeringshantering och koordination
https:\//management.azure.com | Används för replikeringshantering och koordination 
*.services.visualstudio.com | Används för telemetri (det är valfritt)
time.nist.gov | Används för att kontrollera tidssynkronisering mellan system och global tid.
time.windows.com | Används för att kontrollera tidssynkronisering mellan system och global tid.
-https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> -https:\//login.live.com  <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com | Konfigurera OVF behöver åtkomst till dessa URL: er. De används för access control och Identitetshantering av Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Slutför nedladdningen av MySQL
|
**PROGRAMVARA FÖR ATT INSTALLERA** | 
VMware vSphere PowerCLI | [PowerCLI version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) ska installeras om konfigurationsservern körs på en VMware-VM.
MYSQL | MySQL ska installeras. Du kan installera manuellt eller Site Recovery kan du installera den. (Se [konfigurera inställningar för](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) för mer information)

**Konfigurations/processervern ändrar storlek på krav**

**CPU** | **Minne** | **Cachedisk** | **Dataändringshastigheten** | **Replikerade datorer**
--- | --- | --- | --- | ---
8 virtuella processorer<br/><br/> 2 platser * 4 kärnor \@ 2,5 GHz | 16GB | 300 GB | 500 GB eller mindre | < 100 datorer
12 virtuella processorer<br/><br/> 2 socks * 6 kärnor \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100-150 datorer
16 vcpu: er<br/><br/> 2 socks * 8 kärnor \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 datorer

