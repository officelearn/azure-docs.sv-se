---
title: JavaScript-exempel
titleSuffix: Azure AD B2C
description: Lär dig mer om hur du använder JavaScript i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a26f6c5e69ca083335580a0368459e062de3941e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187669"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-exempel för användning i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Du kan lägga till din egen JavaScript-klientkod i dina Azure Active Directory B2C-program (Azure AD B2C).

Så här aktiverar du JavaScript för dina program:

* Lägga till ett element i din [anpassade princip](custom-policy-overview.md)
* Välja en [sidlayout](page-layout.md)
* Använda [b2clogin.com](b2clogin.md) i dina begäranden

I den här artikeln beskrivs hur du kan ändra din anpassade princip för att aktivera skriptkörning.

> [!NOTE]
> Om du vill aktivera JavaScript för användarflöden läser du [JavaScript- och sidlayoutversioner i Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Krav

### <a name="select-a-page-layout"></a>Välja en sidlayout

* Välj en [sidlayout](contentdefinitions.md#select-a-page-layout) för elementen i användargränssnittet i programmet.

    Om du tänker använda JavaScript måste du definiera en `contract` [sidlayoutversion](contentdefinitions.md#migrating-to-page-layout) med sidversion för *alla* innehållsdefinitioner i din anpassade princip.

## <a name="add-the-scriptexecution-element"></a>Lägga till scriptexecution-elementet

Du aktiverar skriptkörning genom att lägga till **scriptexecution-elementet** i [elementet RelyingParty.](relyingparty.md)

1. Öppna din anpassade principfil. Registrera till exempel *Registrera DigOrSignin.xml*.
2. Lägg till **elementet ScriptExecution** i **elementet UserJourneyBehaviors** **i RelyingParty:**

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Spara och ladda upp filen.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>JavaScript-exempel

### <a name="show-or-hide-a-password"></a>Visa eller dölja ett lösenord

Ett vanligt sätt att hjälpa dina kunder med deras anmälningsframgång är att låta dem se vad de har angett som lösenord. Det här alternativet hjälper användarna att registrera sig genom att göra det möjligt för dem att enkelt se och göra korrigeringar av sitt lösenord om det behövs. Alla fält av typen lösenord har en kryssruta med etiketten **Visa lösenord.**  Detta gör det möjligt för användaren att se lösenordet i oformaterad text. Inkludera kodavsnittet i din registrerings- eller inloggningsmall för en sida som själv har bekräftats:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Lägg till användningsvillkor

Inkludera följande kod på sidan där du vill inkludera en **checklista för användningsvillkor.** Den här kryssrutan behövs vanligtvis på dina registreringssidor för lokala konton och registrering av sociala konton.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

Ersätt `termsOfUseUrl` med länken till ditt användarvillkorsavtal i koden. Skapa ett nytt användarattribut som kallas **termsOfUse** för din katalog och inkludera sedan **termsOfUse** som ett användarattribut.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan anpassa användargränssnittet för dina program i [Anpassa användargränssnittet i ditt program med hjälp av en anpassad princip i Azure Active Directory B2C](custom-policy-ui-customization.md).
