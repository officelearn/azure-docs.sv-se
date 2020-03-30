---
title: Felsöka fel på VMware vCenter-identifieringsfel i Azure Site Recovery
description: I den hÃ¤r artikeln beskrivs felsÃ¶kning av VMware vCenter-identifieringsfel i Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74091238"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Felsöka identifieringsfel för vCenter Server

Den här artikeln hjälper dig att felsöka problem som uppstår på grund av VMware vCenter-identifieringsfel.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Icke-numeriska värden i egenskapen maxSnapShots

I versioner före 9.20 kopplas vCenter från när det hämtar ett `snapshot.maxSnapShots` icke-numeriskt värde för egenskapsegenskapen på en virtuell dator.

Det här problemet identifieras med fel-ID 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Så här löser du problemet:

- Identifiera den virtuella datorn och ange värdet till ett numeriskt värde (VM Edit-inställningar i vCenter).

Eller

- Uppgradera konfigurationsservern till version 9.20 eller senare.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Proxykonfigurationsproblem för vCenter-anslutning

vCenter Discovery respekterar systemstandardproxyinställningarna som konfigurerats av systemanvändaren. DRA-tjänsten respekterar proxyinställningarna som tillhandahålls av användaren under installationen av konfigurationsservern med hjälp av den enhetliga installationsinstallationsprogrammet eller OVA-mallen. 

I allmänhet används fullmakten för att kommunicera med offentliga nätverk. som att kommunicera med Azure. Om proxyn är konfigurerad och vCenter är i en lokal miljö kan den inte kommunicera med DRA.

Följande situationer uppstår när det här problemet påträffas:

- VCenter-servern \<vCenter> kan inte nås på grund av felet: Fjärrservern returnerade ett fel: (503) Server är inte tillgänglig
- VCenter-servern \<vCenter> kan inte nås på grund av felet: Fjärrservern returnerade ett fel: Det gick inte att ansluta till fjärrservern.
- Det gick inte att ansluta till vCenter/ESXi-servern.

Så här löser du problemet:

Ladda ner [PsExec-verktyget](https://aka.ms/PsExec). 

Använd PsExec-verktyget för att komma åt systemanvändarkontexten och avgöra om proxyadressen är konfigurerad. Du kan sedan lägga till vCenter i förbikopplingslistan med hjälp av följande procedurer.

För konfiguration av identifieringsproxy:

1. Öppna IE i systemanvändarsammanhang med psexec-verktyget.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Ändra proxyinställningarna i Internet Explorer för att kringgå vCenter IP-adressen.
3. Starta om tjänsten tmanssvc.

För DRA-proxykonfiguration:

1. Öppna en kommandotolk och öppna mappen Microsoft Azure Site Recovery Provider.
 
    **cd C:\Program-filer\Microsoft Azure Site Recovery Provider**

3. Kör följande kommando i kommandotolken.
   
   **DRKONFIGURATOR. EXE /configure /AddBypassUrls [IP-adress/FQDN för vCenter Server som angavs vid tidpunkten för lägg till vCenter]**

4. Starta om DRA-providertjänsten.

## <a name="next-steps"></a>Nästa steg

[Hantera konfigurationsservern för VMware VM-haveriberedskap](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
