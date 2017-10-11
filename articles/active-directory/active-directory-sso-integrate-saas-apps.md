---
title: Integrera Azure Active Directory enkel inloggning med SaaS-appar | Microsoft Docs
description: "Aktivera enkel inloggning, autentisering och centraliserad hantering av SaaS-appar i Azure Active Directory för användaretablering. En översikt över hur du integrerar Azure Active Directory till SaaS-appar."
services: active-directory
keywords: integrera Azure AD med SaaS-appar
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 53b9d341-d1fc-4bbb-ac7c-3f4c68fcf00a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: aaronsm
ms.openlocfilehash: fc0d297598c334ca8f6f8a2bd3ae948c87956342
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Integrera Azure Active Directory enkel inloggning med SaaS-appar
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-enterprise-apps-manage-sso.md)
> * [Klassisk Azure-portal](active-directory-sso-integrate-saas-apps.md)
>
>

[!INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Om du vill komma igång konfigurerar enkel inloggning för en app som du tillämpar i din organisation, kommer du att använda en befintlig katalog i Azure Active Directory (AD Azure). Du kan använda en Azure AD-katalog som du har fått via Microsoft Azure, Office 365 eller Windows Intune. Om du har två eller flera av dessa, se [administrera Azure AD-katalogen](active-directory-administer.md) att avgöra vilket som ska användas.

> [!IMPORTANT]
> Microsoft rekommenderar att du hanterar Azure AD via [Azure AD administratörscenter](https://aad.portal.azure.com) på Azure Portal istället för via den klassiska Azure-portalen som nämns i den här artikeln. Att tilldela administratörsroller i Azure AD-administrationscentret, se [Tilldela administratörsroller i Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).

## <a name="authentication"></a>Autentisering
För program som stöder WS-Federation, SAML 2.0 eller OpenID Connect protokoll, Azure Active Directory använder signeringscertifikatet du etablerar förtroenderelationer. Mer information om detta finns [hantera certifikat för federerad enkel inloggning](active-directory-sso-certs.md).

För program som stöder endast HTML-formulär för inloggning i Azure Active Directory använder 'lösenordsvalv' upprätta betrodda relationer. Detta gör att användare i din organisation kan loggas in automatiskt till ett SaaS-program med Azure AD med hjälp av informationen om användarkontot från SaaS-program. Azure AD samlar in och lagrar informationen om användarkontot och relaterade lösenordet på ett säkert sätt. Mer information finns i [lösenordsbaserade enkel inloggning](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## <a name="authorization"></a>Auktorisering
Ett etablerade konto används att ha behörighet att använda ett program när de har autentiserats via enkel inloggning. Användaretablering kan göras manuellt eller i vissa fall kan du lägga till och ta bort användarinformation från SaaS appen baserat på ändringar som gjorts i Azure Active Directory. Läs mer om hur du använder befintliga Azure AD-kopplingar för automatiserad etablering av [automatisk användaretablering och avetablering för SaaS-program](active-directory-saas-app-provisioning.md).

Annars kan kan du manuellt lägga till användare i en app eller använda andra etablering lösningar som är tillgängliga på marknaden.

## <a name="access"></a>Åtkomst
Azure AD innehåller flera anpassningsbara sätt att distribuera program till användare i din organisation. Du är inte låst i en viss distribution eller en lösning för åtkomst. Du kan använda [den lösning som bäst passar dina behov](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Ytterligare överväganden för program som redan används
Konfigurera enkel inloggning på för ett program som använder din organisation redan är en annan process från processen att skapa nya konton för ett nytt program. Det finns några förberedande steg, inklusive: mappning av användaridentiteter i programmet till Azure AD identiteter och förstå hur användare får logga in på ett program när det har integrerats.

> [!NOTE]
> Om du vill konfigurera enkel inloggning för ett befintligt program måste du ha behörighet som global administratör i både Azure AD och SaaS-program.
>
>

### <a name="mapping-user-accounts"></a>Mappning av användarkonton
Användarens identitet har oftast en unik identifierare som kan vara en e-postadress eller användarens huvudnamn (UPN). Behöver du länken (mappar) programidentitet för varje användare till deras respektive Azure AD-identitet. Det finns ett par olika sätt att åstadkomma detta beroende på hur krav på program-autentisering.

Mer information om hur du mappar programmet identiteter med Azure AD-identiteter finns [anpassa anspråk som utfärdats i SAML-token](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) och [anpassa attributmappning för att etablera](active-directory-saas-customizing-attribute-mappings.md).

### <a name="understanding-the-users-log-in-experience"></a>Förstå användarens inloggning
När du integrerar SSO för ett program som redan används, är det viktigt att förstå att användarupplevelsen påverkas. För alla program startar användare med sina Azure AD-autentiseringsuppgifter för inloggning. Det kan också bero på att de måste använda en annan portal åt programmen.

Enkel inloggning för vissa program kan göras på programmets tecken i gränssnittet, men för andra program användaren måste gå via en central portal som ([Mina appar](http://myapps.microsoft.com) eller [Office365](http://portal.office.com/myapps)) att logga in. Mer information om de olika typerna av enkel inloggning och deras användarupplevelser i [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

En annan viktig resurs är *utelämna användargodkännande* i den [Guiding utvecklare](active-directory-applications-guiding-developers-for-lob-applications.md) artikel.

## <a name="next-steps"></a>Nästa steg
För SaaS-appar som du hittar i Appgalleriet för Azure AD innehåller ett antal [självstudier om hur man integrerar SaaS-appar](active-directory-saas-tutorial-list.md).

Om appen inte är i App-galleriet, kan du [lägga till den i Appgalleriet för Azure AD som ett anpassat program](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Det finns mycket mer information om samtliga av dessa fel i biblioteket Azure.com från och med [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

Dessutom kan du inte missar i [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md).
