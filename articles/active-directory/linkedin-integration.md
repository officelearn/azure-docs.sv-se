---
title: Aktivera LinkedIn-anslutningar för Microsoft-appar och tjänster i Azure Active Directory | Microsoft Docs
description: Beskriver hur du aktiverar eller inaktiverar LinkedIn konto anslutningar för Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/22/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 33e3305288edc3990ed88b39c819293a8adc2dfe
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>LinkedIn konto anslutningar för Microsoft-appar och tjänster
I den här artikeln får du lära dig hur hantera LinkedIn konto anslutningar för din klient i administrationscentret för Azure Active Directory (AD Azure). 

> [!IMPORTANT]
> LinkedIn konto anslutningar är för närvarande på att släppas till Azure AD-klienter. När den är distribuerat till din klient aktiveras som standard. Det är inte tillgänglig för Förenta staternas myndigheter och organisationer med Exchange Online postlådor som finns i Australien Kanada, Kina, Frankrike, Tyskland, Indien, Sydkorea, Storbritannien, Japan och Sydafrika. Stöd för dessa platser postlådan kommer snart.  En uppdaterad vy över distributionen information finns i [Office 365-översikt](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc) sidan.

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>Hur LinkedIn konto anslutningar visas för användaren
LinkedIn konto anslutningar kan användare Se offentliga LinkedIn-profilinformation i några av sina Microsoft-appar. Användare i din klient kan du ansluta sitt LinkedIn och Microsoft arbete eller skola konton för att visa ytterligare information för LinkedIn-profilen. Mer information finns i [LinkedIn information och funktioner i Microsoft-appar och tjänster](https://go.microsoft.com/fwlink/?linkid=850740).

När användare i din organisation ansluter arbetet LinkedIn och Microsoft- eller skolkonton, har de två alternativ: 
* Ge behörighet att dela data mellan båda kontona. Detta innebär att de ger behörighet för deras LinkedIn-konto för att dela data med sitt arbete med Microsoft eller skolkonto, samt deras Microsoft arbets- eller skolkonto för att dela data med sitt LinkedIn-konto. Data som delas med LinkedIn lämnar online-tjänster. 
* Ger behörighet att dela data endast från sina LinkedIn-konto till sitt arbete med Microsoft och skolkonto

Mer information om data som delas mellan användarnas LinkedIn och Microsoft arbetar eller skolkonton, se [LinkedIn i Microsoft-program på arbetsplatsen eller skolan](https://www.linkedin.com/help/linkedin/answer/84077). 
* [Användare kan koppla konton](https://www.linkedin.com/help/linkedin/answer/85097) och ta bort data delningsbehörigheter när som helst. 
* [Användarna kan styra hur sina egna LinkedIn-profilen visas](https://www.linkedin.com/help/linkedin/answer/83), inklusive om deras profil kan visas i Microsoft-appar.

## <a name="privacy-considerations"></a>Överväganden för sekretess
Om du aktiverar LinkedIn konto anslutningar kan Microsoft-appar och -tjänster åtkomst till vissa av dina användare LinkedIn information. Läs den [sekretesspolicy för Microsoft](https://privacy.microsoft.com/privacystatement/) lära dig mer om överväganden för sekretess när du aktiverar anslutningar för LinkedIn-konto i Azure AD. 

## <a name="manage-linkedin-account-connections"></a>Hantera anslutningar till LinkedIn-konto
Funktion för anslutningar av LinkedIn-kontot är aktiverat som standard för din hela klient. Du kan välja att inaktivera LinkedIn konto anslutningar för din hela klient eller aktivera LinkedIn konto anslutningar för valda användare i din klient. 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>Aktivera eller inaktivera LinkedIn konto anslutning för din klient i Azure-portalen

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/) med ett konto som är en global administratör för Azure AD-klient.
2. Välj **användare**.
3. På den **användare** bladet väljer **användarinställningar**.
4. Under **LinkedIn konto anslutningar**:
  * Välj **Ja** att aktivera LinkedIn konto anslutningar för alla användare i din klientorganisation
  * Välj **valda** för att aktivera LinkedIn-kontoanvändare anslutningar för endast valda klient
  * Välj **nr** att inaktivera LinkedIn konto anslutningar för alla användare ![anslutningar för att aktivera LinkedIn-konto](./media/linkedin-integration/LinkedIn-integration.png)
5. Spara dina inställningar när du är klar genom att välja **spara**.

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>Aktivera eller inaktivera LinkedIn konto anslutningar för din organisation Office 2016 appar med hjälp av Grupprincip

1. Hämta den [filer för Office 2016 administrativa mallar (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahera den **ADMX** filer och kopiera dem till centrala lagringsplatsen.
3. Öppna Grupprinciphantering.
4. Skapa ett grupprincipobjekt med följande inställning: **Användarkonfiguration** > **Administrationsmallar** > **Microsoft Office 2016**  >  **Diverse** > **visa LinkedIn-funktioner i Office-program**.
5. Välj **aktiverat** eller **inaktiverade**.
  * När principen är **aktiverad**, **visa LinkedIn-funktioner i Office-program** hittades i dialogrutan Alternativ för Office 2016 är aktiverad. Det innebär också att användare i organisationen kan använda LinkedIn-funktionerna i Office-programmen.
  * När principen är **inaktiverad**, **visa LinkedIn-funktioner i Office-program** hittades i alternativen för Office 2016 dialogrutan anges till inaktiverat tillstånd och slutanvändarna kan inte ändra den här inställningen. Användare i din organisation kan inte använda LinkedIn-funktioner i sina program för Office 2016. 

Den här principen påverkar endast Office 2016 appar för en lokal dator. Användare kan se LinkedIn-funktioner i profilen kort i Office 365, även om de inaktivera LinkedIn i sina appar för Office 2016. 

### <a name="learn-more"></a>Läs mer 
* [LinkedIn information och funktioner i Microsoft-appar](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn hjälp och support](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Nästa steg
Du kan använda följande länk för att visa dina aktuella LinkedIn konto anslutningar i Azure-portalen:

[Konfigurera LinkedIn konto anslutningar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 