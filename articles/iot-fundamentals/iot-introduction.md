---
title: Introduktion till Sakernas Internet (IoT) i Azure
description: Introduktionen förklarar grunderna i Azure IoT och IoT-tjänster, och innehåller exempel som kan illustrera användningen av IoT.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: ca99f9ac36281ecddf41bcc228440adcad90412b
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582436"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Vad är Azure Sakernas Internet (IoT)?

Azure Sakernas Internet (IoT) är en samling Microsoft-hanterade molntjänster som ansluter, övervakar och styr miljontals IoT-resurser. Enklare uttryckt består en IoT-lösning av en eller flera IoT-enheter och en eller flera serverdelstjänster som körs i molnet som kommunicerar med varandra. 

Den här artikeln beskriver grunderna i IoT, tar upp användningsfall och beskriver kortfattat de åtta separata tjänsterna som är tillgängliga. Genom att förstå vad som är tillgängligt kan du ta reda på vad du vill titta på mer noggrant för att utforma ditt scenario.

## <a name="introduction"></a>Introduktion

De viktigaste delarna av en IoT-lösning är följande: enheter, serverdelstjänster och kommunikation mellan de båda. 

### <a name="iot-devices"></a>IoT-enheter

Enheter som vanligtvis består av ett kretskort med anslutna sensorer som ansluter till internet. Många enheter kommunicerar via en Wi-Fi-krets. Här följer några exempel på IoT-enheter:

* trycksensorer på en fjärransluten oljepump
* temperatur- och fuktighetssensorer i en luftkonditioneringsenhet
* accelerometrar i en hiss
* närvarosensorer i ett rum

Två enheter som används ofta för prototyper är grundläggande MX-Chip IoT Devkit från Microsoft och Raspberry PI-enheter. MX-Chip Devkit har sensorer som skapats för temperatur, tryck och fuktighet, samt ett gyroskop och en accelerometer, en magnetometer och en Wi-Fi-krets. Raspberry PI är en IoT-enhet som du kan koppla många olika typer av sensorer till, så att du kan välja exakt det du behöver för ditt scenario. 

[SDK:er för IoT-enheter](../iot-hub/iot-hub-devguide-sdks.md) hjälper dig att skapa appar som körs på dina enheter så att de kan utföra uppgifter som du behöver. Med SDK:er kan du skicka telemetri till din IoT-hubb, ta emot meddelanden och uppdateringar från IoT-hubb och så vidare.

### <a name="communication"></a>Kommunikation

Enheten kan kommunicera med serverdelstjänster i båda riktningarna. Här följer några exempel på hur enheten kan kommunicera med serverdelslösningen.

#### <a name="examples"></a>Exempel 

* Din enhet kan skicka temperatur från en mobil kylbil var femte minut till en IoT-hubb. 

* Serverdelstjänsten kan be enheten för att skicka telemetri oftare för att diagnostisera problem. 

* Din enhet kan skicka aviseringar baserat på de värden som läses från dess sensorer. Om du till exempel övervakar en satsreaktor på en kemisk anläggning kanske du vill skicka en avisering när temperaturerna överskrider ett visst värde.

* Din enhet kan skicka information till en instrumentpanel för visning av mänskliga operatörer. Exempelvis kan ett kontrollrum i en raffinaderi visa temperatur och tryck på varje rör, samt den volym som passerar genom röret, vilket gör att operatörerna kan titta på den. 

