---
title: Configuration server-krav för haveriberedskap för VMware till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver krav och support när du distribuerar konfigurationsservern för VMware-haveriberedskap till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 399dcd744819cf4cb5d9f5f9636967c34e186a0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921010"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Configuration server-krav för haveriberedskap för VMware till Azure

Du distribuerar en lokal konfigurationsserver när du använder [Azure Site Recovery](site-recovery-overview.md) för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure.

- Den configuration server koordinater kommunikationen mellan lokala VMware- och Azure. Den hanterar också replikering av data.
- [Läs mer](vmware-azure-architecture.md) om konfigurationsserverns komponenter och processer.

## <a name="configuration-server-deployment"></a>Distribution av konfigurationsserver

För haveriberedskap för virtuella VMware-datorer till Azure distribuera konfigurationsservern som en VMware VM.

- Site Recovery tillhandahåller en OVA-mall som du hämtar från Azure-portalen och importera till vCenter-servern och konfigurera konfigurationsservern VM.
- När du distribuerar konfigurationsservern med hjälp av OVA-mallen, uppfyller den virtuella datorn automatiskt de krav som anges i den här artikeln.
- Vi rekommenderar starkt att du ställer in konfigurationsservern med hjälp av OVA-mallen. Men om du konfigurerar haveriberedskap för virtuella VMware-datorer och kan inte använda OVA-mall, kan du distribuera konfiguration av servern med [de här instruktionerna](physical-azure-set-up-source.md).
- Om du distribuerar konfigurationsservern för haveriberedskap för lokala fysiska datorer till Azure, följer du anvisningarna i [i den här artikeln](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Maskinvarukrav

**Komponent** | **Krav** 
--- | ---
Processorkärnor | 8 
RAM | 16 GB
Antal diskar | 3, inklusive OS-disk, processerverns cachedisk och kvarhållningsenhet för återställning efter fel 
Ledigt diskutrymme (processerverns cacheminne) | 600 GB
Ledigt diskutrymme (kvarhållningsdisken) | 600 GB

## <a name="software-requirements"></a>Programvarukrav

**Komponent** | **Krav** 
--- | ---
Operativsystem | Windows Server 2012 R2 <br> Windows Server 2016
Nationella inställningar för operativsystem | Engelska (en-us)
Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Grupprinciper | Aktivera inte dessa grupp-principer: <br> -Förhindra åtkomst till Kommandotolken. <br> -Förhindra åtkomst till registerredigeringsverktygen. <br> -Förtroende för bifogade filer. <br> -Aktivera körning av skript. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | – Ingen befintlig standardwebbplatsen <br> – Ingen befintlig webbplats/program lyssnar på port 443 <br>-Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställningen 

## <a name="network-requirements"></a>Nätverkskrav

**Komponent** | **Krav** 
--- | --- 
IP-adresstyp | Statisk 
Internet-åtkomst | Servern måste ha åtkomst till dessa URL: er (direkt eller via proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> -https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF måste också ha åtkomst till följande webbadresser: <br> -https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> -https:\//login.live.com  <br> -https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> -https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring) 
Typ av nätverkskort | VMXNET3 (om konfigurationsservern är en VMware-VM)

## <a name="required-software"></a>Programvara som krävs

**Komponent** | **Krav** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) ska installeras om konfigurationsservern körs på en VMware-VM.
MYSQL | MySQL ska installeras. Du kan installera manuellt eller Site Recovery kan du installera den.

## <a name="sizing-and-capacity-requirements"></a>Storlek och kapacitetskrav

I följande tabell sammanfattas kapacitetskrav för konfigurationsservern. Om du replikerar flera virtuella VMware-datorer, bör du granska den [överväganden vid kapacitetsplanering](site-recovery-plan-capacity-vmware.md), och kör den [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) verktyg för VMWare replication.read 

**Komponent** | **Krav** 
--- | ---

| **CPU** | **Minne** | **Cachedisk** | **Dataändringshastigheten** | **Replikerade datorer** |
| --- | --- | --- | --- | --- |
| 8 virtuella processorer<br/><br/> 2 platser * 4 kärnor \@ 2,5 GHz | 16 GB | 300 GB | 500 GB eller mindre | Les än 100 datorer |
| 12 virtuella processorer<br/><br/> 2 socks * 6 kärnor \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100-150 datorer |
| 16 vcpu: er<br/><br/> 2 socks * 8 kärnor \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 – 200 datorer | 



## <a name="next-steps"></a>Nästa steg
Konfigurera haveriberedskap för [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.
