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
ms.openlocfilehash: d77269c1e965d5bca1e32b756ef26e2c694e5c81
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747827"
---
**Krav för konfiguration och process Server**


## <a name="hardware-requirements"></a>Maskin varu krav

**Komponent** | **Krav** 
--- | ---
Processorkärnor | 8 
RAM | 16 GB
Antal diskar | 3, inklusive OS-disken, cache-disk för processerver och lagrings enhet för återställning efter fel 
Ledigt disk utrymme (bearbetnings serverns cacheminne) | 600 GB
Ledigt disk utrymme (kvarhållning av disk) | 600 GB
 | 

## <a name="software-requirements"></a>Programvarukrav

**Komponent** | **Krav** 
--- | ---
Operativsystem | Windows Server 2012 R2 <br> Windows Server 2016
Nationella inställningar för operativsystem | Engelska (en-us)
Windows Server-roller | Aktivera inte följande roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Grup principer | Aktivera inte dessa grup principer: <br> -Förhindra åtkomst till kommando tolken. <br> -Förhindra åtkomst till verktyg för redigering av registret. <br> – Förtroende logik för bifogade filer. <br> – Aktivera skript körning. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Ingen befintlig standard webbplats. <br> -Ingen befintlig webbplats eller ett program som lyssnar på port 443. <br>-Aktivera [Anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx). <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -inställningen.
| 

## <a name="network-requirements"></a>Nätverkskrav

**Komponent** | **Krav** 
--- | --- 
IP-adresstyp | Statisk 
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring) 
Typ av nätverkskort | VMXNET3 (om konfigurations servern är en virtuell VMware-dator)
 |
**Internet åtkomst** (servern behöver åtkomst till följande URL: er, direkt eller via proxy):|
\*.backup.windowsazure.com | Används för replikerad data överföring och samordning
\*.store.core.windows.net | Används för replikerad data överföring och samordning
\*.blob.core.windows.net | Används för att komma åt lagrings kontot som lagrar replikerade data
\*.hypervrecoverymanager.windowsazure.com | Används för hanterings åtgärder och samordning av replikering
https:\//management.azure.com | Används för hanterings åtgärder och samordning av replikering 
*.services.visualstudio.com | Används för telemetri (valfritt)
time.nist.gov | Används för att kontrol lera tidssynkronisering mellan system och global tid
time.windows.com | Används för att kontrol lera tidssynkronisering mellan system och global tid
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | OVF-installationen behöver åtkomst till dessa URL: er. De används för åtkomst kontroll och identitets hantering av Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | För att slutföra MySQL-nedladdning. </br> I några regioner kan hämtningen omdirigeras till CDN-URL: en. Se till att CDN-URL: en också vit listas, om det behövs.
|

## <a name="required-software"></a>Nödvändig program vara

**Komponent** | **Krav** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI version 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) bör installeras om konfigurations servern körs på en virtuell VMware-dator.
MySQL | MySQL ska installeras. Du kan installera manuellt, eller så kan Azure Site Recovery installera det. (Mer information finns i [Konfigurera inställningar](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings).)

## <a name="sizing-and-capacity-requirements"></a>Storleks-och kapacitets krav

I följande tabell sammanfattas kapacitets kraven för konfigurations servern. Om du replikerar flera virtuella VMware-datorer bör du gå igenom [kapacitets planerings överväganden](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) och köra [Distributionshanteraren för Azure Site Recovery-verktyget](../articles/site-recovery/site-recovery-deployment-planner.md).


**REGISTRERA** | **Minnesoptimerade** | **Cachelagra disk** | **Data ändrings takt** | **Replikerade datorer**
--- | --- | --- | --- | ---
8 virtuella processorer<br/><br/> 2 Sockets * 4 kärnor \@ 2,5 GHz | 16 GB | 300 GB | 500 GB eller mindre | < 100 datorer
12 virtuella processorer<br/><br/> 2 SOCKS * 6 kärnor \@ 2,5 GHz | 18 GB | 600 GB | 500 GB till 1 TB | 100 till 150-datorer
16 virtuella processorer<br/><br/> 2 SOCKS * 8 kärnor \@ 2,5 GHz | 32 GB | 1 TB | 1 till 2 TB | 150 till 200-datorer