Dessa uppgifter med flera kan implementeras med hjälp av [SDK:er för IoT-enheter](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Överväganden för anslutning

Att ansluta enheter säkert och tillförlitligt är ofta den största utmaningen för IoT-lösningar. Det här beror på att IoT-enheter skiljer sig från andra klienter som till exempel webbläsare och mobilappar. IoT-enheter är mer specifikt:

* Ofta inbyggda system utan mänskliga operatörer (till skillnad från en telefon).

* Kan distribueras på avlägsna platser, där fysisk åtkomst är dyr.

* Kan i vissa fall enbart nås via lösningens serverdel. Det finns inget annat sätt att interagera med enheten.

* Kan ha begränsade ström- och bearbetningsresurser.

* Kan ha oregelbunden, långsam eller dyr nätverksanslutning.

* Kan behöva använda privatägda, anpassade eller branschspecifika programprotokoll.

### <a name="back-end-services"></a>Serverdelstjänster 

Här följer några av de funktioner som en serverdelstjänst kan ge.

* Ta emot stora mängder telemetri från dina enheter och bestämma hur telemetridata bearbetas och lagras.

* Analysera telemetri för att ge insikter, oavsett om de skickas i realtid eller i efterhand.

* Skicka kommandon från molnet till en specifik enhet. 

* Etablera enheter och kontrollera vilka enheter som kan att ansluta till din infrastruktur.

* Kontrollera status för enheter och övervaka deras verksamhet.

I ett förutsägande underhållsscenario lagrar till exempel molnserverdelen historisk telemetri. Lösningen använder dessa data för att identifiera potentiellt avvikande beteenden på specifika pumpar innan de orsakar verkliga problem. Med hjälp av dataanalys kan den identifiera att den förebyggande lösningen ska skicka ett kommando till enheten för att vidta en korrigerande åtgärd. Den här processen skapar en automatisk feedback-loop mellan enheten och molnet som ökar effektiviteten för lösningen dramatiskt.

## <a name="an-iot-example"></a>Ett IoT-exempel

Här är ett exempel på hur ett företag använde IoT för att spara miljontals dollar. 

Det finns en enorm boskapsranch med hundratusentals kor. Det är svårt att hålla koll på så många kor, och veta hur de mår, och det kräver mycket omkringkörande. De kopplade sensorer till varje enskild ko som skickade information som GPS-koordinater och temperatur till en serverdelstjänst som ska skrivas till en databas.

De har en analytisk tjänst som söker igenom inkommande data och analyserar data för varje ko för att kontrollera frågor som:

* Har kon feber? Hur länge har kon haft feber? Om det är längre än en dag ska GPS-koordinater hämtas, och kon ska hittas och behandlas med antibiotika om det behövs. 

* Är kon på samma plats i mer än en dag? I så fall hämtar du GPS-koordinaterna och letar reda på kon. Har kon fallit från en klippa? Är kon skadad? Behöver kon hjälp? 

Företagets implementering av den här IoT-lösningen gjorde att de kunde kontrollera och behandla kor snabbt och minska tiden som de ägnade åt att köra omkring och kontrollera djuren, vilket gjorde att de sparade mycket pengar. Fler exempel från verkligheten på hur företag använder IoT finns i avsnittet om [Microsofts tekniska fallstudier för IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="iot-services"></a>IoT-tjänster

Det finns flera IoT-relaterade tjänster i Azure och det kan vara förvirrande att ta reda på vilken du vill använda. Vissa, till exempel IoT Central och IoT-lösningsacceleratorer, ger mallar för att hjälpa dig att skapa en egen lösning och snabbt komma igång. Du kan också helt utveckla dina egna lösningar med hjälp av andra tjänster som är tillgängliga – det beror helt på hur mycket hjälp och hur stor kontroll du vill ha. Här följer en lista över tjänsterna som är tillgängliga, samt vad du kan använda dem för.

1. [**IoT Central**](../iot-central/overview-iot-central.md): Detta är en SaaS-lösning som hjälper dig att ansluta, övervaka och hantera dina ioT-enheter. Om du vill starta väljer du en mall för din typ av enhet och skapar och testar ett grundläggande IoT Central-program som kommer att användas av operatörer av enheten. Med programmet IoT Central kommer också att kunna övervaka enheterna och etablera nya enheter. Den här tjänsten är för enkla IoT-lösningar som inte kräver mycket tjänstanpassning. 

2. [**IoT-lösningsacceleratorer**](/azure/iot-suite): Det här är en samling av PaaS-lösningar som du kan använda för att påskynda utvecklingen av din IoT-lösning. Du börjar med en angiven IoT-lösning och sedan anpassar du lösningen efter dina behov. Du behöver Java- eller .NET-kunskaper för att anpassa serverdelen och kunskaper i JavaScript för att anpassa visualiseringen. 

3. [**IoT Hub**](/azure/iot-hub/): Med den här tjänsten kan du ansluta från dina enheter till en IoT-hubb och övervaka och styra flera miljarder IoT-enheter. Detta är särskilt användbart om du behöver dubbelriktad kommunikation mellan dina IoT-enheter och serverdelen. Det här är den underliggande tjänsten för IoT Central och IoT-lösningsacceleratorer. 

4. [**IoT Hub Device Provisioning Service**](/azure/iot-dps/): Det här är en hjälptjänst för IoT Hub som du kan använda för att etablera enheter till din IoT-hubb på ett säkert sätt. Med den här tjänsten kan du enkelt etablera miljontals enheter snabbt, i stället för att etablera dem en i taget. 

5. [**IoT Edge**](/azure/iot-edge/): Den här tjänsten bygger på IoT Hub. Den kan användas för att analysera data på IoT-enheter i stället för i molnet. Färre meddelanden måste skickas till molnet eftersom delar av din arbetsbelastning flyttas till Edge. 

6. [**Azure Digital Twins**](../digital-twins/index.yml): Med den här tjänsten kan du skapa omfattande modeller av den fysiska miljön. Du kan modellera relationer och interaktioner mellan människor, utrymmen och enheter. Du kan till exempel förutsäga underhållsbehov för en fabrik, analysera i realtidsenergikrav för ett elnät eller optimera användningen av tillgängligt utrymme för ett kontor.

7. [**Time Series Insights**](/azure/time-series-insights): Med den här tjänsten kan du lagra, visualisera fråga stora mängder time series-data som genererats av IoT-enheter. Du kan använda den här tjänsten med IoT Hub. 

8. [**Azure Maps**](/azure/azure-maps): Den här tjänsten tillhandahåller geografisk information till webbprogram och mobilappar. Det finns en fullständig uppsättning av REST API:er samt en webbaserad JavaScript-kontroll som kan användas för att skapa flexibla program som fungerar på datorer eller mobila appar för Windows- och Apple-enheter.

## <a name="next-steps"></a>Nästa steg

För verkliga affärsfall och vilken arkitektur som använts finns i avsnittet om [Microsoft Azure IoT:s tekniska fallstudier](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Vissa exempelprojekt som du kan prova med en IoT DevKit finns i [IoT DevKit-katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

En mer utförlig förklaring av de olika tjänsterna och hur de används finns i [Azure IoT-tjänster och tekniker](iot-services-and-technologies.md).

En detaljerad beskrivning av IoT-arkitekturen finns i [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Referensarkitektur för Microsoft Azure IoT).
