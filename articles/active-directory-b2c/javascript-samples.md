---
title: JavaScript-exempel – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du använder JavaScript i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 972c8ec1b67161a3998a1b165072f584db2f7fbb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570519"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>JavaScript-exempel för användning i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Du kan lägga till egna klientens JavaScript-kod till din Azure Active Directory (Azure AD) B2C-program. Om du vill aktivera JavaScript för dina program, måste du lägga till ett element till din [anpassad princip](active-directory-b2c-overview-custom.md)väljer en [sidan kontrakt](page-contract.md), och använda [b2clogin.com](b2clogin.md) i dina önskemål. Den här artikeln beskrivs hur du kan ändra den anpassade principen för att aktivera körning av skript.

> [!NOTE]
> Om du vill aktivera JavaScript för användarflöden [JavaScript och sidan kontrakt-versioner i Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Markera ett sida-kontrakt för användargränssnittets delar av ditt program. Om du tänker använda JavaScript måste du definiera en sida kontrakt-version för alla dina innehållsdefinitioner i en egen princip.

## <a name="add-the-scriptexecution-element"></a>Lägg till elementet ScriptExecution

Du aktiverar körning av skript genom att lägga till den **ScriptExecution** elementet så att den [RelyingParty](relyingparty.md) element.

1. Öppna filen anpassad princip. Till exempel *SignUpOrSignin.xml*.
2. Lägg till den **ScriptExecution** elementet så att den **UserJourneyBehaviors** element i **RelyingParty**:

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

## <a name="guidelines-for-using-javascript"></a>Riktlinjer för att använda JavaScript

Följ dessa riktlinjer när du anpassar gränssnittet för ditt program med hjälp av JavaScript:

- Inte binder en click-händelse i `<a>` HTML-element.
- Om du bara får du ett beroende på Azure AD B2C-kod eller kommentarer.
- Inte ändra ordningen eller hierarki med Azure AD B2C HTML-element. Använda en Azure AD B2C-princip för att styra ordningen för UI-element.
- Du kan anropa valfri RESTful-tjänst med detta:
    - Du kan behöva ange ditt RESTful-tjänst CORS så att klientsidan HTTP-anrop.
    - Kontrollera att dina RESTful-tjänst är säker och använder endast HTTPS-protokollet.
    - Använd inte JavaScript direkt för att anropa Azure AD B2C-slutpunkter.
- Du kan bädda in din JavaScript eller du kan länka till externa JavaScript-filer. När du använder en extern JavaScript-fil, se till att använda absolut URL och inte en relativ URL.
- JavaScript-ramverk:
    - Azure AD B2C använder en specifik version av jQuery. Omfattar inte en annan version av jQuery. Med hjälp av mer än en version på samma sida orsakar problem.
    - Med hjälp av RequireJS stöds inte.
    - De flesta JavaScript-ramverk stöds inte av Azure AD B2C.
- Azure AD B2C-inställningar som kan läsas genom att anropa `window.SETTINGS`, `window.CONTENT` objekt, till exempel det aktuella språket i Användargränssnittet. Ändra inte värdet för dessa objekt.
- Använda lokalisering i en princip för att anpassa felmeddelandet för Azure AD B2C.
- Om något kan uppnås med hjälp av en Grupprincip, är det vanligtvis det rekommenderade sättet.

## <a name="javascript-samples"></a>JavaScript-exempel

### <a name="show-or-hide-a-password"></a>Visa eller dölja ett lösenord

Ett vanligt sätt att hjälpa dina kunder med framgången registrering är så att de kan se vad de har angett som sitt lösenord. Det här alternativet hjälper användare att registrera dig genom att enkelt se och göra ändringar av sitt lösenord om det behövs. Ett fält av typen lösenord har en kryssruta med en **Show lösenord** etikett.  På så sätt kan användare för att visa lösenordet i klartext. Inkludera det här kodstycket i din registrering eller inloggning mall för en självkontrollerad sida:

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

Inkludera följande kod i din sida där du vill inkludera en **användningsvillkor** kryssrutan. Den här kryssrutan krävs normalt i lokalt konto registrering och sociala konto registrering sidorna.

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

I koden, Ersätt `termsOfUseUrl` med länken till din villkor för användning. Skapa ett nytt användarattribut som kallas för din katalog **termsOfUse** och inkludera **termsOfUse** som ett användarattribut.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan anpassa användargränssnittet i dina program i [anpassa användargränssnittet i ditt program med en anpassad princip i Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
