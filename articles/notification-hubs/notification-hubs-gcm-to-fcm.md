---
title: Azure Notification Hubs och FCM-migreringen (Google Firebase Cloud Messaging)
description: Beskriver hur Azure Notification Hubs adresserar Google GCM till FCM-migrering.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127031"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure Notification Hubs och Google Firebase Cloud Messaging-migrering

## <a name="current-state"></a>Aktuellt tillstånd

När Google tillkännagav sin migrering från Google Cloud Messaging (GCM) till Firebase Cloud Messaging (FCM) var push-tjänster som vårt tvungna att justera hur vi skickade meddelanden till Android-enheter för att hantera ändringen.

Vi uppdaterade vår tjänst serverd, sedan publicerade uppdateringar till våra API och SDK:er efter behov. Med vår implementering tog vi beslutet att upprätthålla kompatibilitet med befintliga GCM-meddelandescheman för att minimera kundernas påverkan. Det innebär att vi för närvarande skickar meddelanden till Android-enheter med FCM i FCM Legacy Mode. I slutändan vill vi lägga till verkligt stöd för FCM, inklusive de nya funktionerna och nyttolastformat. Det är en långsiktig förändring och den nuvarande migreringen är inriktad på att upprätthålla kompatibilitet med befintliga program och SDK: er. Du kan använda antingen GCM eller FCM SDK:er i din app (tillsammans med vår SDK) och vi ser till att meddelandet skickas korrekt.

Vissa kunder har nyligen fått ett e-postmeddelande från Google som varnade för appar med hjälp av en GCM-slutpunkt för aviseringar. Detta var bara en varning, och ingenting är trasigt - appens Android-meddelanden skickas fortfarande till Google för bearbetning och Google bearbetar dem fortfarande. Vissa kunder som uttryckligen angav GCM-slutpunkten i sin tjänstkonfiguration använde fortfarande den inaktuella slutpunkten. Vi hade redan identifierat denna lucka och arbetade på att åtgärda problemet när Google skickade e-postmeddelandet.

Vi ersatte den inaktuella slutpunkten och korrigeringen distribueras.

## <a name="going-forward"></a>Framöver

Googles FCM FAQ säger att du inte behöver göra någonting. I [FCM FAQ](https://developers.google.com/cloud-messaging/faq), Google sa "klient SDKs och GCM tokens kommer att fortsätta att arbeta på obestämd tid. Du kan dock inte rikta in dig på den senaste versionen av Google Play-tjänsterna i din Android-app om du inte migrerar till FCM."

Om din app använder GCM-biblioteket följer du Googles instruktioner om att uppgradera till FCM-biblioteket i appen. Vår SDK är kompatibel med antingen, så du behöver inte uppdatera något i din app på vår sida (så länge du är uppdaterad med vår SDK-version).

## <a name="questions-and-answers"></a>Frågor och svar

Här är några svar på vanliga frågor som vi har hört från kunder:

**F:** Vad behöver jag göra för att vara kompatibel med cutoff datum (Googles nuvarande cutoff datum är 29 maj och kan ändras)?

**A.** Ingenting. Vi kommer att upprätthålla kompatibilitet med befintliga GCM-meddelandeschema. Din GCM-nyckel fortsätter att fungera som vanligt, liksom alla GCM SDK:er och bibliotek som används av ditt program.

Om/när du bestämmer dig för att uppgradera till FCM SDK:er och bibliotek för att dra nytta av nya funktioner fungerar GCM-nyckeln fortfarande. Du kan byta till att använda en FCM-nyckel om du vill, men se till att du lägger firebase till ditt befintliga GCM-projekt när du skapar det nya Firebase-projektet. Detta garanterar bakåtkompatibilitet med dina kunder som kör äldre versioner av appen som fortfarande använder GCM SDK:er och bibliotek.

Om du skapar ett nytt FCM-projekt och inte ansluter till det befintliga GCM-projektet kommer du, när du har uppdaterat Meddelandehubbar med den nya FCM-hemligheten, att förlora möjligheten att skicka meddelanden till dina aktuella appinstallationer, eftersom den nya FCM-nyckeln inte har någon koppling till den gamla GCM Projekt.

**F:** Varför får jag det här e-postmeddelandet om gamla GCM-slutpunkter som används? Vad måste jag göra?

**A.** Ingenting. Vi har migrerat till de nya slutpunkterna och kommer att vara klar snart, så ingen förändring är nödvändig. Ingenting är bruten, vår en missade slutpunkt orsakade helt enkelt varningsmeddelanden från Google.

**F:** Hur kan jag övergå till de nya FCM SDK:erna och biblioteken utan att bryta befintliga användare?

S: Uppgradera när som helst. Google har ännu inte meddelat någon utfasning av befintliga GCM SDKs och bibliotek. Se till att du inte bryter push-meddelanden till dina befintliga användare genom att se till att du associerar med ditt befintliga GCM-projekt när du skapar det nya Firebase-projektet. Detta säkerställer att nya Firebase-hemligheter fungerar för användare som kör de äldre versionerna av din app med GCM SDK:er och bibliotek, samt nya användare av din app med FCM SDK:er och bibliotek.

**F:** När kan jag använda nya FCM-funktioner och scheman för mina aviseringar?

**A.** När vi har publicerat en uppdatering av vårt API och SDK: er, håll ögonen öppna - vi förväntar oss att ha något för dig under de kommande månaderna.
