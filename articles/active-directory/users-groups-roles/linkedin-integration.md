---
title: Aktivera LinkedIn anslutningar-integrering i Azure Active Directory | Microsoft Docs
description: Beskriver hur du aktiverar eller inaktiverar LinkedIn-kontoanslutningar för Microsoft-appar i Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 0eaa2656313ecd9b64503051265dc16285f0a1f3
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44392272"
---
# <a name="linkedin-account-connections"></a>LinkedIn-kontoanslutningar

I den här artikeln får du lära dig aktivera eller inaktivera LinkedIn-kontoanslutningar för din klient i administrationscentret för Azure Active Directory (AD Azure).

> [!IMPORTANT]
> LinkedIn-kontoanslutningar inställning för närvarande distribueras till Azure AD-klienter. När den distribueras till din klient, aktiveras den som standard. 
> 
> Undantag:
> * Inställningen är inte tillgänglig för kunder som använder Microsoft Cloud för amerikanska myndigheter, Microsoft Cloud Tyskland eller Azure och Office 365 som drivs av 21Vianet i Kina.
> * Inställningen är inaktiverad som standard för klienter som etableras i Tyskland. Observera att inställningen inte är tillgänglig för kunder som använder Microsoft Cloud Tyskland.
> * Inställningen är inaktiverad som standard för klienter som etableras i Frankrike.

> Integrationen fungerar bara om du har aktiverat *och* om du tillåter användarna att godkänna att appar får åtkomst till företagets data å deras vägnar. Läs om hur inställningen medgivande [hur du tar bort en användares åtkomst till ett program](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-in-the-azure-portal"></a>Aktivera eller inaktivera LinkedIn-kontoanslutningar för din klient i Azure portal

Du kan aktivera eller inaktivera LinkedIn-kontoanslutningar för hela din klientorganisation eller bara valda användare i din klient.

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com/) med ett konto som är en global administratör för Azure AD-klient.
2. Välj **användare**.
3. På den **användare** bladet väljer **användarinställningar**.
4. Under **LinkedIn-kontoanslutningar**:
  * Välj **Ja** att aktivera LinkedIn-kontoanslutningar för alla användare i din klient
  * Välj **valda** för att aktivera LinkedIn-anslutningar för endast valda klient kontoanvändare
  * Välj **nr** att inaktivera LinkedIn-kontoanslutningar för alla användare ![att aktivera LinkedIn-kontoanslutningar](./media/linkedin-integration/linkedin-integration.png)
5. Spara dina inställningar när du är klar genom att välja **spara**.

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-using-group-policy"></a>Aktivera eller inaktivera LinkedIn-kontoanslutningar för din klient med en Grupprincip

1. Ladda ned den [Office 2016 administrativa mallar-filer (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrahera den **ADMX** filer och kopiera dem till din centrala store.
3. Öppna Grupprinciphantering.
4. Skapa ett grupprincipobjekt med följande inställning: **Användarkonfiguration** > **Administrationsmallar** > **Microsoft Office 2016**  >  **Diverse** > **visa LinkedIn-funktionerna i Office-program**.
5. Välj **aktiverat** eller **inaktiverad**.
  
 Region | Effekt
------ | ------
**Aktiverad** | Den **visa LinkedIn-funktionerna i Office-program** i alternativen för Office 2016 är aktiverad. Användare i organisationen kan använda LinkedIn-funktionerna i Office-programmen.
 **Inaktiverad** | Den **visa LinkedIn-funktionerna i Office-program** i Office 2016 alternativ är inaktiverad och slutanvändare kan inte ändra den här inställningen. Användare i din organisation kan inte använda LinkedIn-funktionerna i Office 2016-programmen.

Den här grupprincipen påverkar endast Office 2016-appar för en lokal dator. Användarna kan se LinkedIn-funktionerna i profilen kort i hela Office 365, även om de inaktivera LinkedIn i sina Office 2016-appar.

## <a name="learn-more"></a>Läs mer

* [Integrera LinkedIn i din organisation](linkedin-user-consent.md)

* [LinkedIn-information och funktioner i din Microsoft-appar](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn Hjälpcenter](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Nästa steg
Använd länken nedan för att se din aktuella LinkedIn-kontoanslutningar i Azure portal:

[Konfigurera LinkedIn-kontoanslutningar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 