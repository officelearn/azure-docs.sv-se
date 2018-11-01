---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 1d1d593b7305e0cd9899f4ec388cb441ced90b10
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133801"
---
1. Besök [Azure Portal] på din Mac. Klicka på **Alla tjänster** > **App Services** > serverdelen du precis har skapat. I mobilappens inställningar väljer du det språk du vill använda:

    - Objective-C &ndash; **Snabbstart** > **iOS (Objective-C)**
    - Swift &ndash; **Snabbstart** > **iOS (Swift)**

    Under **3. Konfigurera klientprogrammet** klickar du på **Ladda ned**. Ett fullständigt Xcode-projekt som förkonfigurerats att ansluta till din serverdel hämtas. Öppna projektet med Xcode.

1. Klicka på **Kör**-knappen för att skapa projektet och starta appen i iOS-simulatorn.

1. Ange en beskrivande text i appen, till exempel *Slutföra kursen*, och klicka sedan på plustecknet (**+**). Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan.

   ![Snabbstart för appar som körs i iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
