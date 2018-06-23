---
title: Problem som konfigurerar federerad enkel inloggning för ett icke-galleriet program | Microsoft Docs
description: Vissa av de vanliga problem som kan uppstå när du konfigurerar federerad enkel inloggning till din anpassade SAML-program som inte finns med i Azure AD Application Gallery
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 36262320a5a8457b22cbe9fe9d902fda26b6609c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331112"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Konfigurera federerad enkel inloggning för ett icke-galleriet program problem

Om du stöter på problem när du konfigurerar ett program. Kontrollera att du har följt alla steg i artikeln [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Det går inte att lägga till en annan instans av programmet

Om du vill lägga till en andra instans av ett program, måste du kunna:

-   Konfigurera en unik identifierare för den andra instansen. Du kan inte konfigurera den samma identifierare som används för den första instansen.

-   Konfigurera ett annat certifikat än den som används för den första instansen.

Om programmet inte stöder något av föregående, kan du konfigurera en andra instans.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Där anger formatet ID för entiteterna (användar-ID)

Du kan inte välja det ID för entiteterna (användar-ID)-format som Azure AD skickar till programmet i svaret efter autentisering av användare.

Azure AD väljer format för attributet NameID (användar-ID) baserat på det valda värdet eller det format som begärs av programmet i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Var hittar jag programmetadata eller certifikat från Azure AD

Följ dessa steg för att ladda ned programmetadata eller certifikat från Azure AD:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  Gå till **SAML-signeringscertifikat** avsnittet och klicka sedan på **hämta** värde i kolumnen. Beroende på vilka programmet kräver att konfigurera enkel inloggning, finns antingen alternativet för att hämta Metadata XML eller certifikatet.

Azure AD Ange inte en URL för att hämta metadata. Metadata kan endast hämtas som en XML-fil.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Vet inte hur du anpassar SAML anspråk skickas till ett program

Information om hur du anpassar SAML attributet anspråk som skickas till ditt program finns [anspråk mappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](manage-apps/what-is-application-management.md)
