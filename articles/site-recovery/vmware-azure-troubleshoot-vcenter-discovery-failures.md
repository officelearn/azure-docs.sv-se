---
title: Felsöka växla tillbaka till den lokala under VMware VM-katastrofåterställning till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskrivs olika sätt att felsöka problem med återställning efter fel och återaktiveringen av skyddet under VMware VM-katastrofåterställning till Azure med Azure Site Recovery.
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: c598c5e238458c010500579c5371622b85e71de0
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540813"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Felsöka vCenter-identifieringsfel

Den här artikeln hjälper dig att felsöka problem som uppstår på grund av VMware vCenter-identifiering av fel.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Icke-numeriska värden i egenskapen maxSnapShots

I versioner innan 9.20 vCenter kopplar bort när du hämtar en icke-numeriska värdet för egenskapen `snapshot.maxSnapShots` egenskapen på en virtuell dator.

Det här problemet har identifierats av fel-ID 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Att lösa problemet:

- Identifiera den virtuella datorn och ange värdet till ett numeriskt värde (VM redigera inställningarna i vCenter).

Eller

- Uppgradera konfigurationsservern till version 9.20 eller senare.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Proxy konfigurationsproblem för vCenter-anslutning

vCenter identifiering utförs följer standard systemproxyinställningarna konfigurerats av användaren för System. DRA-tjänsten godkänner proxy-inställningar som anges av användaren under installationen av konfigurationsservern med hjälp av den enhetligt installationsprogram eller OVA-mallen. 

I allmänhet ska proxyservern användas för att kommunicera med offentliga nätverk; till exempel kommunicera med Azure. Om proxyservern har konfigurerats och vCenter är i en lokal miljö, inte det att kunna kommunicera med dra-versionen.

I följande situationer inträffa när det här problemet inträffar:

- VCenter-servern \<vCenter > kan inte nås på grund av felet: Fjärrservern returnerade ett fel: (503) servern är inte tillgänglig
- VCenter-servern \<vCenter > kan inte nås på grund av felet: Fjärrservern returnerade ett fel: Det går inte att ansluta till fjärrservern.
- Det går inte att ansluta till vCenter/ESXi-servern.

Att lösa problemet:

Ladda ned den [PsExec verktyget](https://aka.ms/PsExec). 

Använda PsExec för att komma åt användarkontext System och avgöra om Proxyadressen är konfigurerat. Du kan sedan lägga till vCenter till listan över kringgå med hjälp av följande procedurer.

Proxykonfiguration för identifiering:

1. Öppna Internet Explorer i systemkontexten som användare med hjälp av verktyget PsExec.
    
    PSExec -s -i ”%programfiles%\Internet Explorer\iexplore.exe”

2. Ändra proxyinställningarna i Internet Explorer för att kringgå IP-adress för vCenter.
3. Starta om tjänsten tmanssvc.

DRA proxykonfiguration:

1. Öppna en kommandotolk och öppna mappen Microsoft Azure Site Recovery-providern.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery-providern**

3. Kör följande kommando från Kommandotolken.
   
   **DRCONFIGURATOR. EXE / konfigurera /AddBypassUrls [IP-adressen/FQDN för vCenter Server som angavs vid tidpunkten för Lägg till vCenter]**

4. Starta om tjänsten DRA provider.

## <a name="next-steps"></a>Nästa steg

[Hantera konfigurationsservern för VMware-VM-katastrofåterställning](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
