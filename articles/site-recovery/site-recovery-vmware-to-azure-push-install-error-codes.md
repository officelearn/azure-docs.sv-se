---
title: "Azure Site Recovery för felsökning från VMware Azure | Microsoft Docs"
description: "Felsöka vid replikering av virtuella Azure-datorer"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/28/2017
ms.author: asgang
ms.openlocfilehash: b7b03442ba815c86e5defa1018b66f56c0b379df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Felsökning av problem med installation av Mobilitetstjänsten push

Den här artikeln beskrivs vanliga problem som kan uppstår när du försöker installera Azure Site Recovery Mobility-tjänsten på källservern för att aktivera skydd.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Fel 78007 - den begärda åtgärden kunde inte slutföras
Det här felet kan orsakas av tjänsten av flera skäl. Välj den motsvarande provider-fel fortsätta felsökningen.

* [Fel 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [Fel 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [Fel 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [Fel 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [Fel 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [Fel 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [Fel 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [Fel 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>Fel 95105 - skydd kan inte vara aktiverad (EP0856)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95105 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0856**. <br> Antingen **File and Printer Sharing** är inte tillåten på käll-datorer eller att det är nätverksanslutningsproblem mellan processervern och källdatorn.| **Fil- och skrivardelning** är inte aktiverad. | Tillåt **File and Printer Sharing** på källdatorn i Windows-brandväggen. På källdatorn, under **Windows-brandväggen** > **tillåta en app eller funktion via brandväggen**väljer **fil- och skrivardelning för alla profiler**. </br> Kontrollera följande krav för att kunna slutföra push-installation.<br> Läs mer om [felsökning av WMI-isssues](#troubleshoot-wmi-issues)


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Fel 95107 - skydd kan inte vara aktiverad (EP0858)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95107 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0858**. <br> De angivna autentiseringsuppgifterna för att installera Mobilitetstjänsten är felaktiga eller användarkontot har inte tillräcklig behörighet. | Användarautentiseringsuppgifterna som anges för att installera Mobilitetstjänsten på källdatorn är felaktiga. | Kontrollera användarautentiseringsuppgifterna som anges för källdatorn på konfigurationsservern är korrekta. <br> Om du vill lägga till eller redigera användarens autentiseringsuppgifter, gå till konfigurationsservern och välj **Cspsconfigtool** > **hantera kontot**. </br> Kontrollera följande [krav](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) ska kunna slutföras push-installation.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Fel 95117 - skydd kan inte vara aktiverad (EP0865)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95117 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0865**. <br> Antingen körs inte källdatorn eller också är problem med nätverksanslutningen mellan processervern och källdatorn. | Problem med nätverksanslutningen mellan processervern och källservern. | Kontrollera nätverksanslutningen mellan processervern och källservern. </br> Kontrollera följande [krav](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) ska kunna slutföras push-installation.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Fel 95103 - skydd kan inte vara aktiverad (EP0854)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95103 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0854**. <br> Windows Management Instrumentation (WMI) är inte tillåten på källdatorn eller det finns problem med nätverksanslutningen mellan processervern och källdatorn.| WMI är blockerad i Windows-brandväggen. | Tillåt WMI i Windows-brandväggen. Under **Windows-brandväggen** > **tillåta en app eller funktion via brandväggen**väljer **WMI för alla profiler**. </br> Kontrollera följande [krav](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) ska kunna slutföras push-installation.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Fel 95213 - skydd kan inte vara aktiverad (EP0874)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95213 </br>**Meddelande:** Installation av mobilitetstjänsten misslyckades på källdatorn % SourceIP; misslyckades med felkoden **EP0874**. <br> | Version av operativsystemet på källdatorn stöds inte. <br>| Se till att källdatorn operativsystem. Läs den [supportmatrisen](https://aka.ms/asr-os-support). </br> Kontrollera följande [krav](https://aka.ms/pushinstallerror) ska kunna slutföras push-installation.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Fel 95108 - skydd kan inte vara aktiverad (EP0859)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95108 </br>**Meddelande:** Push-installation av mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0859**. <br>| Antingen autentiseringsuppgifterna för att installera mobilitetstjänsten felaktiga, eller användarkontot har inte behörighet <br>| Se till att de angivna autentiseringsuppgifterna är den **rot** kontots autentiseringsuppgifter. Att [Lägg till/redigera användarautentiseringsuppgifter](site-recovery-vmware-to-azure-manage-configuration-server.md#modify-user-accounts-and-passwords)går du till konfigurationsservern och klicka på ”Cspsconfigtool” genvägsikon på skrivbordet. Klicka på ”Hantera konto” Lägg till/redigera autentiseringsuppgifter.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Fel 95265 - skydd kan inte vara aktiverad (EP0902)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95265 </br>**Meddelande:** Push-installation av mobilitetstjänsten till källdatorn lyckades men källdatorn kräver en omstart för vissa system ändringarna ska börja gälla. <br>| En äldre version av mobilitetstjänsten har redan installerats på servern.| Replikeringen av den virtuella datorn fortsätter sömlöst.<br> Starta om servern under din nästa underhållsfönster att hämta fördelarna med de nya förbättringarna i mobilitetstjänsten.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Fel 95224 - skydd kan inte vara aktiverad (EP0883)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95224 </br>**Meddelande:** Push-installation av mobilitetstjänsten till källdatorn % SourceIP; misslyckades med felkoden EP0883. Ett system från en tidigare installation eller uppdatering väntar.| Systemet har inte startats om avinstallerar en äldre/inkompatibel version av mobilitetstjänsten.| Se till att det finns ingen version av mobilitetstjänsten på servern. <br> Starta om servern och kör skyddsaktiveringsjobbet|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Resurs för att felsöka problem med push-installation

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Felsökning av problem med fil- och skrivardelning
*  [Aktivera eller inaktivera fildelning med Grupprincip](https://technet.microsoft.com/en-us/library/cc754359(v=ws.10).aspx)
* [Hur du aktiverar fil och skrivardelning via Windows-brandväggen](https://technet.microsoft.com/en-us/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>Felsökning av problem med WMI
* [Testa grundläggande WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI-felsökning](https://msdn.microsoft.com/en-us/library/aa394603(v=vs.85).aspx)
* [Felsökning av problem med WMI-skript och WMI-tjänster](https://technet.microsoft.com/en-us/library/ff406382.aspx#H22)

## <a name="next-steps"></a>Nästa steg
- [Aktivera replikering för virtuella VMware-datorer](vmware-walkthrough-enable-replication.md)
