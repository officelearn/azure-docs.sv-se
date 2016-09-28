<properties
    pageTitle="Koncept i Mobile Engagement | Microsoft Azure"
    description="Koncept i Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


# Koncept i Azure Mobile Engagement

Mobile Engagement har definierat några koncept som är gemensamma för alla plattformar som stöds. I den här artikeln beskrivs koncepten kortfattat.

Den här artikeln är ett bra ställe att börja på om du är nybörjare på Mobile Engagement. Läs även igenom dokumentationen för den specifika plattform som du använder eftersom den vidareutvecklar resonemangen kring de koncept som beskrivs i denna artikel med mer information, fler exempel och eventuella begränsningar.

## Enheter och användare
Mobile Engagement identifierar användare genom att generera ett unikt ID för varje enhet. Detta ID kallas enhets-ID (eller `deviceid`). Alla appar som körs på samma enhet delar samma enhets-ID.

Detta innebär med andra ord att Mobile Engagement kopplar ihop enheten med en viss användare. Användare och enhet är alltså likvärdiga koncept i det här sammanhanget.

## Sessioner och aktiviteter
En session kan definieras som ett användningstillfälle som sträcker sig över en viss tidsperiod, vanligen från den tidpunkt då användaren börjar använda ett program fram till den tidpunkt då användningen upphör.

En aktivitet är den användning av en viss underordnad del av appen som genomförs av en användare (utgörs vanligen av en skärm, men det kan vara vad som helst i appen).

En användare kan bara utföra en aktivitet i taget.

En aktivitet identifieras med ett namn (begränsat till 64 tecken) och du kan även välja att bädda in extra data (högst 1 024 byte).

Sessioner beräknas automatiskt utifrån sekvensen av aktiviteter som utförs av användarna. Sessionen startar när användaren startar den första aktiviteten och slutar när användarens sista aktivitet är klar. Det innebär att en session inte behöver startas eller stoppas. Istället är det aktiviteterna som uttryckligen startas eller stoppas. Om ingen aktivitet rapporteras så rapporteras heller ingen session.

## Händelser
Händelser som används för att rapportera omedelbara åtgärder (till exempel när en användare har tryckt på en tangent eller läst en artikel).

En händelse kan vara relaterad till den aktuella sessionen eller ett jobb som körs, eller också kan det vara en fristående händelse.

En händelse identifieras med ett namn (begränsat till 64 tecken) och du kan även välja att bädda in extra data (högst 1 024 byte).

## Fel
Fel används för att rapportera problem som identifieras av programmet (till exempel felaktiga användaråtgärder eller misslyckade API-anrop).

Ett fel kan vara relaterat till den aktuella sessionen, ett jobb som körs eller också kan det vara ett fristående fel.

Ett fel identifieras med ett namn (begränsat till 64 tecken) och du kan även välja att bädda in extra data (högst 1 024 byte).

## Jobb
Jobb används för att rapportera åtgärder som har en varaktighet (som varaktighet för API-anrop, visningstid för annonser, varaktighet för uppgifter som körs i bakgrunden eller varaktighet för användaråtgärder).

Ett jobb är inte relaterat till en session eftersom en uppgift kan köras i bakgrunden utan interaktion med användaren.

Ett jobb identifieras med ett namn (begränsat till 64 tecken) och du kan även välja att bädda in extra data (högst 1 024 byte).

## Krascher
Krascher utfärdas automatiskt av Mobile Engagement SDK för att rapportera programfel när problem som inte identifierats av programmet får det att krascha.

## Programinformation
Programinformation (eller appinfo) används för att tagga användare, det vill säga för att koppla vissa data till användare av ett program (detta liknar cookies i webbläsarna, men med undantag för att appinfo lagras på serversidan på plattformen Azure Mobile Engagement).

Appinfo kan registreras med Mobile Engagement SDK API eller genom att använda enhets-API för Mobile Engagement-plattformen.

Appinfo utgörs av ett nyckel-/värdepar som är kopplade till en enhet. Nyckeln är namnet på appinfon (begränsat till 64 ASCII-bokstäver [a–z, A–Z], siffror [0–9] och understreck [_]). Värdet (begränsat till 1 024 tecken) kan vara en sträng, ett heltal, ett datum (ÅÅÅÅ-MM-DD) eller ett booleskt värde (sant eller falskt).

Valfritt antal appinformationsvärden kan kopplas till en enhet inom de gränser som definierats av villkoren för den specifika prisnivån i Mobile Engagement. Mobile Engagement håller bara reda på den senaste värdeuppsättningen för en nyckel (ingen historik). Om ett värde anges eller ändras tvingas Mobile Engagement att omvärdera målgruppskriterierna för appinfon (om sådan finns), vilket innebär att appinfo kan användas för att utlösa push-meddelanden i realtid.

## Extra data
Extra data (eller extra information) utgörs av diverse uppgifter som kan kopplas till händelser, fel, aktiviteter och jobb.

Extra data är strukturerade på ungefär samma sätt som JSON-objekt – de består av ett träd med nyckel-/värdepar. Nycklarna är begränsade till 64 ASCII-bokstäver [a–z, A–Z], siffror [0–9] och understreck [_]) och den totala storleken för extra information är max 1 024 tecken (efter kodning i JSON av Mobile Engagement SDK).

Hela trädet med nyckel-/värdepar lagras som ett JSON-objekt. Endast den första nivån av nycklar/värden delas upp och görs direkt tillgänglig för avancerade funktioner såsom Segment (du kan till exempel enkelt definiera ett segment som kallas ”SciFi-fans” som består av alla användare som har skickat händelsen med namnet ”visat_innehåll” med den extra nyckeln ”innehållstyp” och värdet ”scifi” minst tio gånger under den senaste månaden). Därför rekommenderar vi att du endast skickar extra information som utgörs av enkla listor med nyckel-/värdepar bestående av skalära värden (till exempel strängar, datum, heltal eller booleska värden).

## Nästa steg

- [Översikt över Windows Universal SDK för Azure Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md)
- [Översikt över Windows Phone Silverlight SDK för Azure Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS SDK för Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md)
- [Android SDK för Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md)



<!--HONumber=Sep16_HO3-->


