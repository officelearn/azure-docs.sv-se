---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 3217383b105c022aef42d8000f3a41cefea542fe
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133275"
---
1. Besök [Azure Portal].
2. Klicka på **App Services** &gt; serverdelen som du skapade.
3. I inställningarna för mobilappar, klickar du på **Snabbstart** > **Cordova**.
![Azure-portalen med snabbstart för mobila appar markerad][quickstart]
4. Under **Konfigurera klientprogrammet**, väljer du **Skapa en ny app**, och klickar sedan på **Hämta**.
2. Packa upp den hämtade ZIP-filen i en katalog på hårddisken, navigera till lösningsfilen (.sln) och öppna den med hjälp av Visual Studio.
3. I Visual Studio väljer du lösningsplattform (Android, iOS eller Windows) från listrutan bredvid startpilen. Välj en viss distributionsenhet eller emulator genom att klicka på den gröna pilen i listrutan. Du kan använda Android-plattformen och Ripple-emulatorn av standardtyp. För mer avancerade självstudier (t.ex, push-meddelanden) krävs att du väljer en enhet eller emulator som stöds.
4. Tryck på F5 eller klicka på den gröna pilen för att skapa och köra din Cordova-app. Om du ser en säkerhetsdialogruta i emulatorn som begär åtkomst till nätverket, accepterar du begäran.
5. Efter att appen har startats på enheten eller emulatorn, skriver du in en beskrivande text i **Ange ny text**, exempelvis *Genomför självstudierna*, och klickar sedan på **Lägg till**-knappen.

Serverdelen infogar data från begäran i TodoItem-tabellen i SQL Database och returnerar information om det nyligen lagrade objekten tillbaka till mobilappen. Mobilappen visar dessa data i listan.

Du kan upprepa steg 3 till 5 för andra plattformar.

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
