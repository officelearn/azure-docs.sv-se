---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240236"
---
1. Navigera till lösningsfilen i klientprojektet (.sln) och öppna den med hjälp av Visual Studio.

2. I Visual Studio väljer du lösningsplattform (Android, iOS eller Windows) från listrutan bredvid startpilen. Välj en viss distributionsenhet eller emulator genom att klicka på den gröna pilen i listrutan. Du kan använda Android-plattformen och Ripple-emulatorn av standardtyp. För mer avancerade självstudier (t.ex, push-meddelanden) krävs att du väljer en enhet eller emulator som stöds.

3. Öppna filen `ToDoActivity.java` i den här mappen - ZUMOAPPNAME/app/src/main/java/com/exempel/zumoappname. Programnamnet är `ZUMOAPPNAME`.

4. Gå till den [Azure-portalen](https://portal.azure.com/) och navigera till den mobila appen som du skapade. På den `Overview` bladet letar du reda på URL: en som är den offentliga slutpunkten för din mobilapp. Exempel – sitename för min app name ”test123” kommer att https://test123.azurewebsites.net.

5. Gå till den `index.js` filen i ZUMOAPPNAME/www/js/index.js och i `onDeviceReady()` metod, Ersätt `ZUMOAPPURL` parameter med offentlig slutpunkt ovan.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    blir
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Tryck på F5 eller klicka på den gröna pilen för att skapa och köra din Cordova-app. Om du ser en säkerhetsdialogruta i emulatorn som begär åtkomst till nätverket, accepterar du begäran.

7. När appen har startats på enheten eller emulatorn, ange en beskrivande text i **ange ny text**, till exempel *i självstudiekursen* och klicka sedan på den **Lägg till** knappen.

Serverdelen infogar data från begäran i TodoItem-tabellen i SQL Database och returnerar information om det nyligen lagrade objekten tillbaka till mobilappen. Mobilappen visar dessa data i listan.

Du kan upprepa steg 3 till 5 för andra plattformar.