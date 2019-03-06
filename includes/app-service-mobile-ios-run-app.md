---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 8146489a913ce863cee7534331231a248a3ea7ac
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890944"
---
1. Besök [Azure Portal] på din Mac. Klicka på **Alla tjänster** > **App Services** > serverdelen du precis har skapat. I mobilappens inställningar väljer du det språk du vill använda:

    - Objective-C &ndash; **Snabbstart** > **iOS (Objective-C)**
    - Swift &ndash; **Snabbstart** > **iOS (Swift)**

    Under **3. Konfigurera klientprogrammet** klickar du på **Ladda ned**. Ett fullständigt Xcode-projekt som förkonfigurerats att ansluta till din serverdel hämtas. Öppna projektet med Xcode.

1. Klicka på **Kör**-knappen för att skapa projektet och starta appen i iOS-simulatorn.

1. I appen klickar du på plusikonen (**+**), anger en beskrivande text, till exempel *Slutför självstudien*, och klickar sedan på knappen Spara. Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan.

   ![Snabbstart för appar som körs i iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
