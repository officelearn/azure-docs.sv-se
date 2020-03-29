---
title: Automatisk distribution för enhetsgrupper – Azure IoT Edge | Microsoft-dokument
description: Använda automatiska distributioner i Azure IoT Edge för att hantera grupper av enheter baserat på delade taggar
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8aaac6100ba980301ff3e85a3ac3959bfee89b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76895962"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Förstå IoT Edge automatiska distributioner för enskilda enheter eller i stor skala

Automatiska distributioner och distribution i flera lager hjälper dig att hantera och konfigurera moduler på ett stort antal IoT Edge-enheter.

Azure IoT Edge innehåller två sätt att konfigurera modulerna så att de körs på IoT Edge-enheter. Den första metoden är att distribuera moduler per enhet. Du skapar ett distributionsmanifest och tillämpar det sedan på en viss enhet efter namn. Den andra metoden är att distribuera moduler automatiskt till alla registrerade enheter som uppfyller en uppsättning definierade villkor. Du skapar ett distributionsmanifest och definierar sedan vilka enheter det gäller för baserat på [taggar](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) i enhetstvillingen.

Den här artikeln fokuserar på att konfigurera och övervaka flottor av enheter, gemensamt kallade *IoT Edge automatiska distributioner*.De grundläggande distributionsstegen är följande:

1. En operatör definierar en distribution som beskriver en uppsättning moduler och målenheterna.Varje distribution har ett distributionsmanifest som återspeglar den här informationen.
2. IoT Hub-tjänsten kommunicerar med alla riktade enheter för att konfigurera dem med de deklarerade modulerna.
3. IoT Hub-tjänsten hämtar status från IoT Edge-enheterna och gör dem tillgängliga för operatören.En operatör kan till exempel se när en Edge-enhet inte har konfigurerats eller om en modul misslyckas under körning.
4. När som helst konfigureras nya IoT Edge-enheter som uppfyller målvillkoren för distributionen.

I den här artikeln beskrivs varje komponent som ingår i konfigurationen och övervakningen av en distribution. En genomgång av hur du skapar och uppdaterar en distribution finns i [Distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).

## <a name="deployment"></a>Distribution

En automatisk distribution i IoT Edge tilldelar IoT Edge-modulavbildningar att köras som instanser på en riktad uppsättning IoT Edge-enheter. Det fungerar genom att konfigurera ett IoT Edge-distributionsmanifest för att inkludera en lista över moduler med motsvarande initieringsparametrar.En distribution kan tilldelas en enda enhet (baserat på enhets-ID) eller till en grupp enheter (baserat på taggar).När en IoT Edge-enhet tar emot ett distributionsmanifest hämtas och installeras behållaravbildningarna från respektive behållardatabaser och konfigureras därefter.När en distribution har skapats kan en operatör övervaka distributionsstatusen för att se om målenheter är korrekt konfigurerade.

Endast IoT Edge-enheter kan konfigureras med en distribution. Följande förutsättningar måste finnas på enheten innan den kan ta emot distributionen:

* Basoperativsystemet
* Ett containerhanteringssystem, som Moby eller Docker
* Etablering av IoT Edge-körningen

### <a name="deployment-manifest"></a>Distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver de moduler som ska konfigureras på de riktade IoT Edge-enheterna. Den innehåller konfigurationsmetadata för alla moduler, inklusive de systemmoduler som krävs (särskilt IoT Edge-agenten och IoT Edge-hubben).  

Konfigurationsmetadata för varje modul innehåller:

* Version
* Typ
* Status (till exempel löpning eller stoppad)
* Omstartsprincip
* Avbildnings- och behållarregistret
* Vägar för in- och utdata

Om modulavbildningen lagras i ett privat behållarregister innehåller IoT Edge-agenten registerautentiseringsuppgifterna.

### <a name="target-condition"></a>Målvillkor

Målvillkoret utvärderas kontinuerligt under distributionens livstid. Alla nya enheter som uppfyller kraven ingår och alla befintliga enheter som inte längre gör tas bort. Distributionen återaktiveras om tjänsten upptäcker en ändring av målvillkoret.

Du har till exempel en distribution med en målvillkorstaggar.miljö = prod. När du startar distributionen finns det 10 produktionsenheter. Modulerna har installerats i dessa 10 enheter. IoT Edge-agentstatusen visar totalt 10 enheter, 10 lyckade svar, 0 felsvar och 0 väntande svar. Nu lägger du till ytterligare fem enheter med tags.environment = "prod". Tjänsten identifierar ändringen och IoT Edge-agentstatusen blir totalt 15 enheter, 10 lyckade svar, 0 felsvar och 5 väntande svar medan den distribueras till de fem nya enheterna.

Använd alla booleska villkor på enhetstvillingtaggar, enhetstvillingrapporterade egenskaper eller deviceId för att välja målenheter. Om du vill använda villkor med taggar måste du{} lägga till "taggar": avsnittet i enhetstvillingen under samma nivå som egenskaper. [Läs mer om taggar i enhetstvilling](../iot-hub/iot-hub-devguide-device-twins.md)

