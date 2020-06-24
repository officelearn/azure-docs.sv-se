---
title: Koncept och definitioner för anpassade kommandon – tal tjänst
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig mer om begrepp och definitioner för program med anpassade kommandon.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5a5e3590e0e184633760f080dfd3402ed75bd48e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307895"
---
# <a name="custom-commands-concepts-and-definitions"></a>Koncept och definitioner för anpassade kommandon

Den här artikeln fungerar som en referens för begrepp och definitioner för program med anpassade kommandon.

## <a name="commands-configuration"></a>Konfiguration av kommandon
Kommandon är de grundläggande Bygg stenarna i ett program för anpassade kommandon. Ett kommando är en uppsättning konfigurationer som krävs för att slutföra en viss uppgift som definierats av en användare.

### <a name="example-sentences"></a>Exempel på meningar
Exempel på yttranden är de uppsättnings exempel som användaren kan säga för att utlösa ett visst kommando. Observera att du bara behöver ange ett exempel på yttranden och inte en fullständig lista. 

### <a name="parameters"></a>Parametrar
Parametrar är information som krävs av kommandona för att slutföra en uppgift. I komplexa scenarier kan parametrar också användas för att definiera villkor som utlöser anpassade åtgärder.

### <a name="completion-rules"></a>Regler för slut för ande
Serie regler som ska utföras när kommandot är klart att uppfyllas, dvs. När alla villkor i reglerna är uppfyllda.

### <a name="interaction-rules"></a>Interaktions regler
Ytterligare regler för att hantera mer detaljerade eller komplexa situationer. Du kan lägga till ytterligare verifieringar eller konfigurera avancerade funktioner, till exempel bekräftelser eller en steg korrigering. Du kan också bygga egna anpassade interaktions regler.

## <a name="parameters-configuration"></a>Parameter konfiguration

Parametrar är information som krävs av kommandon för att slutföra en uppgift. I komplexa scenarier kan parametrar också användas för att definiera villkor som utlöser anpassade åtgärder.

### <a name="name"></a>Name
En parameter identifieras av egenskapen Name. Du bör alltid ge ett beskrivande namn till en parameter. En parameter kan refereras mellan olika avsnitt, t. ex. vid konstruktion av villkor, tal svar eller andra åtgärder.
 
### <a name="isglobal"></a>IsGlobal
Kryss ruta som anger om omfånget för den här parametern delas mellan alla kommandon i programmet. Om en parameter är global kan dess värde tillhandahållas från alla kommando omfånget och när ett värde har tilldelats – det kan refereras från något av kommandona. 

### <a name="required"></a>Obligatorisk
Kryss ruta som anger om ett värde för den här parametern krävs för att slutföra kommandona/slutföras. Du måste konfigurera svar för att användaren ska kunna ange värde om en parameter är markerad som obligatorisk.

### <a name="type"></a>Typ
Anpassade kommandon stöder följande parameter typer:


* DateTime
* Geografi
* Antal
* Sträng

Alla dessa parameter typer stöder konfiguration av standardvärde som du kan konfigurera från portalen.

### <a name="configuration"></a>Konfiguration
Konfigurationen är en parameter egenskap som endast definierats för typen: String. Nedan visas de värden som stöds
* Inga
* Acceptera fullständig indata: om du aktiverar det här alternativet accepterar parametern alla indata-uttryck, vilket är användbart när användaren behöver en parameter med fullständig uttryck. för t. ex. post adresser.
* Acceptera fördefinierade indatavärden från extern katalog: används för att konfigurera parametrar som kan anta en mängd olika värden. t. ex. försäljnings katalog. I det här fallet finns katalogen på en extern webb slut punkt och kan konfigureras separat.
* Acceptera fördefinierade indatavärden från intern katalog: används för att konfigurera parametrar som kan anta några värden. I det här fallet måste värdena konfigureras i tal Studio.


### <a name="validation"></a>Validering
Valideringar är lämpliga för vissa parameter typer som låter dig konfigurera begränsningar för parameterns värde. För närvarande stöder anpassade kommandon valideringar på följande parameter typer:
* DateTime
* Antal

## <a name="rules-configuration"></a>Regel konfiguration
En regel i anpassade kommandon definieras av en uppsättning **villkor**, som när det är uppfyllt, körs en uppsättning **åtgärder**. Med regeln kan du också konfigurera **postens tillstånd** och **förväntningar** för nästa tur.

### <a name="types"></a>Typer
Anpassade kommandon stöder följande regel kategorier:
* Regler för slut för ande

    Lista med regler som ska utföras vid kommando uppfyllelse. Alla regler som kon figurer ATS i det här avsnittet för vilka villkoren är sanna, kommer att köras.
    
* Interaktions regler

    Interaktions regler kan användas för att konfigurera ytterligare anpassade verifieringar, bekräftelser, en steg korrigering eller för att utföra andra anpassade dialog logik. Dessa regler utvärderas vid varje turn bearbetning och kan användas för att utlösa slut för ande regler.

De olika åtgärderna som kon figurer ATS som en del av en regel utförs i den ordning de visas i redigerings portalen.
    
### <a name="conditions"></a>Villkor
En uppsättning villkor som måste uppfyllas för att en regel ska kunna köras. Regel villkor kan vara av följande typer:
* Parametervärdet är lika med: den konfigurerade parameterns värde är lika med ett angivet värde.
* Inget parameter värde: konfigurerade parametrar får inte ha något värde.
* Obligatoriska parametrar: konfigurerad parameter har ett värde.
* Alla obligatoriska parametrar: alla parametrar som har marker ATS som obligatoriska har ett värde.
* Uppdaterade parametrar: ett eller flera parameter värden har uppdaterats till följd av bearbetning av aktuella indata (uttryck/Activity).
* Bekräftelsen lyckades: indatamängds-uttryck/Activity lyckades bekräftas (Ja).
* Bekräftelse nekades: indatamängds-uttryck/aktivitet var en lyckad bekräftelse (Nej).
* Föregående kommando måste uppdateras: det här villkoret används i instanser när du vill fånga en negationad bekräftelse tillsammans med en uppdatering. I bakgrunden konfigureras detta för när dialog motorn identifierar en negativ bekräftelse där avsikten är samma som föregående Turn, och användaren har svarat med en uppdatering.

### <a name="actions"></a>Åtgärder
* Skicka tal svar: Skicka ett tal svar tillbaka till klienten.
* Uppdatera parameter värde: uppdatera värdet för en kommando parameter till ett angivet värde.
* Rensa parameter värde rensa bort kommando parameter värde.
* Anropa webb slut punkt: anropa till en webb slut punkt.
* Skicka aktivitet till klient: skicka en anpassad aktivitet till klienten.

### <a name="expectations"></a>Prestationer
Förväntningar används för att konfigurera tips för bearbetning av nästa användarindata. Följande typer stöds:
* Väntar på bekräftelse från användare: anger att programmet förväntar sig en bekräftelse (Ja/Nej) för nästa användarindata.
* Förväntade parameter indata från användare: Ange en eller flera kommando parametrar som programmet förväntar sig från användarindata.

### <a name="post-execution-state"></a>Efter körnings tillstånd
Dialog status efter bearbetning av aktuella indata (uttryck/Activity). Det är av följande typer:
* Kommandot har slutförts: Slutför kommandot och inga ytterligare regler för kommandot kommer att bearbetas.
* Regler för att köra slut för ande: kör alla giltiga regler för slut för ande.
* Vänta på användarens indata: vänta på nästa användarindata.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Se exempel på GitHub](https://aka.ms/speech/cc-samples)
