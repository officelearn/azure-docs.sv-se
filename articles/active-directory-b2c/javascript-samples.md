---
title: JavaScript-exempel
titleSuffix: Azure AD B2C
description: Lär dig hur du använder JavaScript i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1381ddb16697b1e892794604bbfafda815bd6182
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149089"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-exempel för användning i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Du kan lägga till din egen kod för Java Script på klient sidan i Azure Active Directory B2C (Azure AD B2C)-program.

Så här aktiverar du Java Script för dina program:

* Lägg till ett element i din [anpassade princip](custom-policy-overview.md)
* Välj en [Sidlayout](page-layout.md)
* Använd [b2clogin.com](b2clogin.md) i dina begär Anden

I den här artikeln beskrivs hur du kan ändra den anpassade principen för att aktivera skript körning.

> [!NOTE]
> Om du vill aktivera Java Script för användar flöden, se [Java Script och versioner av sidlayout i Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="select-a-page-layout"></a>Välj en sidlayout

* Välj en [Sidlayout](contentdefinitions.md#select-a-page-layout) för användar gränssnitts elementen i programmet.

    Om du tänker använda Java Script måste du [definiera en version](contentdefinitions.md#migrating-to-page-layout) av sidlayouten med Page `contract` version för *alla* innehålls definitioner i den anpassade principen.

## <a name="add-the-scriptexecution-element"></a>Lägg till elementet ScriptExecution

Du aktiverar skript körning genom att lägga till **ScriptExecution** -elementet i [RelyingParty](relyingparty.md) -elementet.

1. Öppna filen anpassad princip. Till exempel *SignUpOrSignin. XML*.
2. Lägg till **ScriptExecution** -elementet i **UserJourneyBehaviors** -elementet för **RelyingParty**:

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

Ett vanligt sätt att hjälpa dina kunder med framgången registrering är så att de kan se vad de har angett som sitt lösenord. Det här alternativet hjälper användare att registrera dig genom att enkelt se och göra ändringar av sitt lösenord om det behövs. Alla fält av typen lösen ord har en kryss ruta med etiketten **Visa lösen ord** .  På så sätt kan användare för att visa lösenordet i klartext. Inkludera det här kodstycket i din registrering eller inloggning mall för en självkontrollerad sida:

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

### <a name="add-terms-of-use"></a>Lägga till användningsvillkor

Inkludera följande kod på sidan där du vill inkludera en **användnings villkors** kryss ruta. Den här kryssrutan krävs normalt i lokalt konto registrering och sociala konto registrering sidorna.

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

I koden ersätter du `termsOfUseUrl` med länken till dina användnings villkor. Skapa ett nytt användarattribut med namnet **termsofuse** i din katalog och ta sedan med **termsofuse** som användar-attribut.

## <a name="next-steps"></a>Nästa steg

Hitta mer information om hur du kan anpassa användar gränssnittet för dina program i [Anpassa användar gränssnittet för ditt program med hjälp av en anpassad princip i Azure Active Directory B2C](custom-policy-ui-customization.md).
