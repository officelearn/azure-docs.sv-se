---
title: Aktivera LinkedIn-anslutningar för Microsoft-appar och tjänster i Azure Active Directory | Microsoft Docs
description: Beskriver hur du aktiverar eller inaktiverar LinkedIn-kontoanslutningar för Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 4b3ff0b2481b42f516d28ac17f2616685730b7d5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872418"
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>LinkedIn-kontoanslutningar för Microsoft-appar och tjänster
I den här artikeln får du lära dig hur du hanterar LinkedIn-kontoanslutningar för din klient i administrationscentret för Azure Active Directory (AD Azure). 

> [!IMPORTANT]
> LinkedIn-konto anslutningar funktioner distribueras för närvarande till Azure AD-klienter. När den distribueras till din klient, aktiveras den som standard. Det är inte tillgänglig för myndigheter i USA och organisationer med Exchange Online postlådor som finns i Australien, Kanada, Kina, Frankrike, Tyskland, Indien, Sydkorea, Storbritannien, Japan och Sydafrika. Stöd för platserna postlådan kommer snart.  En uppdaterad vy över information om distribution finns det [Office 365-översikt](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc) sidan.

## <a name="benefit-to-users"></a>Dra till användare
När användare ansluter sina LinkedIn-konto, används LinkedIn-information för att visa personlig information och funktioner i olika Microsoft-appar eller tjänster. Användarna kan se insikter om personerna som de arbetar med i Microsoft profil kortet, även om de personer utanför organisationen. Med tiden blir också upplevelsen LinkedIn skräddarsydda till sitt arbete och mer relevant. Till exempel LinkedIn kan föreslå nya anslutningar baserat på som användare arbetar med eller surface insikter om personer i deras kalender för den dagen.

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Hur LinkedIn-kontoanslutningar visas för användaren
LinkedIn-kontoanslutningar tillåta användare att se offentliga LinkedIn-profilinformation i några av sina Microsoft-appar. Användare i din klient kan du ansluta arbetet LinkedIn- och Microsoft- eller skolkonton för att visa ytterligare information för LinkedIn-profil. Mer information finns i [LinkedIn information och funktioner i Microsoft-appar och tjänster](https://go.microsoft.com/fwlink/?linkid=850740).

När användare i din organisation ansluta sitt arbete med LinkedIn- och Microsoft- eller skolkonton, har de två alternativ: 
* Ge behörighet att dela data mellan båda kontona. Det innebär att de ger behörighet för sitt LinkedIn-konto för att dela data med sina Microsoft arbets eller skolkonto, samt deras Microsoft arbets- eller skolkonto för att dela data med sina LinkedIn-konto. Data som delas med LinkedIn lämnar onlinetjänsterna. 
* Ge behörighet att dela data endast från sina LinkedIn-konto till sitt Microsoft-arbete och skola konto

För mer information om data som delas mellan användarnas LinkedIn- och Microsoft arbets eller skolkonto kan du läsa [LinkedIn i Microsoft-program på arbetsplatsen eller skolan](https://www.linkedin.com/help/linkedin/answer/84077). 
* [Användare kan koppla från konton](https://www.linkedin.com/help/linkedin/answer/85097) och ta bort data delningsbehörigheter när som helst. 
* [Användare kan styra hur deras egen LinkedIn-profil visas](https://www.linkedin.com/help/linkedin/answer/83), inklusive om deras profil visas i Microsoft-appar.

## <a name="privacy-considerations"></a>Överväganden för sekretess
Om du aktiverar LinkedIn-kontoanslutningar kan Microsoft-appar och tjänster för att få åtkomst till vissa av dina användares LinkedIn-information. Läs den [Microsoft Privacy Statement](https://privacy.microsoft.com/privacystatement/) mer information om överväganden för sekretess när du aktiverar LinkedIn-kontoanslutningar i Azure AD. 

## <a name="manage-linkedin-account-connections"></a>Hantera LinkedIn-kontoanslutningar
LinkedIn-konto anslutningar funktionen är aktiverad som standard för hela din klientorganisation. Du kan välja att inaktivera LinkedIn-kontoanslutningar för hela din klientorganisation eller aktivera LinkedIn-kontoanslutningar för valda användare i din klient. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Aktivera eller inaktivera LinkedIn anslutning för din klient i Azure portal

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/) med ett konto som är en global administratör för Azure AD-klient.
2. Välj **användare**.
3. På den **användare** bladet väljer **användarinställningar**.
4. Under **LinkedIn-kontoanslutningar**:
  * Välj **Ja** att aktivera LinkedIn-kontoanslutningar för alla användare i din klient
  * Välj **valda** för att aktivera LinkedIn-anslutningar för endast valda klient kontoanvändare
  * Välj **nr** att inaktivera LinkedIn-kontoanslutningar för alla användare ![att aktivera LinkedIn-kontoanslutningar](./media/linkedin-integration/linkedin-integration.png)
5. Spara dina inställningar när du är klar genom att välja **spara**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Aktivera eller inaktivera LinkedIn-kontoanslutningar för organisationens Office 2016-appar med hjälp av en Grupprincip

1. Ladda ned den [Office 2016 administrativa mallar-filer (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahera den **ADMX** filer och kopiera dem till din centrala store.
3. Öppna Grupprinciphantering.
4. Skapa ett grupprincipobjekt med följande inställning: **Användarkonfiguration** > **Administrationsmallar** > **Microsoft Office 2016**  >  **Diverse** > **visa LinkedIn-funktionerna i Office-program**.
5. Välj **aktiverat** eller **inaktiverad**.
  * När principen är **aktiverad**, **visa LinkedIn-funktionerna i Office-program** inställningen i dialogrutan Alternativ för Office 2016 har aktiverats. Det innebär också att användarna i organisationen kan använda LinkedIn-funktionerna i Office-programmen.
  * När principen är **inaktiverad**, **visa LinkedIn-funktionerna i Office-program** hittades i alternativen för Office 2016 dialogrutan anges till inaktiverat tillstånd och slutanvändare kan inte ändra den här inställningen. Användare i din organisation kan inte använda LinkedIn-funktionerna i Office 2016-programmen.

Den här grupprincipen påverkar endast Office 2016-appar för en lokal dator. Användarna kan se LinkedIn-funktionerna i profilen kort i hela Office 365, även om de inaktivera LinkedIn i sina Office 2016-appar. 

### <a name="learn-more"></a>Läs mer 
* [LinkedIn-information och funktioner i din Microsoft-appar](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn Hjälpcenter](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Nästa steg
Använd länken nedan för att se din aktuella LinkedIn-kontoanslutningar i Azure portal:

[Konfigurera LinkedIn-kontoanslutningar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 