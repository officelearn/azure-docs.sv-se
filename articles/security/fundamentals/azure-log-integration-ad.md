---
title: Azure Log Integration med Azure Active Directory gransknings loggar | Microsoft Docs
description: Lär dig hur du installerar tjänsten Azure Log Integration och integrerar loggar från Azures gransknings loggar
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 0820ca227a4d0e8c71ed3f35cd8fa2841163d38f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727654"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrera Azure Active Directory gransknings loggar

Azure Active Directory (Azure AD) gransknings händelser hjälper dig att identifiera privilegierade åtgärder som har inträffat i Azure Active Directory. Du kan se vilka typer av händelser som du kan spåra genom att granska [Azure Active Directory gransknings rapport händelser](../../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> Funktionen Azure logg integrering kommer att föråldras med 06/15/2019. AzLog hämtningar inaktiverades den 27 juni 2018. Information om vad du kan göra när du flyttar framåt finns i [använda Azure Monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Steg för att integrera Azure Active Directory gransknings loggar

> [!NOTE]
> Innan du försöker utföra stegen i den här artikeln måste du läsa artikeln [Kom igång](azure-log-integration-get-started.md) och slutföra de relevanta stegen där.

1. Öppna kommando tolken och kör det här kommandot:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Kör följande kommando: 
 
   ``azlog createazureid``

   Med det här kommandot uppmanas du att ange din Azure-inloggning. Kommandot skapar sedan ett Azure Active Directory tjänstens huvud namn i Azure AD-klienterna som är värdar för de Azure-prenumerationer där den inloggade användaren är administratör, en gemensam administratör eller en ägare. Kommandot fungerar inte om den inloggade användaren bara är en gäst användare i Azure AD-klienten. Autentisering till Azure görs via Azure AD. När du skapar ett huvud namn för tjänsten för Azure Log Integration skapas den Azure AD-identitet som har åtkomst att läsa från Azure-prenumerationer.

3. Kör följande kommando för att ange klient-ID: t. Du måste vara medlem i rollen klient organisations administratör för att köra kommandot.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Exempel:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Kontrol lera följande mappar för att kontrol lera att JSON-filerna för Azure Active Directory gransknings loggen skapas i dem:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Följande videoklipp visar de steg som beskrivs i den här artikeln:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Om du vill ha mer information om hur du hämtar informationen i JSON-filerna till SIEM-systemet (Security information and Event Management) kontaktar du din SIEM-leverantör.

Community-assistans är tillgängligt via [Azure log integration MSDN-forumet](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Det här forumet gör det möjligt för personer i Azure Log Integration community att stödja varandra med frågor, svar, tips och trick. Dessutom övervakar Azure Log Integration-teamet det här forumet och ser när det kan.

Du kan också öppna en [support förfrågan](../../azure-supportability/how-to-create-azure-support-request.md). Välj **logg integrering** som den tjänst som du begär support för.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Log Integration finns i:

* [Microsoft Azure log integration för Azure-loggar](https://www.microsoft.com/download/details.aspx?id=53324): Den här Download Center-sidan innehåller information, system krav och Installationsinstruktioner för Azure Log Integration.
* [Introduktion till Azure log integration](azure-log-integration-overview.md): Den här artikeln beskriver hur du Azure Log Integration, dess viktiga funktioner och hur det fungerar.
* [Azure log integration vanliga frågor och svar](azure-log-integration-faq.md): Den här artikeln ger svar på frågor om Azure Log Integration.
* [Nya funktioner för Azure-diagnostik och Azures gransknings loggar](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): I det här blogg inlägget får du en introduktion till Azures gransknings loggar och andra funktioner som hjälper dig att få insikter om driften av dina Azure-resurser.
