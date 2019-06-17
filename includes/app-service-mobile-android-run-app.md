---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240324"
---
1. Öppna projektet med **Android Studio** med hjälp av **Importera projekt (Eclipse ADT, Gradle, osv.)** . Kontrollera att du har gjort det här importvalet för att undvika eventuella JDK-fel.

2. Öppna filen `ToDoActivity.java` i den här mappen - ZUMOAPPNAME/app/src/main/java/com/exempel/zumoappname. Programnamnet är `ZUMOAPPNAME`.

3. Gå till den [Azure-portalen](https://portal.azure.com/) och navigera till den mobila appen som du skapade. På den `Overview` bladet letar du reda på URL: en som är den offentliga slutpunkten för din mobilapp. Exempel – sitename för min app name ”test123” kommer att https://test123.azurewebsites.net.

4. I `onCreate()` metod, Ersätt `ZUMOAPPURL` parameter med offentlig slutpunkt ovan.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    blir
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Tryck på knappen **Kör app** för att skapa projektet och starta appen i Android-simulatorn.

4. Ange en beskrivande text i appen, till exempel *Slutför guiden*, och klicka sedan på Lägg till-knappen. Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan.
    ![Snabbstart för Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)