---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 5ef4fb2059710fcd5f05ea17edbbf00472ae0a6f
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762619"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- En distribuerad kommunikations tjänst resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- Ett telefonnummer som anskaffats i kommunikations tjänst resursen. [så här hämtar du ett telefonnummer](../../telephony-sms/get-phone-number.md).
- A `User Access Token` för att aktivera anrops klienten. Mer information om [hur du hämtar en `User Access Token` ](../../access-tokens.md)
- Slutför snabb starten för att [komma igång med att lägga till samtal till ditt program](../getting-started-with-calling.md)

### <a name="prerequisite-check"></a>Krav kontroll

- Om du vill visa de telefonnummer som är associerade med kommunikations tjänst resursen loggar du in på [Azure Portal](https://portal.azure.com/), letar upp resursen för kommunikations tjänster och öppnar fliken **telefonnummer** i det vänstra navigerings fönstret.
- Du kan skapa och köra din app med Azure Communication Services som anropar klient bibliotek för Java Script:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js
```

## <a name="setting-up"></a>Konfigurera

### <a name="add-pstn-functionality-your-app"></a>Lägg till PSTN-funktionalitet i appen

Utöka layouten med telefon uppringnings kontroller.

Placera den här koden i slutet av `<body />` avsnittet i **index.html**, före `<script />` Taggar:

```html
<input 
  id="callee-phone-input"
  type="text"
  placeholder="Phone number you would like to dial"
  style="margin-bottom:1em; width: 230px;"
/>
<div>
  <button id="call-phone-button" type="button">
    Start Phone Call
  </button>
  &nbsp;
  <button id="hang-up-phone-button" type="button" disabled="true">
    Hang Up Phone Call
  </button>
</div>
```

Utöka program logiken med telefoni funktioner.

Lägg till den här koden i **client.js**:

```javascript
const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services som anropar klient bibliotek för Java Script.

| Namn                                           | Beskrivning                                                                                          |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| PhoneNumber | Den här klassen krävs för att initiera ett telefonnummer som du vill använda för telefoni funktioner. |

## <a name="start-a-call-to-phone"></a>Starta ett samtal till telefon

Ange telefonnummer som du har köpt i kommunikations tjänst resursen, som ska användas för att starta anropet:
> [!WARNING]
> Observera att telefonnummer måste anges i formatet E. 164 internationellt standard. (t. ex.: + 12223334444)

Lägg till en händelse hanterare för att initiera ett anrop till det telefonnummer som du angav när du `callPhoneButton` klickar på:


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.call(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: '+18336528005'}
  });

  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>Avsluta ett samtal till telefon

Lägg till en händelse lyssnare för att avsluta det aktuella anropet när `hangUpPhoneButton` användaren klickar på den:

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

`forEveryone`Egenskapen avslutar anropet för alla anrops deltagare.

## <a name="run-the-code"></a>Kör koden

Använd `webpack-dev-server` för att skapa och köra din app. Kör följande kommando för att paketera program värden på en lokal webserver:


```console
npx webpack-dev-server --entry ./client.js --output bundle.js
```

Öppna webbläsaren och gå till `http://localhost:8080/` . Du bör se följande:


![Skärm bild av det färdiga JavaScript-programmet.](../media/javascript/pstn-calling-javascript-app.png)

Du kan ringa ett samtal till ett reellt telefonnummer genom att ange ett telefonnummer i fältet tillagd text och klicka på knappen **Starta telefonsamtal** .

> [!WARNING]
> Observera att telefonnummer måste anges i formatet E. 164 internationellt standard. (t. ex.: + 12223334444)
