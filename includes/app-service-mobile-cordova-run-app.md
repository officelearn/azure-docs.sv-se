---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240236"
---
1. Navigera till lösningsfilen i klientprojektet (.sln) och öppna den med Visual Studio.

2. I Visual Studio väljer du lösningsplattform (Android, iOS eller Windows) från listrutan bredvid startpilen. Välj en viss distributionsenhet eller emulator genom att klicka på den gröna pilen i listrutan. Du kan använda Android-plattformen och Ripple-emulatorn av standardtyp. För mer avancerade självstudier (t.ex, push-meddelanden) krävs att du väljer en enhet eller emulator som stöds.

3. Öppna filen `ToDoActivity.java` i den här mappen - ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. Programnamnet är `ZUMOAPPNAME`.

4. Gå till [Azure-portalen](https://portal.azure.com/) och navigera till mobilappen som du skapade. Leta `Overview` efter webbadressen som är den offentliga slutpunkten för mobilappen på bladet. Exempel - webbplatsnamnet för mitt appnamn "test123" kommer att vara https://test123.azurewebsites.net.

5. Gå till `index.js` filen i ZUMOAPPNAME/www/js/index.js `onDeviceReady()` och `ZUMOAPPURL` ersätt parametern med den offentliga slutpunkten ovan.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    Blir
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Tryck på F5 eller klicka på den gröna pilen för att skapa och köra din Cordova-app. Om du ser en säkerhetsdialogruta i emulatorn som begär åtkomst till nätverket, accepterar du begäran.

7. När appen har startats på enheten eller emulatorn skriver du meningsfull text i **Retur ny text**, till exempel Slutför *självstudien* och klickar sedan på knappen **Lägg till.**

Serverdelen infogar data från begäran i TodoItem-tabellen i SQL Database och returnerar information om det nyligen lagrade objekten tillbaka till mobilappen. Mobilappen visar dessa data i listan.

Du kan upprepa steg 3 till 5 för andra plattformar.