---
title: Slut användar upplevelse för program – Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) tillhandahåller flera anpassningsbara sätt att distribuera program till slutanvändare i din organisation.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4a091abff02e544f97f5621b56836cf6ba6e237
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175956"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Slut användar upplevelse för program i Azure Active Directory

Azure Active Directory (Azure AD) tillhandahåller flera anpassningsbara sätt att distribuera program till slutanvändare i din organisation:

* Åtkomst panel för Azure AD
* Office 365-program start
* Direkt inloggning till federerade appar
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Vilken metod (er) du väljer att distribuera i din organisation är ditt eget gottfinnande.

## <a name="azure-ad-access-panel"></a>Åtkomst panel för Azure AD

Åtkomst panelen på https://myapps.microsoft.com är en webbaserad portal som gör det möjligt för slutanvändare med ett organisations konto i Azure Active Directory att visa och starta molnbaserade program som de har beviljats åtkomst till av Azure AD-administratören. Om du är en slutanvändare med [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)kan du även använda funktioner för grupp hantering via självbetjäning via åtkomst panelen.

![Skärm bild som visar Azure AD Access panel-portalen](media/what-is-single-sign-on/azure-ad-access-panel.png)

Som standard visas alla program på en enda sida. Men du kan använda arbets ytor för att gruppera relaterade program och presentera dem på en separat flik, vilket gör dem lättare att hitta. Du kan till exempel använda arbets ytor för att skapa logiska grupperingar av program för vissa jobb roller, aktiviteter, projekt och så vidare. Mer information finns i så [här använder du arbets ytor i Mina appar för att anpassa användar åtkomst paneler (för hands version)](access-panel-workspaces.md). 

Åtkomst panelen är separat från Azure Portal och kräver inte att användare har en Azure-prenumeration eller en Office 365-prenumeration.

Mer information om Azure AD-åtkomst panelen finns i [introduktionen till åtkomst panelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Office 365-program start

För organisationer som har distribuerat Office 365 visas även program som är tilldelade till användare via Azure AD i Office 365-portalen på [https://portal.office.com/myapps](https://portal.office.com/myapps). Detta gör det enkelt och bekvämt för användare i en organisation att starta sina appar utan att behöva använda en andra Portal och är den rekommenderade app-lösningen för organisationer som använder Office 365.

![Skärm bild som visar Office 365-portalen](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Mer information om Office 365 Application starta finns i se till att [din app visas i Office 365-appens start](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Direkt inloggning till federerade appar

De flesta federerade program som har stöd för SAML 2,0, WS-Federation eller OpenID Connect stöder även möjligheten för användare att starta i programmet och sedan sedan bli inloggad via Azure AD, antingen genom automatisk omdirigering eller genom att klicka på en länk för att logga in. Detta kallas för inaktiverad inloggning av tjänst leverantör och de flesta federerade program i Azure AD-programgalleriet stöder detta (se dokumentationen som är länkad från appens konfigurations guide för enkel inloggning i Azure Portal för mer information).

![Exempel på en inloggnings sida för mobilapp](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Direkta inloggnings länkar

Azure AD stöder även länkar med direkt enkel inloggning till enskilda program som stöder lösenordsbaserad enkel inloggning, länkad enkel inloggning och alla former av federerad enkel inloggning.

Dessa länkar är särskilt utformade URL: er som skickar en användare via inloggnings processen för Azure AD för ett specifikt program utan att användaren behöver starta dem från Azure AD Access-panelen eller Office 365. Dessa **URL: er för användar åtkomst** kan hittas under egenskaperna för tillgängliga företags program. I Azure Portal väljer du **Azure Active Directory** > **företags program**. Välj programmet och välj sedan **Egenskaper**.

![Exempel på URL för användar åtkomst i Twitter-egenskaper](media/end-user-experiences/direct-sign-on-link.png)

Dessa länkar kan kopieras och klistras in överallt där du vill ange en inloggnings länk till det valda programmet. Detta kan finnas i ett e-postmeddelande eller på en anpassad webbaserad portal som du har ställt in för åtkomst till användar program. Här är ett exempel på en Azure AD Direct-inloggnings-URL för Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Precis som för organisatoriska URL: er för åtkomst panelen kan du ytterligare anpassa URL: en genom att lägga till en av de aktiva eller verifierade domänerna för katalogen efter myapps.microsoft.com-domänen. Detta säkerställer att all organisations anpassning läses in direkt på inloggnings sidan utan att användaren behöver ange sitt användar-ID först:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

När en behörig användare klickar på någon av dessa programspecifika länkar ser de först sin organisations inloggnings sida (förutsatt att de inte redan är inloggade) och när inloggningen omdirigeras till appen utan att först stoppa åtkomst panelen. Om användaren saknar nödvändiga förutsättningar för att komma åt programmet, t. ex. lösen ord för den lösen ordsbaserade tillägget för enkel inloggning, uppmanas användaren att installera det saknade tillägget. Länk-URL: en förblir också konstant om konfigurationen för enkel inloggning för programmet ändras.

Dessa länkar använder samma mekanismer för åtkomst kontroll som åtkomst panelen och Office 365 och endast de användare eller grupper som har tilldelats till programmet i Azure Portal kan autentiseras. Alla användare som inte har behörighet kommer dock att se ett meddelande som förklarar att de inte har beviljats åtkomst och att de får en länk till åtkomst panelen för att visa tillgängliga program som de har åtkomst till.

## <a name="next-steps"></a>Nästa steg

Information om distributions planer finns i [Azure Active Directory distributions planer](../fundamentals/active-directory-deployment-plans.md)