Exempel på målvillkor:

* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = "prod" OCH tags.location = 'westus'
* tags.environment = "prod" ELLER tags.location = 'westus'
* tags.operator = "John" OCH tags.environment = "prod" INTE deviceId = 'linuxprod1'
* properties.reported.devicemodel = '4000x'

Tänk på dessa begränsningar när du skapar ett målvillkor:

* I enhetstvillingen kan du bara skapa ett måltillstånd med hjälp av taggar, rapporterade egenskaper eller deviceId.
* Dubbla offerter är inte tillåtna i någon del av målvillkoret. Använd enstaka citattecken.
* Enstaka citattecken representerar värdena för målvillkoret. Därför måste du komma bort från det enda citatet med ett annat citat om det är en del av enhetens namn. Om du till exempel `operator'sDevice`vill `deviceId='operator''sDevice'`rikta in dig på en enhet som heter , skriv .
* Siffror, bokstäver och följande tecken tillåts i `-:.+%_#*?!(),=@;$`målvillkorsvärden: .

### <a name="priority"></a>Prioritet

En prioritet definierar om en distribution ska tillämpas på en riktad enhet i förhållande till andra distributioner. En distributionsprioritet är ett positivt heltal, med större tal som anger högre prioritet. Om en IoT Edge-enhet är måltavla för mer än en distribution gäller distributionen med högst prioritet.Distributioner med lägre prioritet tillämpas inte, och de slås inte heller samman.Om en enhet är inriktad med två eller flera distributioner med samma prioritet, gäller den senast skapade distributionen (som bestäms av tidsstämpeln för att skapa).

### <a name="labels"></a>Etiketter

Etiketter är strängnyckel/värdepar som du kan använda för att filtrera och gruppera distributioner.En distribution kan ha flera etiketter. Etiketter är valfria och påverkar inte den faktiska konfigurationen av IoT Edge-enheter.

### <a name="metrics"></a>Mått

Som standard rapporterar alla distributioner på fyra mått:

* **Inriktning** visar IoT Edge-enheter som matchar villkoret för inmatningsinriktning.
* **Tillämpad** visar de riktade IoT Edge-enheter som inte är inriktade på en annan distribution med högre prioritet.
* **Reporting Success** visar IoT Edge-enheter som har rapporterat att modulerna har distribuerats.
* **Rapporteringsfel** visar IoT Edge-enheter som har rapporterat att en eller flera moduler inte har distribuerats. Om du vill undersöka felet ytterligare ansluter du på distans till dessa enheter och visar loggfilerna.

Dessutom kan du definiera dina egna anpassade mått för att övervaka och hantera distributionen.

