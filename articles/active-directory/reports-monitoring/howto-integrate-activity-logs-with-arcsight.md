---
title: Hur du integrerar Azure Active Directory-loggar med ArcSight med Azure Monitor (förhandsversion) | Microsoft Docs
description: Lär dig att integrera Azure Active Directory-loggar med ArcSight med Azure Monitor (förhandsversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/03/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1e9f9fe5c04e5293c91765795dcbfb9b0800b809
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168608"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor-preview"></a>Integrera Azure Active Directory-loggar med ArcSight med Azure Monitor (förhandsversion)

[Micro fokus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) är en säkerhet och händelsehantering (SIEM) hanteringslösning som hjälper dig att identifiera och svara på säkerhetshot i din plattform. Du kan nu vidarebefordra Azure Active Directory (Azure AD)-loggar till ArcSight med Azure Monitor med ArcSight connector för Azure AD. Den här funktionen kan du övervaka din klient för säkerhetsintrång med ArcSight.  

I den här artikeln lär du dig hur du dirigerar Azure AD-loggar till ArcSight med Azure Monitor. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här funktionen behöver du:
* En Azure-händelsehubb som innehåller Azure AD activity loggar. Lär dig hur du [strömma din aktivitetsloggar till en händelsehubb](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* En konfigurerade instans av ArcSight Syslog-NG-Daemon-SmartConnector (SmartConnector) eller ArcSight Load Balancer. Om händelser skickas till ArcSight belastningsutjämnare kan skickas de därför till SmartConnector av belastningsutjämnaren.

Ladda ned och öppna den [konfigurationsguide för ArcSight SmartConnector för Azure Monitor Event Hub](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf). Den här guiden innehåller de steg som du måste installera och konfigurera ArcSight SmartConnector för Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrera Azure AD-loggar med ArcSight

1. Först måste slutföra stegen i den **krav** i konfigurationsguiden. Det här avsnittet innehåller följande steg:
    * Ange användarbehörigheter i Azure för att se till att det finns en användare med den **ägare** roll att distribuera och konfigurera anslutningen.
    * Öppna portar på servern med Syslog NG-Daemon SmartConnector så att den kan nås från Azure. 
    * Distributionen kör ett Windows PowerShell-skript så måste du aktivera PowerShell för att köra skript på datorn där du vill distribuera anslutningstjänsten.

2. Följ stegen i den **distribuera anslutningstjänsten** i konfigurationsguiden av att distribuera anslutningstjänsten. Det här avsnittet vägleder dig genom hur du ladda ned och extrahera anslutningen, konfigurera egenskaper för program och kör skriptet för distribution från den extrahera mappen. 

3. Följ stegen i den **Kontrollera distributionen i Azure** att kontrollera att anslutningen har konfigurerats och fungerar på rätt sätt. Kontrollera följande:
    * Nödvändiga Azure functions skapas i din Azure-prenumeration.
    * Azure AD-loggarna strömmas till rätt destination. 
    * Programinställningar från distributionen finns kvar i programinställningarna i Azure-Funktionsappar. 
    * En ny resursgrupp för ArcSight skapas i Azure, med ett Azure AD-program för ArcSight koppling och storage-konton som innehåller de mappade filerna i CEF-format.

4. Slutligen Slutför efter distributionen stegen i den **efter distributionskonfigurationer** för i konfigurationsguiden. Det här avsnittet beskrivs hur du utför ytterligare konfiguration om du har en Apptjänstplan för att förhindra att funktionsappar ska inaktiv efter en timeout-period, konfigurera strömning av diagnostiska loggar från event hub och uppdatera SysLog-NG-Daemon SmartConnector keystore-certifikat för att associera det med det nyligen skapade lagringskontot.

5. I konfigurationsguiden förklarar också hur du anpassar Kopplingsegenskaperna i Azure och hur du uppgraderar och avinstallerar du kopplingen. Det finns också ett avsnitt på prestandaförbättringar, inklusive uppgradera till en [Azure förbrukningsplan](https://azure.microsoft.com/pricing/details/functions) och konfigurera en belastningsutjämnare ArcSight om händelsebelastningen är större än vad en enda Syslog NG-Daemon SmartConnector kan hantera.

## <a name="next-steps"></a>Nästa steg

* [Konfigurationsguide för ArcSight SmartConnector för Azure Monitor Event Hub](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)
