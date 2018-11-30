---
title: Azure Log Integration med Azure Active Directory-granskningsloggar | Microsoft Docs
description: Lär dig hur du installerar Azure Log Integration-tjänsten och integrera loggar från Azure-granskningsloggar
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
ms.date: 06/07/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 0b27cd314dd03375b2d2e6ba537cda74e2ec4310
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313249"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrera Azure Active Directory med granskningsloggar

Azure Active Directory (Azure AD)-granskningshändelser hjälper dig att identifiera Privilegierade åtgärder som inträffat i Azure Active Directory. Du kan se vilka typer av händelser som du kan spåra genom att granska [Azure Active Directory granskningsrapporthändelser](../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> Funktionen Azure Log integration upphör att gälla genom 06/01/2019. AzLog nedladdningar inaktiveras den 27 juni 2018. För information om vad du gör Flytta framåt granska inlägget [Använd Azure monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Stegen för att integrera Azure Active Directory-granskningsloggar

> [!NOTE]
> Innan du försöker utföra stegen i den här artikeln måste du granska den [börjar](security-azure-log-integration-get-started.md) och relevanta stegen.

1. Öppna Kommandotolken och kör det här kommandot:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Kör följande kommando: 
 
   ``azlog createazureid``

   Det här kommandot uppmanas du att Azure-autentiseringsuppgifter. Sedan skapar kommandot en Azure Active Directory-tjänstens huvudnamn i Azure AD-klienter som är värdar för de Azure-prenumerationer som den inloggade användaren är administratör, en delad administratör eller en ägare. Kommandot misslyckas om den inloggade användaren är bara en gästanvändare i Azure AD-klient. Autentisering till Azure görs via Azure AD. Skapa ett huvudnamn för tjänsten för Azure Log Integration skapar Azure AD-identitet som har behörighet att läsa från Azure-prenumerationer.

3. Kör följande kommando för att ange ditt klient-ID. Du måste vara medlem i administratörsrollen för klienten att köra kommandot.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Exempel:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Kontrollera följande mappar för att bekräfta att Azure Active Directory granskningsloggfilerna JSON skapas i dem:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Följande videoklipp visar de steg som beskrivs i den här artikeln:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Kontakta leverantören av SIEM specifika anvisningar för att hämta informationen i JSON-filerna till din säkerhetsinformation och Händelsehanteringssystem (SIEM).

Community-hjälp är tillgänglig via den [MSDN-Forum för Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Detta forum kan andra i Azure Log Integration-webbgruppen för varandra med frågor, svar, tips och råd. Dessutom övervakar det här forumet Azure Log Integration-teamet och hjälper när det är möjligt.

Du kan även öppna en [supportförfrågan](../azure-supportability/how-to-create-azure-support-request.md). Välj **Loggintegrering** som tjänsten som du begär support.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Log Integration finns:

* [Microsoft Azure Log Integration för Azure loggar](https://www.microsoft.com/download/details.aspx?id=53324): den här Download Center sidan ger information om systemkrav och installationsanvisningar för Azure Log Integration.
* [Introduktion till Azure Log Integration](security-azure-log-integration-overview.md): den här artikeln ger en introduktion till Azure Log Integration, de viktigaste funktionerna och hur det fungerar.
* [Azure Log Integration FAQ](security-azure-log-integration-faq.md): den här artikeln ger svar på frågor om Azure Log Integration.
* [Nya funktioner för Azure Diagnostics och Azure-granskningsloggar](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): det här blogginlägget ger en introduktion till Azure-granskningsloggar och andra funktioner som hjälper dig att få insyn i driften av dina Azure-resurser.
