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
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60469682"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Samtycka till LinkedIn-kontoanslutningar för en Azure Active Directory-organisation

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

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>Använd Azure-portalen för att aktivera LinkedIn-kontoanslutningar

Du kan aktivera LinkedIn-kontoanslutningar för de användare som du vill ha åtkomst från hela organisationen för bara utvalda användare i din organisation.

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com/) med ett konto som är en global administratör för Azure AD-organisation.
2. Välj **användare**.
3. På den **användare** bladet väljer **användarinställningar**.
4. Under **LinkedIn-kontoanslutningar**, Tillåt användare att ansluta sina konton för att komma åt sina LinkedIn anslutningar inom vissa Microsoft-appar. Inga data delas förrän användare godkänna att ansluta sina konton.

  * Välj **Ja** samtycker till att tjänsten för alla användare i organisationen
  * Välj **valda** att ge samtycke för endast valda användarna i organisationen
  * Välj **nr** att ångra detta godkännande för användare i din organisation

    ![Integrera LinkedIn-kontoanslutningar i organisationen](./media/linkedin-integration/linkedin-integration.png)

5. När du är klar väljer du **spara** att spara dina inställningar.
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Använda grupprinciper för att aktivera LinkedIn-kontoanslutningar

1. Ladda ned den [Office 2016 administrativa mallar-filer (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahera den **ADMX** filer och kopiera dem till din centrala store.
3. Öppna Grupprinciphantering.
4. Skapa ett grupprincipobjekt med följande inställning: **Användarkonfiguration** > **Administrationsmallar** > **Microsoft Office 2016** > **diverse**  >  **Visa LinkedIn-funktionerna i Office-program**.
5. Välj **aktiverat** eller **inaktiverad**.
  
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
