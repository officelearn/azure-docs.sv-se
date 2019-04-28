---
title: Azure Notification Hubs och Google Firebase Cloud Messaging (FCM)-migrering
description: Beskriver hur Azure Notification Hubs behandlar Google GCM till FCM-migreringen.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458305"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Azure Notification Hubs och Google Firebase Cloud Messaging (FCM)-migrering

## <a name="current-state"></a>Aktuellt tillstånd

Google meddelade när dess migrering från Google Cloud Messaging (GCM) till Firebase Cloud Messaging (FCM), push-tjänster som våra var tvungen att justera hur vi har skickat meddelanden till Android-enheter för ändringen.

Vi uppdaterade vår service-serverdelen och sedan publiceras uppdateringar till vår API och SDK: er efter behov. Med vår implementering fattade vi beslutet att bibehålla kompatibilitet med befintliga GCM meddelande scheman för att minimera påverkan för kunden. Det innebär att vi för närvarande skicka meddelanden till Android-enheter med FCM i FCM bakåtkompatibelt läge. Slutligen vill vi lägga till SANT stöd för FCM, inklusive de nya funktionerna och nyttolastformat. Det är en mer långsiktiga ändring och den aktuella migreringen fokuserar på kompatibilitet med befintliga program och SDK: er. Du kan använda antingen GCM eller FCM-SDK: er i din app (tillsammans med vår SDK) och vi Kontrollera att meddelandet skickas på rätt sätt.

Vissa kunder nyligen fått ett e-postmeddelande från Google varning om appar med hjälp av en GCM-slutpunkt för meddelanden. Det var bara en varning och ingenting har brutits – appens Android fortfarande skickas meddelanden till Google för bearbetning och Google fortfarande bearbetar dem. Vissa kunder som uttryckligen anges GCM-slutpunkten i sina tjänstkonfiguration fortfarande användes föråldrad slutpunkten. Vi hade redan har identifierat luckan och arbetade med att åtgärda problemet när Google skickade e-postmeddelandet.

Vi ersätts den inaktuella slutpunkten och korrigeringen har distribuerats.

## <a name="going-forward"></a>Vi rekommenderar att

Googles FCM vanliga frågor och svar säger att du inte behöver göra något. I den [FCM vanliga frågor och svar](https://developers.google.com/cloud-messaging/faq), Google SA ”klient-SDK: er och GCM-token fortsätter att fungera på obestämd tid. Men kommer inte du att kunna rikta den senaste versionen av Google Play-tjänster i din Android-app om du migrerar till FCM ”.

Om din app använder GCM-biblioteket, gå vidare och följer Googles instruktioner för uppgradering till FCM-biblioteket i din app. Vår SDK är kompatibelt med antingen, så du inte behöver att uppdatera allt i din app på vår sida (förutsatt att du är uppdaterad med vår SDK-version).

## <a name="questions-and-answers"></a>Frågor och svar

Här är några svar på vanliga frågor som vi har hört från kunder:

**F:** Vad behöver jag göra för att vara kompatibel med slutdatumet (Google användarens aktuella slutdatum är 29 maj och kan ändras)?

**S:** Inget. Vi kommer att bibehålla kompatibilitet med befintliga GCM meddelande schemat. Din GCM-nyckel fortsätter att fungera som vanligt kommer alla GCM-SDK: er och bibliotek som används av ditt program.

När du väljer att uppgradera till FCM-SDK: er och bibliotek för att dra nytta av nya funktioner, din GCM-nyckel fungerar fortfarande. Du kan växla till en FCM-nyckel om du vill, men se till att du lägger till Firebase till din befintliga GCM-projekt när du skapar nytt Firebase-projekt. Detta garanterar att bakåtkompatibilitet med dina kunder som kör äldre versioner av appen som fortfarande använder GCM-SDK: er och bibliotek.

Om du skapar ett nytt projekt för FCM och inte ansluta till det befintliga GCM-projektet, när du har uppdaterat Meddelandehubbar med den nya FCM-hemligheten förlorar du möjligheten att push-meddelanden till din aktuella appinstallationer eftersom den nya FCM-nyckeln har inte någon länk till den gamla GCM projekt.

**F:** Varför får jag det här e-postmeddelande om den gamla GCM slutpunkter som används? Vad behöver jag göra?

**S:** Inget. Vi har varit migrera till nya slutpunkter och kommer snart att vara klar, så inga ändringar krävs. Ingenting har brutits, våra en missade slutpunkt bara orsakade varningsmeddelanden från Google.

**F:** Hur kan jag övergå till den nya FCM-SDK: er och bibliotek utan att befintliga användare?

S: Uppgradera när som helst. Google har ännu inte lanserat någon utfasning av befintliga GCM SDK: er och bibliotek. Att se till att du inte avbryta push-meddelanden till befintliga användare, se till att när du skapar det nya Firebase-projektet som du associerar med ditt befintliga GCM-projekt. Detta säkerställer att nya Firebase hemligheter fungerar för användare som kör äldre versioner av din app med GCM-SDK: er och bibliotek, samt nya användare i appen med FCM SDK: er och bibliotek.

**F:** När kan jag använda nya funktioner för FCM och scheman för Mina aviseringar?

**S:** När vi publicerar en uppdatering av vårt API och SDK: er, Håll koll – räknar vi med att ha något för dig under de kommande månaderna.
