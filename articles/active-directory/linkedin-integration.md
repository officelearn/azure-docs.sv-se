---
title: "Aktivera eller inaktivera LinkedIn-integrering för Office-program i Azure Active Directory | Microsoft Docs"
description: "Beskriver hur du aktiverar eller inaktiverar LinkedIn-integrering för Microsoft-appar i Azure Active Directory"
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/30/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 5ebc44d0ef6200baeacf4f1f8c4371e2d1eed9db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="linkedin-integration-for-office-applications"></a>LinkedIn-integrering för Office-program
Den här artikeln förklarar hur du begränsa antalet användare som LinkedIn-integrering finns i Azure Active Directory (AD Azure). LinkedIn-integrering är aktiverad som standard när du har lagt till din klient, som ger användare åtkomst till offentliga LinkedIn-data i några av sina Microsoft-appar. Varje användare kan oberoende av varandra välja att ansluta sina arbets- eller skolkonto konto till deras LinkedIn-konto.

> [!IMPORTANT]
> LinkedIn integration distribueras inte till alla Azure AD-klienter på samma gång. När den är distribuerat till din Azure-klient, är LinkedIn-integrering aktiverad som standard. LinkedIn-integrering är inte tillgänglig för gå-lokala och statliga myndigheter innehavare. 

## <a name="linkedin-integration-from-the-user-perspective"></a>LinkedIn integration ur användarens perspektiv
När användarna i din organisation ansluter sina LinkedIn konto till sitt arbete eller skola, [de tillåta LinkedIn att tillhandahålla data](https://www.linkedin.com/help/linkedin/answer/84077) som ska användas i Microsoft-appar och tjänster som din organisation tillhandahåller. [Användare kan koppla konton](https://www.linkedin.com/help/linkedin/answer/85097), som tar bort behörigheten för LinkedIn att dela data med Microsoft. LinkedIn integration använder offentligt tillgängliga LinkedIn-profilinformation. [Användarna kan styra hur sina egna LinkedIn-profilen visas](https://www.linkedin.com/help/linkedin/answer/83) med LinkedIn sekretessinställningar, till exempel om deras profil kan visas i Microsoft-appar.

## <a name="privacy-considerations"></a>Överväganden för sekretess
Aktiverar LinkedIn-integrering i Azure AD kan Microsoft-appar och -tjänster åtkomst till vissa av dina användare LinkedIn information. Läs den [sekretesspolicy för Microsoft](https://privacy.microsoft.com/privacystatement/) lära dig mer om överväganden för sekretess när du aktiverar LinkedIn-integrering i Azure AD. 

## <a name="manage-linkedin-integration"></a>Hantera LinkedIn-integrering
LinkedIn-integrering för företag är aktiverad som standard i Azure AD. Om du aktiverar LinkedIn-integrering kan alla användare i din organisation använder LinkedIn-funktioner i Microsoft-tjänster, till exempel visa LinkedIn-profiler i Outlook. Inaktivera LinkedIn integration tar bort LinkedIn-funktioner från Microsoft-appar och tjänster och stoppar Datadelning mellan LinkedIn och din organisation via Microsoft-tjänster.

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>Aktivera eller inaktivera LinkedIn-integrering för din organisation i Azure-portalen

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/) med ett konto som är en global administratör för Azure AD-klient.
2. Välj **användare och grupper**.
3. På den **användare och grupper** bladet väljer **användarinställningar**.
4. Under **LinkedIn Integration**väljer **Ja** eller **nr** att aktivera eller inaktivera LinkedIn-integration.
   ![Aktivera LinkedIn-integrering](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>Aktivera eller inaktivera LinkedIn-integrering för din organisation Office 2016 appar med hjälp av Grupprincip

1. Hämta den [filer för Office 2016 administrativa mallar (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahera den **ADMX** filer och kopiera dem till din **centrallager**.
3. Öppna Grupprinciphantering.
4. Skapa ett grupprincipobjekt med följande inställning: **Användarkonfiguration** > **Administrationsmallar** > **Microsoft Office 2016**  >  **Diverse** > **möjliggör Integration LinkedIn**.
5. Välj **aktiverat** eller **inaktiverade**.
  * När principen är **aktiverad**, **visa LinkedIn-funktioner i Office-program** hittades i dialogrutan Alternativ för Office 2016 är aktiverad. Det innebär också att användare i organisationen kan använda LinkedIn-funktionerna i Office-programmen.
  * När principen är **inaktiverad**, **visa LinkedIn-funktioner i Office-program** hittades i alternativen för Office 2016 dialogrutan anges till inaktiverat tillstånd och slutanvändarna kan inte ändra den här inställningen. Användare i din organisation kan inte använda LinkedIn-funktioner i sina program för Office 2016. 

Den här principen påverkar endast Office 2016 appar för en lokal dator. Användare kan se LinkedIn-funktioner i profilen kort i Office 365, även om de inaktivera LinkedIn i sina appar för Office 2016. 

### <a name="learn-more"></a>Läs mer 
* [LinkedIn information och funktioner i Microsoft-appar](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn hjälp och support](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Nästa steg
Du kan använda följande länk för att visa din aktuella LinkedIn-integreringsinställningen i Azure-portalen:

[Konfigurera LinkedIn-integrering](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 