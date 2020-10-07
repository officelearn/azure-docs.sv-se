---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/14/2020
ms.author: nikuklic
ms.openlocfilehash: 5c20543caf5bf623738996ed01064d71a0745c04
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779473"
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

## <a name="setting-up"></a>Konfigurera

### <a name="add-pstn-functionality-to-your-app"></a>Lägg till PSTN-funktion i din app

Lägg till `PhoneNumber` typen i din app genom att ändra **MainActivity. java**:


```java
import com.azure.android.communication.common.PhoneNumber;
```

<!--
> [!TBD]
> Namespace based on input from Komivi Agbakpem. But it does not correlates with other use namespaces in Calling Quickstart. E.g: "com.azure.communication.calling.CommunicationUser" or "com.azure.communication.common.client.CommunicationUserCredential". Double-chek this.
-->

## <a name="start-a-call-to-phone"></a>Starta ett samtal till telefon

Ange det telefonnummer som du har skaffat i kommunikations tjänst resursen. Detta kommer att användas för att starta anropet:

> [!WARNING]
> Observera att telefonnummer måste anges i formatet E. 164 internationellt standard. (t. ex.: + 12223334444)

Ändra `startCall()` händelse hanterare i **MainActivity. java**så att den hanterar telefonsamtal:

```java
    private void startCall() {
        EditText calleePhoneView = findViewById(R.id.callee_id);
        String calleePhone = calleePhoneView.getText().toString();
        PhoneNumber callerPhone = new PhoneNumber("+12223334444");
        StartCallOptions options = new StartCallOptions();
        options.setAlternateCallerId(callerPhone);
        options.setVideoOptions(new VideoOptions(null));
        call = agent.call(
                getApplicationContext(),
                new PhoneNumber[] {new PhoneNumber(calleePhone)},
                options);
    }
```

## <a name="launch-the-app-and-call-the-echo-bot"></a>Starta appen och anropa ekot-roboten

Nu kan du starta appen med hjälp av knappen "kör app" i verktygsfältet (Shift + F10). Du kan ringa ett samtal till telefon genom att ange ett telefonnummer i fältet tillagd text och klicka på knappen **Ring** .
> [!WARNING]
> Observera att telefonnummer måste anges i formatet E. 164 internationellt standard. (t. ex.: + 12223334444)

![Skärm bild som visar det färdiga programmet.](../media/android/quickstart-android-call-pstn.png)
