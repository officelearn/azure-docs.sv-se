---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 537c86512db4706077d0089a4f71ee945623c26b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081547"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>Skapa din meddelandehubb med APNS-information

Under **Notification Services**väljer du **Apple** och följer sedan lämpliga steg baserat på den metod som du valde tidigare i avsnittet [skapa ett certifikat för Notification Hubs](#creating-a-certificate-for-notification-hubs) .  

> [!NOTE]
> Använd endast **produktion** för **program läge** om du vill skicka push-meddelanden till användare som har köpt din app från Store.

### <a name="option-1-using-a-p12-push-certificate"></a>ALTERNATIV 1: använda ett. p12-Push-certifikat

1. Välj **Certifikat**.

1. Välj filikonen.

1. Välj den. P12-fil som du exporterade tidigare och välj sedan **Öppna**.

1. Ange rätt lösen ord om det behövs.

1. Välj **Sandbox**-läge.

1. Välj **Spara**.

### <a name="option-2-using-token-based-authentication"></a>ALTERNATIV 2: använda tokenbaserad autentisering

1. Välj **token**.
1. Ange följande värden som du har köpt tidigare:

    - **Nyckel-ID**
    - **Samlings-ID**
    - **Team-ID**
    - **Token**

1. Välj **sandbox**.
1. Välj **Spara**.
