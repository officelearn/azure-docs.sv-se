---
title: Administratörsmedgivande för LinkedIn-kontoanslutningar – Azure AD | Microsoft-dokument
description: Förklarar hur du aktiverar eller inaktiverar LinkedIn-integrationskontoanslutningar i Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf65f69d9dcaf6de2236c98b56b58ec7e021099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025400"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrera LinkedIn-kontoanslutningar i Azure Active Directory

Du kan tillåta användare i organisationen att komma åt sina LinkedIn-anslutningar i vissa Microsoft-appar. Inga data delas förrän användarna samtycker till att ansluta sina konton. Du kan integrera din organisation i [administrationscentret](https://aad.portal.azure.com)för Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Inställningen För LinkedIn-kontoanslutningar distribueras för närvarande till Azure AD-organisationer. När den distribueras till din organisation är den aktiverad som standard.
> 
> Undantag:
> * Inställningen är inte tillgänglig för kunder som använder Microsoft Cloud för US Government, Microsoft Cloud Germany eller Azure och Office 365 som drivs av 21Vianet i Kina.
> * Inställningen är inaktiverad som standard för klienter som etablerats i Tyskland. Observera att inställningen inte är tillgänglig för kunder som använder Microsoft Cloud Germany.
> * Inställningen är inaktiverad som standard för klienter som etablerats i Frankrike.
>
> När LinkedIn-kontoanslutningar har aktiverats för din organisation fungerar kontoanslutningarna efter att användarna har medgivandet till appar som använder företagsdata för deras räkning. Information om inställningen för användarens medgivande finns i [Så här tar du bort en användares åtkomst till ett program](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Aktivera LinkedIn-kontoanslutningar i Azure-portalen

Du kan aktivera LinkedIn-kontoanslutningar för endast de användare som du vill ha åtkomst till, från hela organisationen till endast valda användare i organisationen.

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com/) med ett konto som är en global administratör för Azure AD-organisationen.
1. Välj **Användare**.
1. På bladet **Användare** väljer du **Användarinställningar**.
1. Under **LinkedIn-kontoanslutningar**kan användare ansluta sina konton för att komma åt sina LinkedIn-anslutningar i vissa Microsoft-appar. Inga data delas förrän användarna samtycker till att ansluta sina konton.

    * Välj **Ja** om du vill aktivera tjänsten för alla användare i organisationen
    * Välj **Markerad grupp** om du bara vill aktivera tjänsten för en grupp valda användare i organisationen
    * Välj **Nej** om du vill återkalla medgivande från alla användare i organisationen

    ![Integrera LinkedIn-kontoanslutningar i organisationen](./media/linkedin-integration/linkedin-integration.png)

1. När du är klar väljer du **Spara** för att spara inställningarna.

> [!Important]
> LinkedIn-integrering är inte fullt aktiverad för användarna förrän de samtycker till att ansluta sina konton. Inga data delas när du aktiverar kontoanslutningar för användarna.

### <a name="assign-selected-users-with-a-group"></a>Tilldela markerade användare med en grupp
Vi har ersatt alternativet "Selected" som anger en lista över användare med möjlighet att välja en grupp användare så att du kan aktivera möjligheten att ansluta LinkedIn- och Microsoft-konton för en enda grupp i stället för många enskilda användare. Om du inte har Aktiverat LinkedIn-kontoanslutningar för valda enskilda användare behöver du inte göra någonting. Om du tidigare har aktiverat LinkedIn-kontoanslutningar för valda enskilda användare bör du:

1. Hämta den aktuella listan över enskilda användare
1. Flytta de för närvarande aktiverade enskilda användarna till en grupp
1. Använd gruppen från föregående som den valda gruppen i inställningen LinkedIn-kontoanslutningar i administrationscentret för Azure AD.

> [!NOTE]
> Även om du inte flyttar dina valda enskilda användare till en grupp kan de fortfarande se LinkedIn-information i Microsoft-appar.

### <a name="get-the-current-list-of-selected-users"></a>Hämta den aktuella listan över valda användare

1. Logga in på Microsoft 365 med ditt administratörskonto.
1. Gå till https://linkedinselectedusermigration.azurewebsites.net/. Du kommer att se en lista över användare som har valts för LinkedIn-kontoanslutningar.
1. Exportera listan till en CSV-fil.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Flytta de markerade enskilda användarna till en grupp

1. Starta PowerShell
1. Installera Azure AD-modul genom att köra`Install-Module AzureAD`
1. Kör följande skript:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Information om hur du använder gruppen från steg två som den valda gruppen i inställningen LinkedIn-kontoanslutningar i Azure AD-administrationscentret finns [i Aktivera LinkedIn-kontoanslutningar i Azure-portalen](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Använda grupprincip för att aktivera LinkedIn-kontoanslutningar

1. Ladda ned [administrationsmallfilerna för Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Extrahera **ADMX-filerna** och kopiera dem till din centrala butik.
1. Öppna Grupprinciphantering.
1. Skapa ett grupprincipobjekt med följande inställning:**Administrativa mallar för** >  **användarkonfiguration** > **Microsoft Office 2016** > **Visa** > **LinkedIn-funktioner i Office-program**.
1. Välj **Aktiverad** eller **Inaktiverad**.
  
   Status | Verkan
   ------ | ------
   **Enabled** | Inställningen **Visa LinkedIn i Office-program** i Office 2016-alternativen är aktiverad. Användare i organisationen kan använda LinkedIn-funktioner i sina Office 2016-program.
   **Disabled** | Inställningen **Visa LinkedIn-funktioner i Office-program** i Office 2016-alternativen är inaktiverad och slutanvändare kan inte ändra den här inställningen. Användare i organisationen kan inte använda LinkedIn-funktioner i sina Office 2016-program.

Den här grupprincipen påverkar bara Office 2016-appar för en lokal dator. Om användare inaktiverar LinkedIn i sina Office 2016-appar kan de fortfarande se LinkedIn-funktioner i Office 365.

## <a name="next-steps"></a>Nästa steg

* [Användarens medgivande och datadelning för LinkedIn](linkedin-user-consent.md)

* [LinkedIn-information och funktioner i dina Microsoft-appar](https://go.microsoft.com/fwlink/?linkid=850740)

* [Hjälpcenter för LinkedIn](https://www.linkedin.com/help/linkedin)

* [Visa din aktuella LinkedIn-integreringsinställning i Azure-portalen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
