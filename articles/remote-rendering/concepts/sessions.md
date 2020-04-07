---
title: Fjärrrenderingssessioner
description: Beskriver vad en fjärrrenderingssession är
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.openlocfilehash: 91a59e1398bf5e68799ad16a20dfb824904edc8a
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681693"
---
# <a name="remote-rendering-sessions"></a>Fjärrrenderingssessioner

I Azure Remote Rendering (ARR) är en *session* ett nyckelkoncept. I den här artikeln förklaras exakt vad en session är.

## <a name="overview"></a>Översikt

Azure Remote Rendering fungerar genom att avlasta komplexa renderingsuppgifter i molnet. Dessa renderingsuppgifter kan inte uppfyllas av vilken server som helst, eftersom de flesta molnservrar inte har GPU:er. På grund av mängden data som är inblandade och det hårda kravet på att producera resultat till interaktiva bildhastigheter, det ansvar som servern hanterar vilken användarbegäran inte heller kan överlämnas till en annan maskin i farten, vilket kan vara möjligt för vanligare webbtrafik.

Det innebär att när du använder Azure Remote Rendering måste en molnserver med nödvändiga maskinvarufunktioner reserveras uteslutande för att hantera dina renderingsbegäranden. En *session* refererar till allt som är involverat med att interagera med den här servern. Det börjar med den första begäran om att reservera (*lease)* en dator för din användning, fortsätter med alla kommandon för inläsning och manipulera modeller, och slutar med att frigöra lånet på molnservern.

## <a name="managing-sessions"></a>Hantera sessioner

Det finns flera sätt att hantera och interagera med sessioner. Det språkoberoende sättet att skapa, uppdatera och stänga av sessioner är via [REST API för sessionshantering](../how-tos/session-rest-api.md). I C# och C++exponeras dessa `AzureFrontend` åtgärder `AzureSession`genom klasserna och . För Unity-program finns det ytterligare `ARRServiceUnity` verktygsfunktioner som tillhandahålls av komponenten.

När du är *ansluten* till en aktiv session exponeras åtgärder som `AzureSession` att läsa in [modeller](models.md) och interagera med scenen genom klassen.

### <a name="managing-multiple-sessions-simultaneously"></a>Hantera flera sessioner samtidigt

Det går inte att *ansluta* till flera sessioner från en enhet helt. Du kan dock skapa, observera och stänga av så många sessioner du vill från ett enda program. Så länge appen inte är tänkt att någonsin ansluta till en session, behöver den inte köras på en enhet som HoloLens 2 heller. Ett användningsfall för en sådan implementering kan vara om du vill styra sessioner genom en central mekanism. Man kan till exempel bygga en webbapp, där flera surfplattor och holoobjektiv kan logga in. Sedan kan appen visa alternativ på surfplattorna, till exempel vilken CAD-modell som ska visas. Om en användare gör ett val, meddelas denna information till alla HoloLenses för att skapa en delad upplevelse.

## <a name="session-phases"></a>Sessionsfaser

Varje session genomgår flera faser.

### <a name="session-startup"></a>Start av session

