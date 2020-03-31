---
title: Integrera loggar med ArcSight med Azure Monitor | Microsoft-dokument
description: Lär dig hur du integrerar Azure Active Directory-loggar med ArcSight med Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05002c1b11ef31b61fb4036f09dc8edcdafca767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75608388"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrera Azure Active Directory-loggar med ArcSight med Azure Monitor

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) är en SIEM-lösning (Security Information and Event Management) som hjälper dig att upptäcka och svara på säkerhetshot i din plattform. Du kan nu dirigera Azure Active Directory (Azure AD) loggar till ArcSight med Hjälp av Azure Monitor med ArcSight-kopplingen för Azure AD. Med den här funktionen kan du övervaka din klient för säkerhetskompromiss med ArcSight.  

I den här artikeln får du lära dig hur du dirigerar Azure AD-loggar till ArcSight med Azure Monitor. 

## <a name="prerequisites"></a>Krav

Om du vill använda den här funktionen behöver du:
* En Azure-händelsenav som innehåller Azure AD-aktivitetsloggar. Läs om hur du [streamar dina aktivitetsloggar till en händelsehubb](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* En konfigurerad instans av ArcSight Syslog NG Daemon SmartConnector (SmartConnector) eller ArcSight Load Balancer. Om händelserna skickas till ArcSight Load Balancer skickas de därför till SmartConnector av belastningsutjämnaren.

Hämta och öppna [konfigurationsguiden för ArcSight SmartConnector för Azure Monitor Event Hub](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292). Den här guiden innehåller de steg du behöver för att installera och konfigurera ArcSight SmartConnector för Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrera Azure AD-loggar med ArcSight

1. Slutför först stegen i avsnittet Förutsättningar i **konfigurationsguiden.** Det här avsnittet innehåller följande steg:
    * Ange användarbehörigheter i Azure för att säkerställa att det finns en användare med **ägarrollen** för att distribuera och konfigurera kopplingen.
    * Öppna portar på servern med Syslog NG Daemon SmartConnector, så att den är tillgänglig från Azure. 
    * Distributionen kör ett Windows PowerShell-skript, så du måste aktivera PowerShell för att köra skript på den dator där du vill distribuera kopplingen.

2. Följ stegen i avsnittet **Distribuera anslutningsdelen** i konfigurationsguiden för att distribuera kopplingen. I det här avsnittet får du lära dig hur du hämtar och extraherar kopplingen, konfigurerar programegenskaper och kör distributionsskriptet från den extraherade mappen. 

3. Följ stegen i **verifiera distributionen i Azure** för att kontrollera att anslutningen är konfigurerad och fungerar korrekt. Kontrollera följande:
    * De nödvändiga Azure-funktionerna skapas i din Azure-prenumeration.
    * Azure AD-loggarna strömmas till rätt mål. 
    * Programinställningarna från distributionen sparas i programinställningarna i Azure Function Apps. 
    * En ny resursgrupp för ArcSight skapas i Azure, med ett Azure AD-program för ArcSight-anslutnings- och lagringskonton som innehåller de mappade filerna i CEF-format.

4. Slutför slutligen stegen efter distributionen i **konfigurationerna efter distributionen** i konfigurationsguiden. I det här avsnittet beskrivs hur du utför ytterligare konfiguration om du använder en App Service-plan för att förhindra att funktionsapparna går inaktiva efter en timeout-period, konfigurerar direktuppspelning av diagnostikloggar från händelsehubben och uppdaterar SysLog NG Daemon SmartConnector keystore-certifikat för att associera det med det nyskapade lagringskontot.

5. Konfigurationsguiden förklarar också hur du anpassar anslutningsegenskaperna i Azure och hur du uppgraderar och avinstallerar kopplingen. Det finns också ett avsnitt om prestandaförbättringar, inklusive uppgradering till en [Azure-förbrukningsplan](https://azure.microsoft.com/pricing/details/functions) och konfigurera en ArcSight Load Balancer om händelsebelastningen är större än vad en enda Syslog NG Daemon SmartConnector kan hantera.

## <a name="next-steps"></a>Nästa steg

[Konfigurationsguide för ArcSight SmartConnector för Azure Monitor Event Hub](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)
