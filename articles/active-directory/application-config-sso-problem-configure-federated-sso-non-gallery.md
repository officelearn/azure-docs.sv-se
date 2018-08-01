---
title: Problem med att konfigurera federerad enkel inloggning för en icke-galleriprogram | Microsoft Docs
description: Åtgärda några av de vanliga problem som kan uppstå när du konfigurerar federerad enkel inloggning till ditt anpassade SAML-program som inte finns med i Azure AD-Programgalleriet
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: ce7c224075b2cd99eddcc5f57b5971e7e43d9b69
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366662"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problem med att konfigurera federerad enkel inloggning för en icke-galleriprogram

Om det uppstår ett problem när du konfigurerar ett program. Kontrollera att du har följt alla steg i artikeln [Konfigurera enkel inloggning till program som inte ingår i Azure Active Directory-programgalleriet.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Det går inte att lägga till en annan instans av programmet

Om du vill lägga till en andra instans av ett program, måste du kunna:

-   Konfigurera en unik identifierare för den andra instansen. Du kan inte konfigurera samma identifieraren som används för den första instansen.

-   Konfigurera ett annat certifikat än den som används för den första instansen.

Om programmet inte stöder något av föregående kan konfigurera du inte en andra instans.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Där anger formatet EntityID (användaridentifierare)

Du kan inte välja det EntityID (användaridentifierare)-format som Azure AD skickar till programmet i svaret efter autentisering av användare.

Azure AD väljer formatet för NameID-attributet (användaridentifierare) baserat på värdet som valts eller formatet som begärs av programmet i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Var hittar jag programmetadata eller certifikat från Azure AD

Följ dessa steg för att ladda ned programmetadata eller certifikat från Azure AD:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Gå till **SAML-signeringscertifikat** och sedan klicka på **hämta** kolumnvärde. Beroende på vilka programmet kräver att du konfigurerar enkel inloggning, se du antingen välja att hämta Metadata XML eller certifikatet.

Azure AD tillhandahåller inte en URL för att hämta metadata. Metadata kan endast hämtas som en XML-fil.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Vet inte hur du anpassar SAML-anspråk som skickas till ett program

Läs hur du anpassar SAML attributet anspråk som skickas till programmet i [Anspråksmappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](manage-apps/what-is-application-management.md)
