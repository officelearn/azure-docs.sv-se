---
title: Slutanvändarupplevelser för program – Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) erbjuder anpassningsbara sätt att distribuera program för slutanvändare i din organisation.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: barbkess
ms.reviewer: arvindh
ms.openlocfilehash: b1a4ec4b3a75d1337c011275591af11da8c45a6a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629280"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Upplevelse för slutanvändare för program i Azure Active Directory
Azure Active Directory (Azure AD) erbjuder anpassningsbara sätt att distribuera program för slutanvändare i din organisation:

* Azure AD-åtkomstpanelen
* Startprogrammet för Office 365
* Direkt inloggning till federerade appar
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Vilka metoder som du väljer att distribuera i din organisation är passar dig bäst.

## <a name="azure-ad-access-panel"></a>Azure AD-åtkomstpanelen
Åtkomstpanelen på https://myapps.microsoft.com är en webbaserad portal där en användare med ett organisationskonto i Azure Active Directory för att visa och starta molnbaserade program som de har beviljats åtkomst med Azure AD-administratör. Om du är en användare med [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), du kan också använda funktioner för hantering av självbetjäning via åtkomstpanelen.

![Azure AD-åtkomstpanelen](media/what-is-single-sign-on/azure-ad-access-panel.png)

Åtkomstpanelen är separat från Azure-portalen och kräver inte användarna måste ha en Azure-prenumeration eller Office 365-prenumeration.

Mer information om Azure AD-åtkomstpanelen finns i den [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Startprogrammet för Office 365
För organisationer som har distribuerat Office 365, visas även program som är tilldelade till användare via Azure AD i Office 365-portalen på https://portal.office.com/myapps. Detta gör det enkelt och praktiskt för användare i en organisation att starta sina appar utan att behöva använda en andra portal och är den rekommenderade app starta lösningen för organisationer som använder Office 365.

![](./media/what-is-single-sign-on/officeapphub.png)

Läs mer om startprogrammet för Office 365, [appen visas i Office 365-appstartaren](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Direkt inloggning till federerade appar
Mest federerade program som stöder SAML 2.0, WS-Federation och OpenID connect också support möjligheten för användare att börja på programmet och sedan hämta loggat in via Azure AD genom automatisk omdirigering eller genom att klicka på en länk för inloggning. Detta kallas tjänstleverantör-initieras inloggnings- och mest federerade program i Azure AD-programgalleriet har stöd för den här (se dokumentationen länkad från appens konfigurationen för enkel inloggning för guiden i Azure-portalen för information).

![](./media/what-is-single-sign-on/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Inloggnings-länkar
Azure AD stöder också enkel inloggning Direktlänkar till enskilda program som har stöd för lösenordsbaserad enkel inloggning, länkade enkel inloggning och någon form av federerad enkel inloggning.

Dessa länkar är särskilt utformad URL: er som skickar en användare via Azure AD logga in igen för ett visst program utan att användaren lanseringen dem från Azure AD åtkomst till panelen eller Office 365. Dessa enkel inloggnings-URL: er finns under fliken instrumentpanel i alla redan integrerade program i Active Directory-avsnittet i Azure-portalen som visas i skärmbilden nedan.

![](./media/what-is-single-sign-on/deeplink.png)

Dessa länkar kan kopieras och klistras in var du vill att ange en länk till det valda programmet. Det kan i ett e-postmeddelande eller i valfri anpassad webbaserad portal som du har konfigurerat för programåtkomst för användare. Här är ett exempel på en Azure AD direkt inloggnings-URL för enkel för Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Liknar specifika URL: er för åtkomstpanelen, du kan också anpassa denna URL genom att lägga till en av de aktiva eller verifierade domänerna för din katalog myapps.microsoft.com-domän. Detta säkerställer att alla företagsprofilering har lästs in direkt på sidan logga in utan att användaren behöver ange sitt användar-ID först:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

När en behörig användare klickar på någon av länkarna programspecifika visas de först finns i deras organisationens inloggningssida (förutsatt att de redan inte är inloggad) och efter inloggningen dirigeras om till appen utan att stoppa på åtkomstpanelen först. Om användaren saknar förutsättningar för att komma åt programmet, till exempel webbläsartillägget lösenordsbaserad enkel inloggning sedan uppmanas länken användaren att installera tillägget saknas. Webbadressen för länken förblir konstant om enkel inloggning för konfigurationen för programmet ändras.

Dessa länkar använder samma metoder för kontroll av åtkomst som åtkomstpanelen och Office 365 och endast de användare eller grupper som har tilldelats till programmet i Azure-portalen kommer att kunna autentisera. Alla användare som är obehörig visas ett meddelande som förklarar att de inte har beviljats åtkomst och får en länk för att läsa in panelen om du vill visa tillgängliga program som de har åtkomst.

## <a name="next-steps"></a>Nästa steg
Distribution planer finns [planer för Azure Active Directory-distribution](../fundamentals/active-directory-deployment-plans.md)