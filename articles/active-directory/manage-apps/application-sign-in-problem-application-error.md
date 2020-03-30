---
title: Felmeddelande visas på appsidan när du har loggat in | Microsoft-dokument
description: Så här löser du problem med Azure AD-inloggning när appen returnerar ett felmeddelande.
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
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185480"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>En appsida visar ett felmeddelande när användaren loggar in

I det här fallet Signerar Azure Active Directory (Azure AD) användaren i. Men programmet visar ett felmeddelande och låter inte användaren slutföra inloggningsflödet. Problemet är att appen inte accepterade svaret som Azure AD utfärdade.

Det finns flera möjliga orsaker till att appen inte accepterade svaret från Azure AD. Om felmeddelandet inte tydligt identifierar vad som saknas i svaret provar du följande:

-   Om appen är Azure AD-galleriet kontrollerar du att du har följt stegen i [Så här felsöker du SAML-baserad enkel inloggning till program i Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Använd ett verktyg som [Fiddler](https://www.telerik.com/fiddler) för att fånga SAML-begäran, svar och token.

-   Skicka SAML-svaret till appleverantören och fråga dem vad som saknas.

## <a name="attributes-are-missing-from-the-saml-response"></a>Attribut saknas i SAML-svaret

Så här lägger du till ett attribut i Azure AD-konfigurationen som ska skickas i Azure AD-svaret:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Högst upp i navigeringsfönstret till vänster väljer du **Alla tjänster** för att öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i sökrutan för filtret och välj sedan Azure Active **Directory**.

4. Välj **Företagsprogram** i navigeringsfönstret i Azure AD.

5. Välj **Alla program om** du vill visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filterkontrollen** högst upp i **listan Alla program**. Ange alternativet **Visa** till "Alla program".

6. Välj det program som du vill konfigurera för enkel inloggning.

7. När appen har läses in väljer du **Enkel inloggning** i navigeringsfönstret.

8. I avsnittet **Användarattribut** väljer du **Visa och redigerar alla andra användarattribut**. Här kan du ändra vilka attribut som ska skickas till appen i SAML-token när användare loggar in.

   Så här lägger du till ett attribut:

   1. Välj **Lägg till attribut**. Ange **namnet**och välj **värde** i listrutan.

   1.  Välj **Spara**. Du ser det nya attributet i tabellen.

9. Spara konfigurationen.

   Nästa gång användaren loggar in på appen skickar Azure AD det nya attributet i SAML-svaret.

## <a name="the-app-doesnt-identify-the-user"></a>Appen identifierar inte användaren

Det går inte att logga in på appen eftersom SAML-svaret saknar ett attribut, till exempel en roll. Eller så misslyckas det eftersom appen förväntar sig ett annat format eller värde för **attributet NameID** (User Identifier).

Om du använder [Azure AD-automatiserad användaretablering](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) för att skapa, underhålla och ta bort användare i appen kontrollerar du att användaren har etablerats i SaaS-appen. Mer information finns i [Inga användare etableras i ett Azure AD Gallery-program](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Lägga till ett attribut i Azure AD-appkonfigurationen

Så här ändrar du värdet för användaridentifieraren:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **Alla tjänster** högst upp i navigeringsfönstret till vänster om du vill öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i sökrutan för filtret och välj sedan Azure Active **Directory**.

4. Välj **Företagsprogram** i navigeringsfönstret i Azure AD.

5. Välj **Alla program om** du vill visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser den app du vill använda använder du **filterkontrollen** högst upp i **listan Alla program**. Ange alternativet **Visa** till "Alla program".

6. Välj den app som du vill konfigurera för SSO.

7. När appen har läses in väljer du **Enkel inloggning** i navigeringsfönstret.

8. Under **Användarattribut**väljer du den unika identifieraren för användaren i listrutan **Användaridentifierare.**

## <a name="change-the-nameid-format"></a>Ändra NameID-format

Om programmet förväntar sig ett annat format för attributet **NameID** (User Identifier) läser du [Redigera nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) för att ändra NameID-formatet.

Azure AD väljer formatet för **NameID-attributet** (User Identifier) baserat på det valda värdet eller det format som begärs av appen i SAML AuthRequest. Mer information finns i avsnittet "NameIDPolicy" [i SAML-protokollet För enkel inloggning](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Appen förväntar sig en annan signaturmetod för SAML-svaret

Så här ändrar du vilka delar av SAML-token som signeras digitalt av Azure AD:

1. Öppna [Azure-portalen](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **Alla tjänster** högst upp i navigeringsfönstret till vänster om du vill öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i sökrutan för filtret och välj sedan Azure Active **Directory**.

4. Välj **Företagsprogram** i navigeringsfönstret i Azure AD.

5. Välj **Alla program om** du vill visa en lista över dina appar.

   > [!NOTE]
   > Om du inte ser det program du vill använda använder du **filterkontrollen** högst upp i **listan Alla program**. Ange alternativet **Visa** till "Alla program".

6. Välj det program som du vill konfigurera för enkel inloggning.

7. När programmet har läses in väljer du **Enkel inloggning** i navigeringsfönstret.

8. Under **SAML-signeringscertifikat**väljer du **Visa avancerade certifikatsigneringsinställningar**.

9. Välj det **signeringsalternativ** som appen förväntar sig bland dessa alternativ:

   * **Signera SAML-svar**
   * **Signera SAML-svar och påstående**
   * **Underteckna SAML-påstående**

   Nästa gång användaren loggar in på appen signerar Azure AD den del av SAML-svaret som du har valt.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Appen förväntar sig att SHA-1-signeringsalgoritmen

Som standard signerar Azure AD SAML-token med hjälp av den säkraste algoritmen. Vi rekommenderar att du inte ändrar signeringsalgoritmen till *SHA-1* om inte appen kräver SHA-1.

Så här ändrar du signeringsalgoritmen:

1. Öppna [Azure-portalen](https://portal.azure.com/) och logga in som global administratör eller medadministratör.

2. Välj **Alla tjänster** högst upp i navigeringsfönstret till vänster om du vill öppna Azure AD-tillägget.

3. Skriv **Azure Active Directory** i sökrutan för filtret och välj sedan Azure Active **Directory**.

4. Välj **Företagsprogram** i navigeringsfönstret i Azure AD.

5. Välj **Alla program om** du vill visa en lista över dina program.

   > [!NOTE]
   > Om du inte ser det program du vill använda använder du **filterkontrollen** högst upp i **listan Alla program**. Ange alternativet **Visa** till "Alla program".

6. Välj den app som du vill konfigurera för enkel inloggning.

7. När appen har läses in väljer du **Enkel inloggning** från navigeringsfönstret till vänster i appen.

8. Under **SAML-signeringscertifikat**väljer du **Visa avancerade certifikatsigneringsinställningar**.

9. Välj **SHA-1** som **signeringsalgoritm**.

   Nästa gång användaren loggar in på appen signerar Azure AD SAML-token med hjälp av SHA-1-algoritmen.

## <a name="next-steps"></a>Nästa steg
[Felsöka SAML-baserad enkel inloggning till program i Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