Mått ger sammanfattningar av de olika tillstånd som enheter kan rapportera tillbaka som ett resultat av att tillämpa en distributionskonfiguration. Mått kan fråga [edgeHub modul tvilling rapporterade egenskaper](module-edgeagent-edgehub.md#edgehub-reported-properties), som *lastDesiredStatus* eller *lastConnectTime*. Ett exempel:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Det är valfritt att lägga till egna mått och påverkar inte den faktiska konfigurationen av IoT Edge-enheter.

## <a name="layered-deployment"></a>Distribution i lager

Distributioner i flera lager är automatiska distributioner som kan kombineras för att minska antalet unika distributioner som behöver skapas. Distributioner i lager är användbara i scenarier där samma moduler återanvänds i olika kombinationer i många automatiska distributioner.

Distributioner i lager har samma grundläggande komponenter som alla automatiska distributioner. De riktar sig till enheter baserat på taggar i enhetstvillingarna och ger samma funktioner kring etiketter, mått och statusrapportering. Distributioner i flera lager har också tilldelats prioriteter för dem, men i stället för att använda prioriteten för att avgöra vilken distribution som tillämpas på en enhet avgör prioriteten hur flera distributioner rankas på en enhet. Om till exempel två distributioner i flera lager har en modul eller en väg med samma namn, tillämpas den lagerförda distributionen med högre prioritet medan den lägre prioriteten skrivs över.

Systemkörningsmodulerna edgeAgent och edgeHub är inte konfigurerade som en del av en distribution i flera lager. Alla IoT Edge-enheter som är inriktade på en distribution i flera lager behöver en automatisk standarddistribution som tillämpas på den först. Den automatiska distributionen ger basen på vilken lagerdistributioner kan läggas till.

En IoT Edge-enhet kan använda en och endast en vanlig automatisk distribution, men den kan använda flera automatiska distributioner i flera lager. Alla distributioner i flera lager som är inriktade på en enhet måste ha högre prioritet än den automatiska distributionen för den enheten.

Tänk till exempel på följande scenario för ett företag som hanterar byggnader. De utvecklade IoT Edge-moduler för att samla in data från övervakningskameror, rörelsesensorer och hissar. Men inte alla deras byggnader kan använda alla tre moduler. Med automatiska standarddistributioner måste företaget skapa enskilda distributioner för alla modulkombinationer som deras byggnader behöver.

![Standard automatiska distributioner måste rymma varje modulkombination](./media/module-deployment-monitoring/standard-deployment.png)

Men när företaget växlar till lager automatiska distributioner de upptäcker att de kan skapa samma modul kombinationer för sina byggnader med färre distributioner att hantera. Varje modul har en egen distribution i flera lager och enhetstaggarna identifierar vilka moduler som läggs till i varje byggnad.

![Automatiska distributioner i flera lager förenklar scenarier där samma moduler kombineras på olika sätt](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Konfiguration av modultvilling

När du arbetar med distributioner i flera lager kan du avsiktligt eller på annat sätt ha två distributioner med samma modul som riktar sig till en enhet. I sådana fall kan du bestämma om distributionen med högre prioritet ska skriva över modultvillingen eller lägga till den. Du kan till exempel ha en distribution som använder samma modul på 100 olika enheter. 10 av dessa enheter är dock i säkra anläggningar och behöver ytterligare konfiguration för att kommunicera via proxyservrar. Du kan använda en distribution i flera lager för att lägga till modultvillingegenskaper som gör att dessa 10 enheter kan kommunicera säkert utan att skriva över den befintliga modultvillinginformationen från basdistributionen.

Du kan lägga till modultvilling önskade egenskaper i distributionsmanifestet. Var i en standarddistribution lägger du till egenskaper i avsnittet **properties.desired** i modultvillingen, i en distribution i flera lager kan du deklarera en ny delmängd av önskade egenskaper.

I en standarddistribution kan du till exempel lägga till den simulerade temperatursensormodulen med följande önskade egenskaper som talar om för den att skicka data med 5 sekunders intervall:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

I en distribution i flera lager som riktar sig till vissa eller alla av samma enheter kan du lägga till en egenskap som talar om för den simulerade sensorn att skicka 1 000 meddelanden och sedan stoppa. Du vill inte skriva över de befintliga egenskaperna, så du skapar ett `layeredProperties`nytt avsnitt i de önskade egenskaperna som kallas , som innehåller den nya egenskapen:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

En enhet som har båda distributionerna tillämpade återspeglar följande egenskaper i modultvillingen för den simulerade temperatursensorn:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Om du anger `properties.desired` fältet för modultvillingen i en distribution i flera lager, skrivs de önskade egenskaperna för modulen över i alla distributioner med lägre prioritet.

## <a name="phased-rollout"></a>Fasutrullning

En stegvis utrullning är en övergripande process där en operatör distribuerar ändringar till en breddning av IoT Edge-enheter. Målet är att göra ändringar gradvis för att minska risken för att göra storskaliga bryta förändringar. Automatiska distributioner hjälper till att hantera stegvisa distributioner över en flotta av IoT Edge-enheter.

En stegvis distribution utförs i följande faser och steg:

1. Upprätta en testmiljö för IoT Edge-enheter genom att `tag.environment='test'`etablera dem och ange en enhetstvillingtagg som .Testmiljön ska spegla den produktionsmiljö som distributionen så småningom kommer att inriktas på.
2. Skapa en distribution med önskade moduler och konfigurationer. Målvillkoret bör inriktas på testmiljön för IoT Edge-enheten.
3. Verifiera den nya modulkonfigurationen i testmiljön.
4. Uppdatera distributionen så att den innehåller en delmängd av IoT Edge-enheter genom att lägga till en ny tagg i målvillkoret. Se också till att prioriteten för distributionen är högre än andra distributioner som för närvarande är inriktade på dessa enheter
5. Kontrollera att distributionen lyckades på de riktade IoT-enheterna genom att visa distributionsstatusen.
6. Uppdatera distributionen så att den inriktar alla återstående IoT Edge-enheter för produktion.

## <a name="rollback"></a>Rollback

Distributioner kan återställas om du får fel eller felkonfigurationer.Eftersom en distribution definierar den absoluta modulkonfigurationen för en IoT Edge-enhet måste en ytterligare distribution också riktas till samma enhet med lägre prioritet även om målet är att ta bort alla moduler.  

Om du tar bort en distribution tas inte modulerna bort från målenheter. Det måste finnas en annan distribution som definierar en ny konfiguration för enheterna, även om det är en tom distribution.

Utför återställningar i följande ordning:

1. Bekräfta att en andra distribution också är inriktad på samma enhetsuppsättning. Om målet med återställningen är att ta bort alla moduler bör den andra distributionen inte innehålla några moduler.
2. Ändra eller ta bort målvillkorsuttrycket för distributionen som du vill återställa så att enheterna inte längre uppfyller målvillkoret.
3. Kontrollera att återställningen lyckades genom att visa distributionsstatus.
   * Den återställda distributionen bör inte längre visa status för de enheter som återställdes.
   * Den andra distributionen bör nu innehålla distributionsstatus för de enheter som återställdes.

## <a name="next-steps"></a>Nästa steg

* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [Distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
* Läs mer om andra IoT Edge-begrepp som [IoT Edge-körningen](iot-edge-runtime.md) och [IoT Edge-modulerna](iot-edge-modules.md).
