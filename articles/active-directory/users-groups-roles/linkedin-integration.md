---
title: Samtycka till LinkedIn-tjänster för din organisation – Azure Active Directory | Microsoft Docs
description: Beskriver hur du aktiverar eller inaktiverar LinkedIn-integrering för Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcb1696efe44293d01153aa37a9835ba5f43370
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199706"
---
# <a name="consent-to-linkedin-integration-for-your-azure-active-directory-organization"></a>Samtycka till LinkedIn-integrering för din Azure Active Directory-organisation

I den här artikeln får du lära dig aktivera eller inaktivera LinkedIn-integrering för organisationen i administrationscentret för Azure Active Directory (AD Azure).

> [!IMPORTANT]
> LinkedIn-integreringsinställningen distribueras för närvarande till Azure AD-organisationer. När den distribueras till din organisation, aktiveras den som standard.
> 
> Undantag:
> * Inställningen är inte tillgänglig för kunder som använder Microsoft Cloud för amerikanska myndigheter, Microsoft Cloud Tyskland eller Azure och Office 365 som drivs av 21Vianet i Kina.
> * Inställningen är inaktiverad som standard för klienter som etableras i Tyskland. Observera att inställningen inte är tillgänglig för kunder som använder Microsoft Cloud Tyskland.
> * Inställningen är inaktiverad som standard för klienter som etableras i Frankrike.
>
> Integrationen fungerar bara om du har aktiverat *och* när användare godkänna att appar får åtkomst till företagets data å deras vägnar. Information om inställningen för medgivande finns i [hur du tar bort en användares åtkomst till ett program](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Aktivera eller inaktivera LinkedIn-integrering för dina användare i Azure portal

Du kan aktivera eller inaktivera LinkedIn-integrering för hela din klientorganisation eller bara valda användare i din klient.

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/) med ett konto som är en global administratör för Azure AD-klient.
2. Välj **användare**.
3. På den **användare** bladet väljer **användarinställningar**.
4. Under **LinkedIn-kontoanslutningar**:

   * Välj **Ja** att ge samtycke för alla användare i organisationen att ansluta sina konton för att få åtkomst till sina LinkedIn-kontakter inom vissa Microsoft-appar.
   * Välj **valda** godkänna endast valda användare i organisationen kan ansluta sina konton för att få åtkomst till sina LinkedIn-kontakter inom vissa Microsoft-appar.
   * Välj **nr** att ångra detta godkännande för användare i din organisation att ansluta sina konton för att få åtkomst till sina LinkedIn-kontakter inom vissa Microsoft-appar.
    ![Aktiverar LinkedIn-integrering i organisationen](./media/linkedin-integration/linkedin-integration.png)
5. Spara dina inställningar när du är klar genom att välja **spara**.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>Aktivera eller inaktivera LinkedIn-integrering för dina användare i en Grupprincip

1. Ladda ned den [Office 2016 administrativa mallar-filer (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahera den **ADMX** filer och kopiera dem till din centrala store.
3. Öppna Grupprinciphantering.
4. Skapa ett grupprincipobjekt med följande inställning: **Användarkonfiguration** > **Administrationsmallar** > **Microsoft Office 2016** > **diverse**  >  **Visa LinkedIn-funktionerna i Office-program**.
5. Välj **aktiverat** eller **inaktiverad**.
  
   Status | Verkan
   ------ | ------
   **Aktiverad** | Den **visa LinkedIn-funktionerna i Office-program** i alternativen för Office 2016 är aktiverad. Användare i organisationen kan använda LinkedIn-funktionerna i Office-programmen.
   **Inaktiverad** | Den **visa LinkedIn-funktionerna i Office-program** i Office 2016 alternativ är inaktiverad och slutanvändare kan inte ändra den här inställningen. Användare i din organisation kan inte använda LinkedIn-funktionerna i Office 2016-programmen.

Den här grupprincipen påverkar endast Office 2016-appar för en lokal dator. Användarna kan se LinkedIn-funktionerna i profilen kort i hela Office 365, även om de inaktivera LinkedIn i sina Office 2016-appar.

## <a name="learn-more"></a>Läs mer

* [Integrera LinkedIn i din organisation](linkedin-user-consent.md)

* [LinkedIn-information och funktioner i din Microsoft-appar](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn Hjälpcenter](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Nästa steg

Använd länken nedan för att se din aktuella LinkedIn-integreringsinställningen i Azure portal:

[Visa din aktuella inställningen för LinkedIn-integrering i Azure portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
