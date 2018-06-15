---
title: Azure Log-integrering med security center | Microsoft Docs
description: Lär dig att hämta Azure Security center-aviseringar arbetar med Log-integration
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
ms.locfileid: "29138499"
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Hur du hämtar Security Center-aviseringar i Azure logganalys-integrering

Den här artikeln innehåller de steg som krävs för att aktivera tjänsten Azure Log-integrering och hämtar Säkerhetsvarning information som genererats av Azure Security Center. Du måste ha slutfört stegen i den [Kom igång](security-azure-log-integration-get-started.md) artikel innan du utför stegen i den här artikeln.

## <a name="detailed-steps"></a>Detaljerade steg

Följande steg skapar nödvändiga Azure Active Directory Service Principal och tilldela principen Service läsbehörighet till prenumerationen:
1. Öppna Kommandotolken och navigera till **c:\Program Files\Microsoft Azure Log-integrering**
2. Kör kommandot``azlog createazureid``

    Det här kommandot uppmanas du att din Azure-inloggning. Kommandot skapar sedan en [Azure Active Directory Service Principal](../active-directory/develop/active-directory-application-objects.md) i Azure AD-klienter som är värdar för Azure-prenumerationer som den inloggade användaren är administratör, en Medadministratör eller en ägare. Kommandot misslyckas om den inloggade användaren är endast gästanvändaren i Azure AD-klient. Autentisering till Azure sker via Azure Active Directory (AD). Skapa ett huvudnamn för tjänsten för Azlog integrering skapar Azure AD-identitet som kommer att få behörighet att läsa från Azure-prenumerationer.

3. Nästa ska du köra ett kommando som tilldelar reader åtkomst på prenumerationen till tjänstens huvudnamn som du nyss skapade. Om du inte anger en SubscriptionID försöker kommandot tilldela rollen service principal läsare alla prenumerationer som du har åtkomst. </br></br>
``azlog authorize <SubscriptionID>`` </br> till exempel </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    Varningar kan visas om du kör kommandot auktorisera omedelbart efter den ```createazureid``` kommando. Det finns vissa fördröjning mellan när Azure AD-kontot skapas och när kontot är tillgängliga för användning. Om du vänta ungefär 60 sekunder när du har kört den ```createazureid``` kommando för att köra kommandot auktorisera sedan dessa varningar inte ska visas.

4. Kontrollera följande mappar för att bekräfta att granskning JSON loggfilerna finns det:
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. Kontrollera följande mappar för att bekräfta att Security Center-aviseringar finns i dem:</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

Om du stöter på problem under installationen och konfigurationen, öppnar du en [supportbegäran](/azure-supportability/how-to-create-azure-support-request.md)väljer **loggen Integration** som tjänsten som du begär support.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Log-integrering finns i följande dokument:

* [Microsoft Azure Log-integrering för Azure loggar](https://www.microsoft.com/download/details.aspx?id=53324) – Download Center för ytterligare information, systemkrav, och installera instruktioner på Azure logganalys-integration.
* [Introduktion till Azure logganalys integration](security-azure-log-integration-overview.md) – det här dokumentet ger en introduktion till Azure logganalys integration, de viktigaste funktionerna och hur det fungerar.
* [Samarbeta konfigurationssteg](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – det här blogginlägget visar hur du konfigurerar Azure logganalys-integrering ska fungera med partnerlösningar Splunk, HP ArcSight och IBM QRadar.
* [Azure logganalys Integration vanliga frågor (FAQ)](security-azure-log-integration-faq.md) – det här avsnittet får du svar frågor om Azure logganalys-integration.
* [Nya funktioner för Azure-diagnostik och Azure-granskningsloggarna](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – det här blogginlägget ger en introduktion till Azure-granskningsloggarna och andra funktioner som hjälper dig få insikter om hur Azure-resurser.
