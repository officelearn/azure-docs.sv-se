---
title: Felmeddelandet visas på appsidan när du loggar in | Microsoft Docs
description: Hur du löser problem med Azure AD-inloggningen när programmet returnerar ett felmeddelande.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41ec1f510b028a2ffe2554bfcbd77bc439c4e79
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272962"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>En app-sida visas ett felmeddelande när användaren loggar in

I det här scenariot loggar användaren in i Azure Active Directory (AD Azure). Men programmet visas ett felmeddelande och kan inte användaren Slutför inloggningen flödet. Problemet är att appen inte accepterar ett svar som utfärdats av Azure AD.

Det finns flera möjliga orsaker till varför appen inte godkände svar från Azure AD. Om felmeddelandet inte tydligt identifiera vad som saknas från svaret, provar du följande:

-   Om appen är Azure AD-galleriet, kontrollerar du att du har följt stegen i [Felsök SAML-baserad enkel inloggning till program i Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Använd ett verktyg som [Fiddler](https://www.telerik.com/fiddler) avbilda SAML-begäran, svar och token.

-   Skicka SAML-svar till appleverantören och be dem om vad som saknas.

## <a name="attributes-are-missing-from-the-saml-response"></a>Attribut saknas från SAML-svar

Följ dessa steg för att lägga till ett attribut i Azure AD-konfiguration som ska skickas i Azure AD-svaret:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en global administratör eller medadministratör.

2. Överst i navigeringsfönstret till vänster väljer **alla tjänster** att öppna Azure AD-tillägget.

3. Typ **Azure Active Directory** i filtret sökrutan och välj sedan **Azure Active Directory**.

4. Välj **företagsprogram** i navigeringsfönstret för Azure AD.

5. Välj **alla program** att visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app som du vill använda den **Filter** kontroll högst upp på den **listan över alla program**. Ange den **visa** alternativet ”alla program”.

6. Välj det program som du vill konfigurera för enkel inloggning.

7. När appen har lästs in väljer **enkel inloggning** i navigeringsfönstret.

8. I den **användarattribut** väljer **visa och redigera alla andra användarattribut**. Här kan du ändra vilka attribut som ska skicka till appen i SAML-token när användare loggar in.

   Lägga till ett attribut:

   1. Välj **Lägg till attribut**. Ange den **namn**, och välj den **värdet** från den nedrullningsbara listan.

   1.  Välj **Spara**. Du ser det nya attributet i tabellen.

9. Spara konfigurationen.

   Nästa gång användaren loggar in till appen, skickar Azure AD det nya attributet i SAML-svar.

## <a name="the-app-doesnt-identify-the-user"></a>Appen inte kan hitta användaren

Det går inte att logga in på appen eftersom SAML-svaret saknar ett attribut, till exempel en roll. Eller det misslyckas eftersom appen förväntar sig ett annat format eller värde för den **NameID** (användaridentifierare) attributet.

Om du använder [Azure AD-automatisk användaretablering](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) för att skapa, underhålla, och ta bort användare i appen, kontrollera att användaren har etablerats till SaaS-appen. Mer information finns i [inga användare etableras till en Azure AD-galleriprogram](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Lägga till ett attribut i Azure AD app-konfiguration

Följ dessa steg om du vill ändra värdet för användar-ID:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en global administratör eller medadministratör.

2. Välj **alla tjänster** överst i navigeringsfönstret till vänster för att öppna Azure AD-tillägget.

3. Typ **Azure Active Directory** i filtret sökrutan och välj sedan **Azure Active Directory**.

4. Välj **företagsprogram** i navigeringsfönstret för Azure AD.

5. Välj **alla program** att visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app som du vill använda den **Filter** kontroll högst upp på den **listan över alla program**. Ange den **visa** alternativet ”alla program”.

6. Välj den app som du vill konfigurera för enkel inloggning.

7. När appen har lästs in väljer **enkel inloggning** i navigeringsfönstret.

8. Under **användarattribut**, Välj den unika identifieraren för användaren från den **användaridentifierare** listrutan.

## <a name="change-the-nameid-format"></a>Ändra NameID-format

Om programmet förväntar sig ett annat format för den **NameID** (användaridentifierare) attributet finns i [redigering nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization.md#editing-nameid) ändra NameID-format.

Azure AD väljer formatet för den **NameID** attribut (användaridentifierare) baserat på det värde som har valts eller formatet som begärs av appen i SAML-AuthRequest. Mer information finns i avsnittet ”NameIDPolicy” i [enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Appen förväntar sig en annan signatur-metod för SAML-svar

Följ dessa steg om du vill ändra vilka delar av SAML-token signeras digitalt av Azure AD:

1. Öppna den [Azure-portalen](https://portal.azure.com/) och logga in som en global administratör eller medadministratör.

2. Välj **alla tjänster** överst i navigeringsfönstret till vänster för att öppna Azure AD-tillägget.

3. Typ **Azure Active Directory** i filtret sökrutan och välj sedan **Azure Active Directory**.

4. Välj **företagsprogram** i navigeringsfönstret för Azure AD.

5. Välj **alla program** att visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser det program som du vill använda den **Filter** kontroll högst upp på den **listan över alla program**. Ange den **visa** alternativet ”alla program”.

6. Välj det program som du vill konfigurera för enkel inloggning.

7. När programmet har lästs in väljer **enkel inloggning** i navigeringsfönstret.

8. Under **SAML-signeringscertifikat**väljer **visa avancerade inställningar för signering av certifikat**.

9. Välj den **signering alternativet** som appen förväntar sig bland dessa alternativ:

   * **Signera SAML-svar**
   * **Signera SAML-svar och försäkran**
   * **Signera SAML-försäkran**

   Nästa gång användaren loggar in till appen, signerar Azure AD en del av SAML-svar som du har valt.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Appen förväntas Signeringsalgoritm för SHA-1

Som standard signerar Azure AD SAML-token med hjälp av den mest säkra algoritmen. Vi rekommenderar att du inte ändrar Signeringsalgoritm till *SHA-1* om inte appen kräver SHA-1.

Följ dessa steg om du vill ändra signeringsalgoritmen som:

1. Öppna den [Azure-portalen](https://portal.azure.com/) och logga in som en global administratör eller medadministratör.

2. Välj **alla tjänster** överst i navigeringsfönstret till vänster för att öppna Azure AD-tillägget.

3. Typ **Azure Active Directory** i filtret sökrutan och välj sedan **Azure Active Directory**.

4. Välj **företagsprogram** i navigeringsfönstret för Azure AD.

5. Välj **alla program** att visa en lista över dina program.

   > [!NOTE]
   > Om du inte ser det program som du vill använda den **Filter** kontroll högst upp på den **listan över alla program**. Ange den **visa** alternativet ”alla program”.

6. Välj den app som du vill konfigurera för enkel inloggning.

7. När appen har lästs in väljer **enkel inloggning** i navigeringsfönstret till vänster i appen.

8. Under **SAML-signeringscertifikat**väljer **visa avancerade inställningar för signering av certifikat**.

9. Välj **SHA-1** som den **Signeringsalgoritm**.

   Nästa gång användaren loggar in till appen, signerar Azure AD SAML-token med hjälp av algoritmen SHA-1.

## <a name="next-steps"></a>Nästa steg
[Så här felsöker du SAML-baserad enkel inloggning till program i Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
