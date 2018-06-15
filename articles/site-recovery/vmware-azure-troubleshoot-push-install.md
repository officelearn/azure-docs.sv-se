---
title: Azure Site Recovery för felsökning från VMware Azure | Microsoft Docs
description: Felsök fel när du replikerar virtuella Azure-datorer.
services: site-recovery
author: anoopkv
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: bdd5d87670ddb26d2c4474a319fe19bef1db62f5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
ms.locfileid: "29812896"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Felsökning av problem med installation av Mobilitetstjänsten push

Den här artikeln beskriver hur du felsöker vanliga fel som kan uppstår när du försöker installera Azure Site Recovery Mobility-tjänsten på källservern för att aktivera skydd.

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
95105 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0856**. <br> Antingen **File and Printer Sharing** är inte tillåten på käll-datorer eller att det är nätverksanslutningsproblem mellan processervern och källdatorn.| **Fil- och skrivardelning** är inte aktiverad. | Tillåt **File and Printer Sharing** på källdatorn i Windows-brandväggen. På källdatorn, under **Windows-brandväggen** > **tillåta en app eller funktion via brandväggen**väljer **fil- och skrivardelning för alla profiler**. </br> Kontrollera följande krav för att kunna slutföra push-installation.<br> Läs mer om [felsökning av WMI utfärdar](#troubleshoot-wmi-issues).


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Fel 95107 - skydd kan inte vara aktiverad (EP0858)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95107 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0858**. <br> De angivna autentiseringsuppgifterna för att installera Mobilitetstjänsten är felaktiga eller användarkontot har inte tillräcklig behörighet. | Användarautentiseringsuppgifterna som anges för att installera Mobilitetstjänsten på källdatorn är felaktiga. | Kontrollera att användarens autentiseringsuppgifter för källdatorn på konfigurationsservern är korrekta. <br> Om du vill lägga till eller redigera användarens autentiseringsuppgifter, gå till konfigurationsservern och välj **Cspsconfigtool** > **hantera kontot**. </br> Kontrollera följande [krav](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) ska kunna slutföras push-installation.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Fel 95117 - skydd kan inte vara aktiverad (EP0865)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95117 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0865**. <br> Antingen körs inte källdatorn eller också är problem med nätverksanslutningen mellan processervern och källdatorn. | Problem med nätverksanslutningen mellan processervern och källservern. | Kontrollera nätverksanslutningen mellan processervern och källservern. </br> Kontrollera följande [krav](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) ska kunna slutföras push-installation.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Fel 95103 - skydd kan inte vara aktiverad (EP0854)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95103 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0854**. <br> Windows Management Instrumentation (WMI) är inte tillåten på källdatorn eller det finns problem med nätverksanslutningen mellan processervern och källdatorn.| WMI är blockerad i Windows-brandväggen. | Tillåt WMI i Windows-brandväggen. Under **Windows-brandväggen** > **tillåta en app eller funktion via brandväggen**väljer **WMI för alla profiler**. </br> Kontrollera följande [krav](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) ska kunna slutföras push-installation.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Fel 95213 - skydd kan inte vara aktiverad (EP0874)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95213 </br>**Meddelande:** Installation av Mobilitetstjänsten till källdatorn % SourceIP; misslyckades med felkoden **EP0874**. <br> | Versionen av operativsystemet på källdatorn stöds inte. <br>| Se till att källdatorn operativsystem. Läs den [supportmatrisen](https://aka.ms/asr-os-support). </br> Kontrollera följande [krav](https://aka.ms/pushinstallerror) ska kunna slutföras push-installation.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Fel 95108 - skydd kan inte vara aktiverad (EP0859)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95108 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn misslyckades med felkoden **EP0859**. <br>| De angivna autentiseringsuppgifterna för att installera Mobilitetstjänsten är felaktiga eller användarkontot har inte tillräcklig behörighet. <br>| Se till att de angivna autentiseringsuppgifterna är den **rot** kontots autentiseringsuppgifter. Om du vill lägga till eller redigera användarens autentiseringsuppgifter, gå till konfigurationsservern och välj den **Cspsconfigtool** genvägsikon på skrivbordet. Välj **hantera kontot** lägga till eller redigera autentiseringsuppgifter.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Fel 95265 - skydd kan inte vara aktiverad (EP0902)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95265 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn lyckades men källdatorn kräver en omstart för vissa system ändringarna ska börja gälla. <br>| En äldre version av Mobilitetstjänsten har redan installerats på servern.| Replikeringen av den virtuella datorn fortsätter sömlöst.<br> Starta om servern under din nästa underhållsfönster att hämta fördelarna med de nya förbättringarna i Mobilitetstjänsten.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Fel 95224 - skydd kan inte vara aktiverad (EP0883)

**Felkod** | **Möjliga orsaker** | **Fel-specifika rekommendationer**
--- | --- | ---
95224 </br>**Meddelande:** Push-installation av Mobilitetstjänsten till källdatorn % SourceIP; misslyckades med felkoden **EP0883**. En systemomstart från en tidigare installation eller uppdatering väntar.| Systemet har startats om när du avinstallerar en äldre eller inkompatibel version av Mobilitetstjänsten.| Se till att det finns ingen version av Mobilitetstjänsten på servern. <br> Starta om servern och kör skyddsaktiveringsjobbet.|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Resurs för att felsöka problem med push-installation

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Felsöka fil och skriva ut dela problem
* [Aktivera eller inaktivera fildelning med Grupprincip](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [Aktivera fil- och skrivardelning via Windows-brandväggen](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>Felsökning av problem med WMI
* [Grundläggande WMI tester](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI-felsökning](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Felsökning av problem med WMI-skript och WMI-tjänster](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>Nästa steg

[Lär dig hur](vmware-azure-tutorial.md) konfigurera katastrofåterställning för virtuella VMware-datorer.