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
ms.openlocfilehash: 1aaec104e9130eeef723c6505e04e3317271566b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234135"
---
**Konfigurations- och processserverkrav**


## <a name="hardware-requirements"></a>Maskinvarukrav

**Komponent** | **Krav** 
--- | ---
Processorkärnor | 8 
RAM | 16 GB
Antal diskar | 3, inklusive OS-disken, processservercachedisken och kvarhållningsenheten för återställning av fel 
Ledigt diskutrymme (processservercache) | 600 GB
Ledigt diskutrymme (kvarhållningsdisk) | 600 GB
 | 

## <a name="software-requirements"></a>Programvarukrav

**Komponent** | **Krav** 
--- | ---
Operativsystem | Windows Server 2012 R2 <br> Windows Server 2016
Nationella inställningar för operativsystem | Engelska (en-*)
Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Grupprinciper | Aktivera inte dessa grupprinciper: <br> - Förhindra åtkomst till kommandotolken. <br> - Förhindra åtkomst till registerredigeringsverktyg. <br> - Förtroendelogik för bifogade filer. <br> - Aktivera skriptkörning. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Ingen befintlig standardwebbplats <br> - Ingen befintlig webbplats /ansökan lyssna på port 443 <br>- Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Aktivera [FastCGI-inställning](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
FIPS (Federala standarder för informationsbehandling) | Aktivera inte FIPS-läge
|

## <a name="network-requirements"></a>Nätverkskrav

**Komponent** | **Krav** 
--- | --- 
IP-adresstyp | Statisk 
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring) 
Typ av nätverkskort | VMXNET3 (om konfigurationsservern är en virtuell virtuell VMware-dator)
 |
**Internetåtkomst** (servern behöver tillgång till följande webbadresser, direkt eller via proxy):|
\*.backup.windowsazure.com | Används för replikerad dataöverföring och samordning
\*.store.core.windows.net | Används för replikerad dataöverföring och samordning
\*.blob.core.windows.net | Används för att komma åt lagringskonto som lagrar replikerade data
\*.hypervrecoverymanager.windowsazure.com | Används för replikeringshantering och samordning
https:\//management.azure.com | Används för replikeringshantering och samordning 
*.services.visualstudio.com | Används för telemetri (valfritt)
time.nist.gov | Används för att kontrollera tidssynkronisering mellan system och global tid
time.windows.com | Används för att kontrollera tidssynkronisering mellan system och global tid
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF-installationen behöver åtkomst till dessa webbadresser. De används för åtkomstkontroll och identitetshantering av Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | För att slutföra MySQL nedladdning. </br> I några få regioner kan hämtningen omdirigeras till CDN-URL:en. Kontrollera att CDN-URL:en också är vitlistad om det behövs.
|

## <a name="required-software"></a>Programvara som krävs

**Komponent** | **Krav** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) ska installeras om konfigurationsservern körs på en virtuell virtuell VMware-dator.
Mysql | MySQL bör installeras. Du kan installera manuellt eller site recovery kan installera det. (Mer information finns i [konfigurera inställningar)](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings)
|

## <a name="sizing-and-capacity-requirements"></a>Storleks- och kapacitetskrav

I följande tabell sammanfattas kapacitetskraven för konfigurationsservern. Om du replikerar flera virtuella datorer med VMware läser du igenom [kapacitetsplaneringsövervägandena](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) och kör [verktyget Azure Site Recovery Deployment Planner](../articles/site-recovery/site-recovery-deployment-planner.md).


**Cpu** | **Minne** | **Cachedisk** | **Dataändringshastighet** | **Replikerade datorer**
--- | --- | --- | --- | ---
8 virtuella processorer<br/><br/> 2 uttag * 4 \@ kärnor 2,5 GHz | 16 GB | 300 GB | 500 GB eller mindre | < 100 maskiner
12 virtuella processorer<br/><br/> 2 strumpor * 6 \@ kärnor 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 till 150 maskiner
16 virtuella processorer<br/><br/> 2 strumpor * 8 \@ kärnor 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 maskiner
|

