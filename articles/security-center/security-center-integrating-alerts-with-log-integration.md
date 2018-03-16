---
title: Integrera Azure Security Center-aviseringar med Azure logganalys-integration | Microsoft Docs
description: "Den här artikeln hjälper dig att komma igång med Security Center-aviseringar integrera med Azure logganalys-integration."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: 229f83d4d74837e506c30a0fd8c704da2b7dc12a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration"></a>Integrera Azure Security Center-aviseringar med Azure logganalys-integration
Många säkerhetsåtgärder och incidenter team förlitar sig på en lösning för Security Information and Event Management (SIEM) som startpunkt för triaging och undersöker säkerhetsaviseringar. Du kan integrera Azure Security Center-aviseringar med Azure Log-integrering med SIEM-lösning.

Azure logganalys-integration stöder för närvarande HP ArcSight Splunk och IBM QRadar.

## <a name="what-logs-can-i-integrate"></a>Vilka loggar kan integrera?
Azure ger utförlig loggning för varje tjänst. Dessa loggar är kategoriserade som:

* **Kontroll och hantering loggar** som ger inblick i Azure Resource Manager skapa-, UPDATE- och DELETE-åtgärder. Dessa plan kontrollhändelserna exponeras i Azure aktivitetsloggar
* **Plan för dataloggar** som ger inblick i de händelser som utlöses när du använder en Azure-resurs. Ett exempel är Windows-händelseloggen, där du kan hämta säkerhetsinformation för händelse från säkerhetskanal i Loggboken. Data plan händelser (som genereras av en virtuell dator eller en Azure-tjänst) är anslutna med Azure diagnostikloggar.

Azure logganalys-integration stöder för närvarande integreringen av:

* Azure VM-loggar
* Azure-granskningsloggar
* Azure Security Center-aviseringar

## <a name="install-azure-log-integration"></a>Installera Azure logganalys-integrering
Hämta [Azure logganalys integration](https://www.microsoft.com/download/details.aspx?id=53324).

Tjänsten Azure logganalys integration samlar in telemetridata från datorn som är installerad.  I insamlade telemetridata är:

* Undantag som uppstår vid körning av Azure logganalys-integrering
* Mått om antalet frågor och händelser som har bearbetats
* Statistik om vilka Azlog.exe kommandoradsalternativ som används

> [!NOTE]
> Du kan stänga av insamling av telemetridata genom att avmarkera det här alternativet.
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrera Azure-granskningsloggarna och Security Center-aviseringar
1. Öppna Kommandotolken och **cd** till **c:\Program Files\Microsoft Azure Log integrering**.
2. Kör den **azlog createazureid** kommando för att skapa en [Azure Active Directory Service Principal](../active-directory/active-directory-application-objects.md) i Azure Active Directory (AD)-klienter som är värdar för Azure-prenumerationer.

    Du ombeds ange inloggningsinformation Azure.

   > [!NOTE]
   > Du måste vara Prenumerationens ägare eller en Medadministratör för prenumerationen.
   >
   >

    Autentisering till Azure sker via Azure AD.  Skapa ett huvudnamn för tjänsten för Azure logganalys integrering skapar Azure AD-identitet som har behörighet att läsa från Azure-prenumerationer.
3. Kör den **azlog auktorisera <SubscriptionID>**  kommando för att tilldela Reader åtkomst på prenumerationen till tjänstens huvudnamn skapade i steg 2. Om du inte anger en **SubscriptionID**, och sedan tjänstens huvudnamn har tilldelats rollen Läsare alla prenumerationer som du har åtkomst till.

   > [!NOTE]
   > Varningar kan visas om du kör den **auktorisera** kommandot omedelbart efter den **createazureid** kommando. Det finns vissa fördröjning mellan när Azure AD-kontot skapas och när kontot är tillgängliga för användning. Om du väntar cirka 10 sekunder efter att ha kört den **createazureid** kommando för att köra den **auktorisera** kommandot sedan dessa varningar inte ska visas.
   >
   >
4. Kontrollera följande mappar för att bekräfta att granskning JSON loggfilerna finns det:

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Kontrollera följande mappar för att bekräfta att Security Center-aviseringar finns i dem:

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. Konfigurera SIEM filen vidarebefordrare anslutningen till rätt mapp. Proceduren varierar beroende på SIEM som du använder.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure aktivitetsloggar och egenskapsdefinitioner finns:

* [Granska driften med Resource Manager](../azure-resource-manager/resource-group-audit.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Azure security nyheter och information.
