---
title: Azure Site Recovery felsökning från VMware till Azure | Microsoft Docs
description: Felsöka fel när du replikerar virtuella Azure-datorer.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.author: ramamill
ms.date: 07/06/2018
ms.openlocfilehash: 8d5db03eeebb659414ea1f554e5b34c938fd2795
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952917"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Felsöka installationsproblem med Mobilitetstjänsten push

Den här artikeln beskriver hur du felsöker vanliga fel som kan uppstår när du försöker installera Azure Site Recovery-Mobilitetstjänsten på källservern för att aktivera skydd.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Fel 78007 – den begärda åtgärden kunde inte slutföras
Det här felet kan misslyckas på grund av tjänsten av flera skäl. Välj motsvarande provider-fel till felsökningen.

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
95105 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0856**. <br> Antingen **File and Printer Sharing** är inte tillåten på käll-datorer eller att det är network problem med nätverksanslutningen mellan processervern och källdatorn.| **Fil- och skrivardelning** har inte aktiverats. | Tillåt **File and Printer Sharing** på källdatorn i Windows-brandväggen. På källdatorn, under **Windows-brandväggen** > **Tillåt en app eller funktion i brandväggen**väljer **fil- och skrivardelning för alla profiler**. </br> Dessutom kan kontrollera följande krav för att slutföra har push-installationen.<br> Läs mer om [felsökning av WMI utfärdar](#troubleshoot-wmi-issues).


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Fel 95107 - skydd kan inte vara aktiverad (EP0858)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95107 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0858**. <br> Antingen tillhandahållna för att installera Mobilitetstjänsten autentiseringsuppgifterna är felaktiga eller användarkontot har inte tillräcklig behörighet. | Användarens autentiseringsuppgifter tillhandahålls för att installera Mobilitetstjänsten på källdatorn är felaktiga. | Se till att användarautentiseringsuppgifterna för källdatorn på konfigurationsservern är korrekta. <br> Om du vill lägga till eller redigera autentiseringsuppgifter för användare, gå till konfigurationsservern och välj **Cspsconfigtool** > **Hantera konto**. </br> Kontrollera också följande [krav](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) ska kunna slutföras push-installationen.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Fel 95117 - skydd kan inte vara aktiverad (EP0865)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95117 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0865**. <br> Antingen körs inte källdatorn eller också är problem med nätverksanslutningen mellan processervern och källdatorn. | Problem med nätverksanslutningen mellan processervern och källservern. | Kontrollera anslutningen mellan processervern och källservern. </br> Kontrollera också följande [krav](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) ska kunna slutföras push-installationen.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Fel 95103 - skydd kan inte vara aktiverad (EP0854)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95103 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0854**. <br> Antingen Windows Management Instrumentation (WMI) är inte tillåten på källdatorn eller också är problem med nätverksanslutningen mellan processervern och källdatorn.| WMI är blockerad i Windows-brandväggen. | Tillåt WMI i Windows-brandväggen. Under **Windows-brandväggen** > **Tillåt en app eller funktion i brandväggen**väljer **WMI för alla profiler**. </br> Kontrollera också följande [krav](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) ska kunna slutföras push-installationen.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Fel 95213 - skydd kan inte vara aktiverad (EP0874)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95213 </br>**Meddelande:** Installation av Mobilitetstjänsten på källdatorn % SourceIP; misslyckades med felkoden **EP0874**. <br> | Operativsystemets version på källdatorn stöds inte. <br>| Se till att källdatorn OS-versionen stöds. Läs den [stödmatris](https://aka.ms/asr-os-support). </br> Kontrollera också följande [krav](https://aka.ms/pushinstallerror) ska kunna slutföras push-installationen.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Fel 95108 - skydd kan inte vara aktiverad (EP0859)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95108 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0859**. <br>| Antingen tillhandahållna för att installera Mobilitetstjänsten autentiseringsuppgifterna är felaktiga eller användarkontot har inte tillräcklig behörighet. <br>| Se till att de angivna autentiseringsuppgifterna är den **rot** kontots autentiseringsuppgifter. Om du vill lägga till eller redigera autentiseringsuppgifter för användare, gå till konfigurationsservern och välj den **Cspsconfigtool** genvägsikon på skrivbordet. Välj **Hantera konto** att lägga till eller redigera autentiseringsuppgifter.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Fel 95265 - skydd kan inte vara aktiverad (EP0902)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95265 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn lyckades men källdatorn måste startas om för att ändringarna ska börja gälla. <br>| En äldre version av Mobilitetstjänsten har redan installerats på servern.| Replikeringen av den virtuella datorn fortsätter sömlöst.<br> Starta om servern under nästa underhållsperiod att ta del av de nya förbättrade funktionerna i Mobilitetstjänsten.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Fel 95224 - skydd kan inte vara aktiverad (EP0883)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95224 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn % SourceIP; misslyckades med felkoden **EP0883**. En systemomstart från en tidigare installation eller uppdatering väntar.| Systemet har inte startats om när du avinstallerar en äldre eller inkompatibel version av Mobilitetstjänsten.| Se till att det finns ingen version av Mobilitetstjänsten på servern. <br> Starta om servern och kör aktivering av skydd.|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Resursen för att felsöka problem med push-installation

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Felsöka fil och skriva ut delningsapplikationen problem
* [Aktivera eller inaktivera fildelning med en Grupprincip](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [Aktivera fil- och skrivardelning via Windows-brandväggen](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>Felsökning av problem med WMI
* [Grundläggande WMI-testning](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Felsökning av WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Felsökning av problem med WMI-skript och WMI-tjänster](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](vmware-azure-tutorial.md) att konfigurera haveriberedskap för virtuella VMware-datorer.