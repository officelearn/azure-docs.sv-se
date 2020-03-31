---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187840"
---
1. Högerklicka på Windows Store-appprojektet i Visual Studio Solution Explorer. Välj sedan **Store** > **Associate App med Store**.

    ![Associera app med Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. Välj **Nästa**i guiden . Logga sedan in med ditt Microsoft-konto. I **Reservera ett nytt appnamn**skriver du ett namn på appen och väljer sedan **Reservera**.
3. När appregistreringen har skapats väljer du det nya appnamnet. Välj **Nästa**och välj sedan **Associera**. Den här processen lägger till den nödvändiga registreringsinformationen för Windows Store i programmanifestet.
4. Upprepa steg 1 och 3 för Appprojektet i Windows Phone Store med samma registrering som du tidigare skapade för Windows Store-appen.  
5. Gå till [Windows Dev Center](https://dev.windows.com/en-us/overview)och logga sedan in med ditt Microsoft-konto. Välj den nya appregistreringen i **Mina appar.** Expandera sedan**Push-meddelanden för** **tjänster** > .
6. På sidan **Push-meddelanden** under **WNS (Windows Push Notification Services) och Microsoft Azure Mobile Apps**väljer du webbplatsen Live **Services**.  Anteckna värdena för **Package SID** och det *aktuella* värdet i **Application Secret**. 

    ![Appinställning i utvecklarcentret](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Programhemligheten och paket-SID:et är viktiga säkerhetsuppgifter. Dela inte dessa värden med någon eller distribuera dem med din app.
   >
   >
