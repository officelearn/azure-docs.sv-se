---
title: Konfigurations Server krav för VMware haveri beredskap till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver support och krav vid distribution av konfigurations servern för VMware haveri beredskap till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 86fa817128dc89eb97bee18f4f8a6de1f650c265
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814298"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Konfigurations Server krav för VMware haveri beredskap till Azure

Du distribuerar en lokal konfigurations server när du använder [Azure Site Recovery](site-recovery-overview.md) för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure.

- Konfigurations servern samordnar kommunikationen mellan lokala VMware och Azure. Den hanterar också datareplikering.
- [Läs mer](vmware-azure-architecture.md) om konfigurations serverns komponenter och processer.

## <a name="configuration-server-deployment"></a>Distribution av konfigurations Server

För haveri beredskap för virtuella VMware-datorer till Azure distribuerar du konfigurations servern som en virtuell VMware-dator.

- Site Recovery innehåller en tjänstmall som du laddar ned från Azure Portal och importera till vCenter Server för att konfigurera den virtuella konfigurations servern.
- När du distribuerar konfigurations servern med hjälp av mallen för ägg, uppfyller den virtuella datorn automatiskt kraven i den här artikeln.
- Vi rekommenderar starkt att du konfigurerar konfigurations servern med hjälp av en ägg-mall. Men om du konfigurerar haveri beredskap för virtuella VMware-datorer och inte kan använda embryo-mallen kan du distribuera konfigurations servern med hjälp av [dessa instruktioner](physical-azure-set-up-source.md).
- Om du distribuerar konfigurations servern för haveri beredskap för lokala fysiska datorer till Azure följer du anvisningarna i [den här artikeln](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Maskinvarukrav

**Komponent** | **Krav** 
--- | ---
Processorkärnor | 8 
RAM | 16 GB
Antal diskar | 3, inklusive OS-disken, cache-disk för processerver och lagrings enhet för återställning efter fel 
Ledigt disk utrymme (bearbetnings serverns cacheminne) | 600 GB
Ledigt disk utrymme (kvarhållning av disk) | 600 GB

## <a name="software-requirements"></a>Programvarukrav

**Komponent** | **Krav** 
--- | ---
Operativsystem | Windows Server 2012 R2 <br> Windows Server 2016
Nationella inställningar för operativsystem | Engelska (en-us)
Windows Server-roller | Aktivera inte följande roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Grup principer | Aktivera inte dessa grup principer: <br> -Förhindra åtkomst till kommando tolken. <br> -Förhindra åtkomst till verktyg för redigering av registret. <br> – Förtroende logik för bifogade filer. <br> – Aktivera skript körning. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Ingen befintlig standard webbplats <br> – Ingen befintlig webbplats/program lyssnar på port 443 <br>-Aktivera [Anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -inställning 

## <a name="network-requirements"></a>Nätverkskrav

**Komponent** | **Krav** 
--- | --- 
IP-adresstyp | Statisk 
Internet-åtkomst | Servern behöver åtkomst till dessa URL: er (direkt eller via proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> -https:\//Management.Azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF måste också ha åtkomst till följande URL: er: <br> -https:\//login.microsoftonline.com <br> -https:\//Secure.aadcdn.microsoftonline-p.com <br> -https:\//login.live.com  <br> -https:\//auth.gfx.MS <br> -https:\//Graph.Windows.net <br> -https:\//login.Windows.net <br> -https:\//www.live.com <br> -https:\//www.Microsoft.com <br> -https:\//dev.mysql.com/get/downloads/MySQLInstaller/MySQL-Installer-community-5.7.20.0.msi 
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring) 
Typ av nätverkskort | VMXNET3 (om konfigurations servern är en virtuell VMware-dator)

## <a name="required-software"></a>Nödvändig program vara

**Komponent** | **Krav** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI version 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) bör installeras om konfigurations servern körs på en virtuell VMware-dator.
MYSQL | MySQL ska installeras. Du kan installera manuellt, eller så kan Site Recovery installera det.

## <a name="sizing-and-capacity-requirements"></a>Storleks-och kapacitets krav

I följande tabell sammanfattas kapacitets kraven för konfigurations servern. Om du replikerar flera virtuella VMware-datorer bör du gå igenom [överväganden för kapacitets planering](site-recovery-plan-capacity-vmware.md)och köra [Distributionshanteraren för Azure Site Recovery](site-recovery-deployment-planner.md) -verktyget för VMware-replikering. Läs 

**Komponent** | **Krav** 
--- | ---

| **CPU** | **Minnesoptimerade** | **Cachelagra disk** | **Data ändrings takt** | **Replikerade datorer** |
| --- | --- | --- | --- | --- |
| 8 virtuella processorer<br/><br/> 2 Sockets * 4 kärnor \@ 2,5 GHz | 16 GB | 300 GB | 500 GB eller mindre | Les än 100 datorer |
| 12 virtuella processorer<br/><br/> 2 SOCKS * 6 kärnor \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 till 150-datorer |
| 16 virtuella processorer<br/><br/> 2 SOCKS * 8 kärnor \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 datorer | 



## <a name="next-steps"></a>Nästa steg
Konfigurera katastrof återställning av [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.
