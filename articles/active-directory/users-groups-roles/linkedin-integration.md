---
title: Aktivera LinkedIn-integrering i Azure Active Directory | Microsoft Docs
description: Beskriver hur du aktiverar eller inaktiverar LinkedIn-integrering för Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/22/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: cccbeefd49f5374aeffcae07ac8c888f7a28690f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160006"
---
# <a name="linkedin-integration"></a>LinkedIn-integrering

I den här artikeln får du lära dig aktivera eller inaktivera LinkedIn-integrering för din klient i administrationscentret för Azure Active Directory (AD Azure).

> [!IMPORTANT]
> LinkedIn-integreringsinställningen distribueras för närvarande till Azure AD-klienter. När den distribueras till din klient, aktiveras den som standard.
> 
> Undantag:
> * Inställningen är inte tillgänglig för kunder som använder Microsoft Cloud för amerikanska myndigheter, Microsoft Cloud Tyskland eller Azure och Office 365 som drivs av 21Vianet i Kina.
> * Inställningen är inaktiverad som standard för klienter som etableras i Tyskland. Observera att inställningen inte är tillgänglig för kunder som använder Microsoft Cloud Tyskland.
> * Inställningen är inaktiverad som standard för klienter som etableras i Frankrike.

> Integrationen fungerar bara om du har aktiverat *och* om du tillåter användarna att godkänna att appar får åtkomst till företagets data å deras vägnar. Läs om hur inställningen medgivande [hur du tar bort en användares åtkomst till ett program](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Aktivera eller inaktivera LinkedIn-integrering för dina användare i Azure portal

Du kan aktivera eller inaktivera LinkedIn-integrering för hela din klientorganisation eller bara valda användare i din klient.

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/) med ett konto som är en global administratör för Azure AD-klient.
2. Välj **användare**.
3. På den **användare** bladet väljer **användarinställningar**.
4. Under **LinkedIn-integrering**:
  * Välj **Ja** att aktivera LinkedIn-integrering för alla användare i din klient
  * Välj **valda** att aktivera LinkedIn-integrering för endast valda klientanvändare
  * Välj **nr** att inaktivera LinkedIn-integrering för alla användare ![att aktivera LinkedIn-integrering](./media/linkedin-integration/linkedin-integration.png)
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