När du ber ARR att [skapa en ny session](../how-tos/session-rest-api.md#create-a-session)är det första den gör att returnera en session [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). Med den här UUID kan du fråga information om sessionen. UUID och viss grundläggande information om sessionen sparas i 30 dagar, så du kan fråga den informationen även efter att sessionen har stoppats. Nu rapporteras **sessionstillståndet** som **Start**.

Därefter försöker Azure Remote Rendering hitta en server som kan vara värd för din session. Det finns två parametrar för den här sökningen. Först kommer det bara att reservera servrar i din [region](../reference/regions.md). Det beror på att nätverksfördröjningen i olika regioner kan vara för hög för att garantera en anständig upplevelse. Den andra faktorn är önskad *storlek* som du har angett. I varje region finns det ett begränsat antal servrar som kan uppfylla *standard-* eller premiumstorleksbegäran. *Premium* Om alla servrar av den begärda storleken för närvarande används i din region misslyckas därför skapandet av sessionen. Orsaken till felet [kan efterfrågas](../how-tos/session-rest-api.md#get-sessions-properties).

> [!IMPORTANT]
> Om du *Standard* begär en standard-VM-storlek och begäran misslyckas på grund av hög efterfrågan, innebär det inte att begära en *Premium-server* kommer att misslyckas också. Så om det är ett alternativ för dig, kan du försöka falla tillbaka till en *Premium* VM.

När tjänsten hittar en lämplig server måste den kopiera rätt virtuell dator (VM) till den för att göra den till en Azure Remote Rendering-värd. Den här processen tar flera minuter. Därefter startas den virtuella datorn och **sessionstillståndet** övergår till **Klar**.

Nu väntar servern uteslutande på dina indata. Detta är också den punkt från vilken på du får faktureras för tjänsten.

### <a name="connecting-to-a-session"></a>Ansluta till en session

När sessionen är *klar*kan du *ansluta* till den. När enheten är ansluten kan den skicka kommandon för att läsa in och ändra modeller. Varje ARR-värd betjänar bara en klientenhet i taget, så när en klient ansluter till en session har den exklusiv kontroll över det renderade innehållet. Det innebär också att renderingsprestanda aldrig kommer att variera av skäl utanför din kontroll.

> [!IMPORTANT]
> Även om endast en klient kan *ansluta* till en session kan grundläggande information om sessioner, till exempel deras aktuella tillstånd, efterfrågas utan att ansluta.

När en enhet är ansluten till en session misslyckas försök från andra enheter att ansluta. Men när den anslutna enheten kopplar från, antingen frivilligt eller på grund av någon form av fel, kommer sessionen att acceptera en annan anslutningsbegäran. Alla tidigare tillstånd (laddade modeller och sådana) kasseras så att nästa anslutningsenhet får en ren griffeltavla. Således sessioner kan återanvändas många gånger, av olika enheter och det kan vara möjligt att dölja sessionen start overhead från slutanvändaren i de flesta fall.

> [!IMPORTANT]
> Fjärrservern ändrar aldrig tillståndet för klientdata. Alla mutationer av data (t.ex. transformeringsuppdateringar och belastningsbegäranden) måste utföras av klientprogrammet. Alla åtgärder uppdaterar omedelbart klienttillståndet.

### <a name="session-end"></a>Sessionen avslutas

När du begär en ny session anger du en *maximal lånetid*, vanligtvis inom intervallet en till åtta timmar. Detta är den tid under vilken värden kommer att acceptera dina indata.

Det finns två vanliga skäl för en session att avsluta. Antingen begär du manuellt att sessionen ska stoppas eller så går den maximala lånetiden ut. I båda fallen stängs alla aktiva anslutningar till värden direkt och tjänsten stängs av på den servern. Servern ges sedan tillbaka till Azure-poolen och kan rekvireras för andra ändamål. Det går inte att ångra eller avbryta att en session stoppas. Om du frågar **sessionstillståndet** för en stoppad session **returneras stoppad** eller **Har upphört att**gälla , beroende på om det stängdes av manuellt eller på grund av att den maximala lånetiden uppnåddes.

En session kan också stoppas på grund av något fel.

I samtliga fall debiteras du inte ytterligare när en session har stoppats.

> [!WARNING]
> Om du ansluter till en session och hur länge det inte påverkar faktureringen. Vad du betalar för tjänsten beror på *sessionens varaktighet,* det betyder den tid som en server är exklusivt reserverad för dig och de begärda maskinvarufunktionerna (vm-storleken). Om du startar en session, ansluter i fem minuter och sedan inte stoppar sessionen, så att den fortsätter att köras tills lånet löper ut, debiteras du för hela sessionshyrtiden. Omvänt är den *maximala hyrestiden* mestadels ett skyddsnät. Det spelar ingen roll om du begär en session med en lånetid på åtta timmar, sedan bara använda den i fem minuter, om du manuellt stoppa sessionen efteråt.

#### <a name="extend-a-sessions-lease-time"></a>Förläng en sessions lånetid

Du kan [förlänga lånetiden](../how-tos/session-rest-api.md#update-a-session) för en aktiv session om det visar sig att du behöver det längre.

## <a name="example-code"></a>Exempelkod

Koden nedan visar en enkel implementering av att starta en session, vänta på *det färdiga* tillståndet, ansluta och sedan koppla från och stänga av igen.

``` cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Flera `AzureFrontend` `AzureSession` instanser och instanser kan underhållas, manipuleras och efterfrågas från kod. Men endast en enda enhet `AzureSession` kan ansluta till en åt gången.

Livslängden för en virtuell dator är `AzureFrontend` inte `AzureSession` knuten till instansen eller instansen. `AzureSession.StopAsync`måste kallas för att stoppa en session.

Det beständiga sessions-ID:t kan efterfrågas via `AzureSession.SessionUUID()` och cachelagras lokalt. Med det här ID:t kan ett program anropa `AzureFrontend.OpenSession` för att binda till den sessionen.

När `AzureSession.IsConnected` är `AzureSession.Actions` sant returnerar `RemoteManager`en instans av , som innehåller funktionerna för att [läsa in modeller,](models.md)manipulera [entiteter](entities.md)och [fråga information](../overview/features/spatial-queries.md) om den renderade scenen.

## <a name="next-steps"></a>Nästa steg

* [Entiteter](entities.md)
* [Modeller](models.md)
