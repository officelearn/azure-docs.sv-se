---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 505eac0996129a17b6b68e8ab4ea2d4fc80fd473
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134399"
---
1. Besök [Azure Portal]. Klicka på **Bläddra alla** > **Mobile Apps** > serverdelen som du nyss skapade. I inställningarna för mobilappar klickar du på **Snabbstart** > **Android**. Under **Konfigurera klientprogrammet** klickar du på **Hämta**. Då hämtas ett fullständigt Android-projekt för en app som är förkonfigurerad att ansluta till din serverdel. 
2. Öppna projektet med **Android Studio** med hjälp av **Importera projekt (Eclipse ADT, Gradle, osv.)**. Kontrollera att du har gjort det här importvalet för att undvika eventuella JDK-fel.
3. Tryck på knappen **Kör app** för att skapa projektet och starta appen i Android-simulatorn.
4. Ange en beskrivande text i appen, till exempel *Slutför guiden*, och klicka sedan på Lägg till-knappen. Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan. 
   
    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Azure Portal]: https://portal.azure.com/
