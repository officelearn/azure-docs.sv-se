---
title: Bli en lokal kodarpartner - Azure Media Services
description: I den här artikeln beskrivs hur du verifierar lokala direktuppspelningsgivare.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 03/02/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f98d9942f8c30f06b0144503b056c1e8a393ae52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298639"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Så här verifierar du din lokala direktuppspelningskodare

Som en lokal azure media services-kodare främjar Media Services din produkt genom att rekommendera kodaren till företagskunder. Om du vill bli en lokal kodarpartner måste du verifiera att din lokala kodare är kompatibla med Media Services. För att göra detta, fyll i följande verifieringar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Direkt verifiering av livehändelse

1. Kontrollera att **slutpunkten för direktuppspelning** körs i ditt Media Services-konto. 
2. Skapa och starta **direktupppassningen** Live Event. <br/> Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
3. Hämta de introllade webbadresserna och konfigurera den lokala kodaren så att den använder webbadressen för att skicka en livestream med flera bitar till Media Services.
4. Hämta förhandsgransknings-URL:en och använd den för att kontrollera att indata från kodaren verkligen tas emot.
5. Skapa ett nytt **tillgångsobjekt.**
6. Skapa en **Live Output** och använd det tillgångsnamn som du skapade.
7. Skapa en **streaming locator** med de inbyggda **strömmande principtyperna.**
8. Lista sökvägarna i **strömningspositioneraren** för att få tillbaka webbadresserna som ska användas.
9. Hämta värdnamnet för den slutpunkt för **direktuppspelning** som du vill strömma från.
10. Kombinera webbadressen från steg 8 med värdnamnet i steg 9 för att få den fullständiga webbadressen.
11. Kör din live-kodare i cirka 10 minuter.
12. Stoppa livehändelsen. 
13. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att titta på den arkiverade tillgången för att säkerställa att uppspelningen inte har några synliga fel på alla kvalitetsnivåer. Du kan också titta på och validera via förhandsgranskningsadressen under livesessionen.
14. Registrera tillgångs-ID, den publicerade direktuppspelnings-URL:en för livearkivet och de inställningar och den version som används från din live-kodare.
15. Återställ livehändelsetillståndet när du har skapat varje exempel.
16. Upprepa steg 5 till 15 för alla konfigurationer som stöds av kodaren (med och utan annonssignalering, bildtexter eller olika kodningshastigheter).

## <a name="live-encoding-live-event-verification"></a>Verifiering av liveevenemangskodning

1. Kontrollera att **slutpunkten för direktuppspelning** körs i ditt Media Services-konto. 
2. Skapa och starta **live-kodningen** Live Event. <br/> Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).
3. Hämta de introllade webbadresserna och konfigurera kodaren så att en enda bithastighets livestream ska kunna skickas till Media Services.
4. Hämta förhandsgransknings-URL:en och använd den för att kontrollera att indata från kodaren verkligen tas emot.
5. Skapa ett nytt **tillgångsobjekt.**
6. Skapa en **Live Output** och använd det tillgångsnamn som du skapade.
7. Skapa en **streaming locator** med de inbyggda **strömmande principtyperna.**
8. Lista sökvägarna i **strömningspositioneraren** för att få tillbaka webbadresserna som ska användas.
9. Hämta värdnamnet för den slutpunkt för **direktuppspelning** som du vill strömma från.
10. Kombinera webbadressen från steg 8 med värdnamnet i steg 9 för att få den fullständiga webbadressen.
11. Kör din live-kodare i cirka 10 minuter.
12. Stoppa livehändelsen.
13. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att titta på den arkiverade tillgången för att säkerställa att uppspelningen inte har några synliga fel för alla kvalitetsnivåer. Du kan också titta på och validera via förhandsgranskningsadressen under livesessionen.
14. Registrera tillgångs-ID, den publicerade direktuppspelnings-URL:en för livearkivet och de inställningar och den version som används från din live-kodare.
15. Återställ livehändelsetillståndet när du har skapat varje exempel.
16. Upprepa steg 5 till 15 för alla konfigurationer som stöds av kodaren (med och utan annonssignalering, bildtexter eller olika kodningshastigheter).

## <a name="longevity-verification"></a>Verifiering av livslängd

Följ samma steg som i [Direkt verifiering av Live Event](#pass-through-live-event-verification) utom steg 11. <br/>I stället för 10 minuter kör du din live-kodare i en vecka eller längre. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att titta på livestreamingen då och då (eller en arkiverad tillgång) för att säkerställa att uppspelningen inte har några synliga problem.

## <a name="email-your-recorded-settings"></a>Skicka dina inspelade inställningar via e-post

Slutligen, e-post dina inspelade inställningar och amshelp@microsoft.com levande parametrar arkiv till Azure Media Services på som ett meddelande om att alla självverifiering kontroller har passerat. Inkludera även dina kontaktuppgifter för eventuella uppföljningar. Du kan kontakta Azure Media Services-teamet med eventuella frågor om den här processen.

## <a name="see-also"></a>Se även

[Testade lokala kodare](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Nästa steg

[Livestreaming med Media Services v3](live-streaming-overview.md)
