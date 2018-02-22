---
title: Azure Log-integrering med Azure Active Directory-granskningsloggar | Microsoft Docs
description: "Lär dig att installera tjänsten Azure Log-integrering och integrera loggar från Azure-granskningsloggar"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 02/16/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 0f45f43a0296a7d90a68b0526f805ea50a1ce6c6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrera Azure Active Directory-granskningsloggar

Granskningshändelser för Azure Active Directory (AD Azure) hjälper dig att identifiera Privilegierade åtgärder som har inträffat i Azure Active Directory. Du kan se vilka typer av händelser som du kan följa genom att granska [Azure Active Directory-granskningsrapporthändelser](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).


> [!NOTE]
> Innan du försöker stegen i den här artikeln, bör du granska den [Kom igång](security-azure-log-integration-get-started.md) artikel och slutför stegen.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Stegen för att integrera Azure Active Directory-granskningsloggar

1. Öppna Kommandotolken och kör det här kommandot:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Kör följande kommando: 
 
   ``azlog createazureid``

   Det här kommandot uppmanas du att din Azure-inloggning. Kommandot skapar sedan ett Azure Active Directory tjänstens huvudnamn i Azure AD-klienter som är värdar för Azure-prenumerationer som den inloggade användaren är administratör, en medadministratör eller en ägare. Kommandot misslyckas om den inloggade användaren är endast gästanvändaren i Azure AD-klient. Autentisering till Azure sker via Azure AD. Skapa ett huvudnamn för tjänsten för Azure Log integrering skapar Azure AD-identitet som har behörighet att läsa från Azure-prenumerationer.

3. Kör följande kommando för att ange dina klient-ID. Du måste vara medlem i rollen Administratör klient köra kommandot.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Exempel:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Kontrollera följande mappar för att bekräfta att de Azure Active Directory audit JSON loggfilerna skapas i dem:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Följande videoklipp visar stegen som beskrivs i den här artikeln:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Specifika anvisningar för att hämta informationen i JSON-filerna till din säkerhetsinformation och Händelsehanteringssystem (SIEM), försäljaren SIEM.

Community-hjälp är tillgänglig via den [Azure Log Integration MSDN-Forum](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Detta forum kan andra i Azure Log-integrering webbgruppen att stödja varandra med frågor, svar, tips och råd. Dessutom övervakar det här forumet teamet Azure Log-integrering och hjälper när den kan.

Du kan även öppna en [supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Välj **loggen Integration** som tjänsten som du begär support.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Log-integrering finns:

* [Microsoft Azure Log-integrering för Azure loggar](https://www.microsoft.com/download/details.aspx?id=53324): den här Download Center sidan ger information, systemkrav och Installationsinstruktioner för Azure Log-integrering.
* [Introduktion till Azure Log-integrering](security-azure-log-integration-overview.md): den här artikeln ger en introduktion till Azure Log Integration, de viktigaste funktionerna och hur det fungerar.
* [Azure Log Integration FAQ](security-azure-log-integration-faq.md): den här artikeln innehåller svar på frågor om Azure Log-integrering.
* [Nya funktioner för Azure-diagnostik och Azure-granskningsloggar](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): det här blogginlägget ger en introduktion till Azure-granskningsloggarna och andra funktioner som hjälper dig insyn i hur Azure-resurser.
