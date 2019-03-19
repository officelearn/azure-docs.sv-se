---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: f4ba467b6d80c9ccafba0a91c1f04152b92cf869
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125020"
---
1. Besök [Azure Portal] på din Mac. Klicka på **Alla tjänster** > **App Services** > serverdelen du precis har skapat. I mobilappens inställningar väljer du det språk du vill använda:

   - Objective-C &ndash; **Snabbstart** > **iOS (Objective-C)**
   - Swift &ndash; **Snabbstart** > **iOS (Swift)**

     Under **3. Konfigurera klientprogrammet** klickar du på **Ladda ned**. Ett fullständigt Xcode-projekt som förkonfigurerats att ansluta till din serverdel hämtas. Öppna projektet med Xcode.

1. Klicka på **Kör**-knappen för att skapa projektet och starta appen i iOS-simulatorn.

1. I appen klickar du på plusikonen (**+**), anger en beskrivande text, till exempel *Slutför självstudien*, och klickar sedan på knappen Spara. Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan.

   ![Snabbstart för appar som körs i iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
