---
title: Funktionsutvärdering - Personalizer
titleSuffix: Azure Cognitive Services
description: När du kör en utvärdering i din Personalizer-resurs från Azure-portalen ger Personalizer information om vilka funktioner i kontext och åtgärder som påverkar modellen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242417"
---
# <a name="feature-evaluation"></a>Funktionsutvärdering

När du kör en utvärdering i din Personalizer-resurs från [Azure-portalen](https://portal.azure.com)ger Personalizer information om vilka funktioner i kontext och åtgärder som påverkar modellen. 

Detta är användbart för att:

* Föreställ dig ytterligare funktioner du kan använda, få inspiration från vilka funktioner som är viktigare i modellen.
* Se vilka funktioner som inte är viktiga och eventuellt ta bort dem eller ytterligare analysera vad som kan påverka användningen.
* Ge vägledning till redaktionella eller kureringsteam om nytt innehåll eller nya produkter som är värda att ta med i katalogen.
* Felsöka vanliga problem och misstag som inträffar när funktioner skickas till Personalizer.

De viktigare funktionerna har starkare vikter i modellen. Eftersom dessa funktioner har starkare vikt, de tenderar att vara närvarande när Personalizer får högre belöningar.

## <a name="getting-feature-importance-evaluation"></a>Utvärdering av funktionsvikt

Om du vill se resultat för funktionsbetydelse måste du köra en utvärdering. Utvärderingen skapar läsbara funktionsetiketter som kan läsas av människor baserat på de funktionsnamn som observerats under utvärderingsperioden.

Den resulterande informationen om funktionsvikt representerar den aktuella Personalizer-onlinemodellen. Utvärderingen analyserar funktionens betydelse för den modell som sparades vid utvärderingsperiodens slutdatum, efter att ha genomgått all utbildning som gjorts under utvärderingen, med den aktuella online-inlärningspolicyn. 

Funktionsviktresultaten representerar inte andra principer och modeller som testats eller skapats under utvärderingen.  Utvärderingen kommer inte att innehålla funktioner som skickas till Personalizer efter utvärderingsperiodens.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Hur man tolkar funktionen betydelse utvärdering

Personalizer utvärderar funktioner genom att skapa "grupper" av funktioner som har liknande betydelse. En grupp kan sägas ha övergripande större betydelse än andra, men inom gruppen, beställning av funktioner är alfabetiskt.

Information om varje funktion innehåller:

* Om funktionen kommer från sammanhang eller åtgärder.
* Funktionsnyckel och värde.

En app för beställning av glassbutik kan till exempel se "Context.Weather:Hot" som en mycket viktig funktion.

Personalizer visar korrelationer av funktioner som, när de beaktas tillsammans, ger högre belöningar.

Du kan till exempel se "Context.Weather:Hot *with* Action.MenuItem:IceCream" samt "Context.Weather:Cold *with* Action.MenuItem:WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Åtgärder som du kan vidta baserat på funktionsutvärdering

### <a name="imagine-additional-features-you-could-use"></a>Föreställ dig ytterligare funktioner som du kan använda

Få inspiration från de viktigaste funktionerna i modellen. Om du till exempel ser "Context.MobileBattery:Low" i en videomobilapp kanske du tror att anslutningstypen också kan få kunderna att välja att se ett videoklipp framför ett annat och sedan lägga till funktioner om anslutningstyp och bandbredd i appen.

### <a name="see-what-features-are-not-important"></a>Se vilka funktioner som inte är viktiga

Eventuellt ta bort oviktiga funktioner eller ytterligare analysera vad som kan påverka användningen. Funktioner kan rankas lågt av många skäl. En kan vara att verkligen funktionen inte påverkar användarnas beteende. Men det kan också innebära att funktionen inte är uppenbar för användaren. 

En videowebbplats kan till exempel se att "Action.VideoResolution=4k" är en funktion med låg prioritet som motsäger användarforskning. Orsaken kan vara att programmet inte ens nämner eller visar videoupplösningen, så att användarna inte ändrar sitt beteende baserat på det.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Ge vägledning till redaktioner eller kureringsteam

Ge vägledning om nytt innehåll eller produkter som är värda att ta med i katalogen. Personalizer är utformad för att vara ett verktyg som förstärker mänsklig insikt och team. Ett sätt det gör detta är genom att ge information till redaktionella grupper om vad det handlar om produkter, artiklar eller innehåll som driver beteende. Scenariot för videoprogram kan till exempel visa att det finns en viktig funktion som kallas "Action.VideoEntities.Cat:true", vilket får redaktionen att ta in fler kattvideor.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Felsöka vanliga problem och misstag

Vanliga problem och misstag kan åtgärdas genom att ändra programkoden så att den inte skickar olämpliga eller felaktigt formaterade funktioner till Personalizer. 

Vanliga misstag när du skickar funktioner är följande:

* Skicka personligt identifierbar information (PII). PiI som är specifikt för en individ (t.ex. namn, telefonnummer, kreditkortsnummer, IP-adresser) ska inte användas med Personalizer. Om ditt program behöver spåra användare använder du ett icke-identifierande UUID eller något annat UserID-nummer. I de flesta fall är detta också problematiskt.
* Med ett stort antal användare är det osannolikt att varje användares interaktion kommer att väga mer än alla befolkningens interaktion, så att skicka användar-ID (även om icke-PII) förmodligen kommer att lägga till mer buller än värde till modellen.
* Skicka datumtidsfält som exakta tidsstämplar i stället för medbaserade tidsvärden. Det är mer användbart att ha funktioner som Context.TimeStamp.Day=Monday eller "Context.TimeStamp.Hour"="13". Det kommer att finnas högst 7 eller 24 funktionsvärden för varje. Men "Context.TimeStamp":"1985-04-12T23:20:50.52Z" är så exakt att det inte kommer att finnas något sätt att lära av det eftersom det aldrig kommer att hända igen.

## <a name="next-steps"></a>Nästa steg

Förstå [skalbarhet och prestanda](concepts-scalability-performance.md) med Personalizer.

