---
title: Administratörens godkännande för LinkedIn-kontoanslutningar - Azure Active Directory | Microsoft Docs
description: Beskriver hur du aktiverar eller inaktiverar LinkedIn-integrering-kontoanslutningar i Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1130885cc3168582935264ffaad9fd7a8ba3c60b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920237"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrera LinkedIn-kontoanslutningar i Azure Active Directory

Du kan låta användare i din organisation åtkomst till sina LinkedIn anslutningar inom vissa Microsoft-appar. Inga data delas förrän användare godkänna att ansluta sina konton. Du kan integrera din organisation i Azure Active Directory (Azure AD) [Administrationscenter](https://aad.portal.azure.com).

> [!IMPORTANT]
> LinkedIn-kontoanslutningar inställning för närvarande distribueras till Azure AD-organisationer. När den distribueras till din organisation, aktiveras den som standard.
> 
> Undantag:
> * Inställningen är inte tillgänglig för kunder som använder Microsoft Cloud för amerikanska myndigheter, Microsoft Cloud Tyskland eller Azure och Office 365 som drivs av 21Vianet i Kina.
> * Inställningen är inaktiverad som standard för klienter som etableras i Tyskland. Observera att inställningen inte är tillgänglig för kunder som använder Microsoft Cloud Tyskland.
> * Inställningen är inaktiverad som standard för klienter som etableras i Frankrike.
>
> När LinkedIn-kontoanslutningar är aktiverade för din organisation fungerar kontoanslutningar när användare godkänna att appar får åtkomst till företagets data å deras vägnar. Information om inställningen för medgivande finns i [hur du tar bort en användares åtkomst till ett program](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Aktivera LinkedIn-kontoanslutningar i Azure portal

Du kan aktivera LinkedIn-kontoanslutningar för de användare som du vill ha åtkomst från hela organisationen för bara utvalda användare i din organisation.

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com/) med ett konto som är en global administratör för Azure AD-organisation.
1. Välj **användare**.
1. På den **användare** bladet väljer **användarinställningar**.
1. Under **LinkedIn-kontoanslutningar**, Tillåt användare att ansluta sina konton för att komma åt sina LinkedIn anslutningar inom vissa Microsoft-appar. Inga data delas förrän användare godkänna att ansluta sina konton.

    * Välj **Ja** aktiverar tjänsten för alla användare i din organisation
    * Välj **valda** att aktivera en grupp för markerade användare i din organisation
    * Välj **nr** att ångra detta godkännande från alla användare i din organisation

    ![Integrera LinkedIn-kontoanslutningar i organisationen](./media/linkedin-integration/linkedin-integration.png)

1. När du är klar väljer du **spara** att spara dina inställningar.

> [!Important]
> LinkedIn-integrering aktiveras inte helt för användarna tills de samtycka till att ansluta sina konton. Inga data delas när du aktiverar kontoanslutningar för dina användare.

### <a name="assign-selected-users-with-a-group"></a>Tilldela valda användare med en grupp
Vi har ersatt 'Valda' alternativet som anger en lista över användare med möjlighet att välja en grupp användare så att du kan göra möjligheten att ansluta LinkedIn och Microsoft-konton för en grupp i stället för många enskilda användare. Om du inte har aktiverats för valda individer LinkedIn-kontoanslutningar, behöver du inte göra något. Om du redan har aktiverat LinkedIn-kontoanslutningar för valda enskilda användare, bör du:

1. Hämta den aktuella listan över enskilda användare
1. Flytta är aktiverad enskilda användare till en grupp
1. Använd gruppen från den tidigare som den valda gruppen i LinkedIn-kontoanslutningar i Azure AD-administrationscentret.

> [!NOTE]
> Även om du inte flytta dina för tillfället valda enskilda användare till en grupp, kan de fortfarande se LinkedIn-information i Microsoft-appar.

### <a name="get-the-current-list-of-selected-users"></a>Hämta den aktuella listan över valda användare

1. Logga in på Microsoft 365 med ditt administratörskonto.
1. Gå till https://linkedinselectedusermigration.azurewebsites.net/. Lista över användare som har valts för LinkedIn-kontoanslutningar visas.
1. Exportera listan till en CSV-fil.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Flytta de markerade enskilda användarna till en grupp

1. Starta PowerShell
1. Installera Azure AD-modulen genom att köra `Install-Module AzureAD`
1. Kör följande skript:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Om du vill använda i gruppen i steg två som den valda gruppen i LinkedIn-kontoanslutningar i Azure AD administratörscenter finns i [aktivera LinkedIn-kontoanslutningar i Azure-portalen](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Använda grupprinciper för att aktivera LinkedIn-kontoanslutningar

1. Ladda ned den [Office 2016 administrativa mallar-filer (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extrahera den **ADMX** filer och kopiera dem till din centrala store.
1. Öppna Grupprinciphantering.
1. Skapa ett grupprincipobjekt med följande inställning: **Användarkonfiguration** > **Administrationsmallar** > **Microsoft Office 2016** > **diverse**  >  **Visa LinkedIn-funktionerna i Office-program**.
1. Välj **aktiverat** eller **inaktiverad**.
  
   Status | Verkan
   ------ | ------
   **Aktiverad** | Den **visa LinkedIn-funktionerna i Office-program** i alternativen för Office 2016 är aktiverad. Användare i organisationen kan använda LinkedIn-funktionerna i sina Office 2016-program.
   **Inaktiverad** | Den **visa LinkedIn-funktionerna i Office-program** i Office 2016 alternativ är inaktiverad och slutanvändare kan inte ändra den här inställningen. Användare i din organisation kan inte använda LinkedIn-funktionerna i Office 2016-programmen.

Den här grupprincipen påverkar endast Office 2016-appar för en lokal dator. Om användarna har inaktiverat LinkedIn i deras Office 2016-appar, kan de fortfarande se LinkedIn-funktionerna i Office 365.

## <a name="next-steps"></a>Nästa steg

* [Användarens medgivande och Datadelning på LinkedIn](linkedin-user-consent.md)

* [LinkedIn-information och funktioner i din Microsoft-appar](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn Hjälpcenter](https://www.linkedin.com/help/linkedin)

* [Visa din aktuella inställningen för LinkedIn-integrering i Azure portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
