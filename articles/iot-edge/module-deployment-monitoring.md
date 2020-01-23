---
title: Automatisk distribution för enhetsgrupper – Azure IoT Edge | Microsoft Docs
description: Använda automatisk distributioner i Azure IoT Edge för att hantera grupper av enheter baserat på delade taggar
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 406830add1891a058e9b43fccb8435aa4d339ed0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548687"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Förstå IoT Edge automatiska distributioner för enskilda enheter eller i stor skala

Automatiska distributioner och lager distribution hjälper dig att hantera och konfigurera moduler på ett stort antal IoT Edge enheter.

Azure IoT Edge tillhandahåller två sätt att konfigurera modulerna så att de körs på IoT Edge enheter. Den första metoden är att distribuera moduler per enhet. Du skapar ett distributions manifest och tillämpar det på en viss enhet efter namn. Den andra metoden är att distribuera moduler automatiskt till alla registrerade enheter som uppfyller en uppsättning definierade villkor. Du skapar ett distributions manifest och definierar sedan vilka enheter den gäller baserat på [taggar](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) i enheten.

Den här artikeln fokuserar på att konfigurera och övervaka flotta av enheter, gemensamt kallade *IoT Edge automatiska distributioner*. De grundläggande distributions stegen är följande:

1. En operator definierar en distribution som beskriver en uppsättning moduler och mål enheter. Varje distribution har ett distributions manifest som motsvarar den här informationen.
2. Tjänsten IoT Hub kommunicerar med alla riktade enheter för att konfigurera dem med de deklarerade modulerna.
3. IoT Hub-tjänsten hämtar status från IoT Edge-enheter och gör dem tillgängliga för operatorn.  En operatör kan till exempel se när en Edge-enhet inte har kon figurer ATS korrekt eller om en modul Miss lyckas under körningen.
4. Nya IoT Edge-enheter som uppfyller villkor som Sök mål har konfigurerats för distributionen när som helst.

Den här artikeln beskriver varje komponent som ingår i Konfigurera och övervaka en distribution. En genomgång för att skapa och uppdatera en distribution finns i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).

## <a name="deployment"></a>Distribution

En automatisk IoT Edge-distribution tilldelar IoT Edge modulen bilder för att köra som instanser på en riktad uppsättning IoT Edge-enheter. Det fungerar genom att konfigurera ett manifest för IoT Edge-distribution med en lista med moduler med motsvarande initieringsparametrar. En distribution kan tilldelas en enda enhet (baserat på enhets-ID) eller till en grupp av enheter (baserat på taggar). När en IoT Edge enhet tar emot ett distributions manifest laddas och installeras behållar avbildningarna från respektive behållares databaser och konfigurerar dem därefter. När en distribution har skapats kan en operatör övervaka distributions statusen för att se om de riktade enheterna är korrekt konfigurerade.

IoT Edge-enheter kan konfigureras med en distribution. Följande krav måste vara på enheten innan den kan ta emot distributionen:

* Det grundläggande operativsystemet
* Ett system för behållaren, t.ex. Moby eller Docker
* Etablering av IoT Edge-körningen

### <a name="deployment-manifest"></a>Distributionsmanifest

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler som ska konfigureras på de aktuella IoT Edge-enheterna. Den innehåller konfigurationsmetadata för alla moduler, inklusive de nödvändiga systemmoduler (särskilt IoT Edge-agenten och IoT Edge hub).  

Av konfigurationsmetadata för varje modul innehåller:

* Version
* Typ
* Status (till exempel igång eller stoppad)
* Starta om princip
* Bild- och container registry
* Vägar för data som indata och utdata

Om modulen avbildningen lagras i ett privat behållarregister, har IoT Edge-agenten autentiseringsuppgifter för registret.

### <a name="target-condition"></a>Målvillkor

Mål villkoret utvärderas kontinuerligt under hela livs längden för distributionen. Alla nya enheter som uppfyller kraven ingår och alla befintliga enheter som inte längre gör tas bort. Distributionen återaktiveras om tjänsten identifierar ändringar target villkor.

Du kan till exempel ha en distribution med en Target condition-taggar. miljö = ' Prod '. När du sätta igång distributionen finns 10 produktionsenheter. Modulerna som har installerats i dessa 10 enheter. IoT Edge agent status visar totalt 10 enheter, 10 lyckade svar, 0 felsvar och 0 väntande svar. Nu lägger du till fem fler enheter med tags.environment = 'prod'. Tjänsten identifierar ändringen och IoT Edge agentens status blir 15 enheter, 10 lyckade svar, 0 felsvar och 5 väntande svar när den distribueras till de fem nya enheterna.

Använd valfritt booleskt villkor på device twins taggar eller deviceId för att välja målenheter. Om du vill använda villkoret med taggar kan du behöva lägga till ”taggar”:{} avsnittet i enhetstvillingen under samma nivå som egenskaper. [Mer information om taggar i enhetstvilling](../iot-hub/iot-hub-devguide-device-twins.md)

Exempel på mål villkor:

* deviceId = 'linuxprod1'
* Tags.Environment = 'prod'
* Tags.Environment = prod och tags.location = 'westus'
* Tags.Environment = prod eller tags.location = 'westus'
* Tags.operator = ”John” och tags.environment = prod inte deviceId = 'linuxprod1'

Här följer några avgränsar när du skapar en Målvillkor:

* Du kan bara skapa en Målvillkor med hjälp av taggar eller deviceId i enhetstvillingen.
* Dubbla citattecken tillåts inte i någon del av målvillkoret. Använd enkla citattecken.
* Enkla citattecken representerar värden för målvillkoret. Du måste därför escape enkelt citattecken med ett annat enkelt citattecken om det är en del av namnet på enheten. Till exempel att fokusera på en enhet med namnet `operator'sDevice`, skriva `deviceId='operator''sDevice'`.
* Siffror, bokstäver och följande tecken är tillåtna i villkoret målvärden: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Prioritet

En prioritet definierar om en distribution ska tillämpas på en målenhet i förhållande till andra distributioner. Prioritetsvärdet distribution är ett positivt heltal med större siffra anger högre prioritet. Om en IoT Edge-enhet är målet för fler än en distribution, gäller distributionen med högst prioritet.  Distributioner med lägre prioritet tillämpas inte och slås inte samman.  Om en enhet är riktad mot två eller flera distributioner med samma prioritet, gäller den senast skapade distributionen (bestäms av tids stämplingen för skapandet).

### <a name="labels"></a>Etiketter

Etiketter är sträng nyckel/värdepar som du kan använda för att filtrera och gruppera distributioner. En distribution kan ha flera etiketter. Etiketter är valfria och påverkar inte den faktiska konfigurationen för IoT Edge enheter.

### <a name="metrics"></a>Mått

Som standard rapporterar alla distributioner på fyra mått:

* **Riktad** visar de IoT Edge enheter som matchar villkoret för distributions målet.
* **Tillämpad** visar riktade IoT Edge enheter som inte är riktade till en annan distribution med högre prioritet.
* **Rapporten lyckades** visar de IoT Edge enheter som har rapporterat tillbaka till tjänsten att modulerna har distribuerats korrekt.
* **Rapporterings fel** visar de IoT Edge enheter som har rapporterat tillbaka till tjänsten att en eller flera moduler inte har distribuerats korrekt. För att undersöka felet, fjärransluta till dessa enheter och visa loggfilerna.

Du kan också definiera egna anpassade mått som hjälper dig att övervaka och hantera distributionen.

Mått tillhandahåller sammanfattande antal i de olika tillstånd som enheterna kan rapportera tillbaka till följd av att en distributions konfiguration har tillämpats. Mått kan fråga [edgeHub-modulens dubbla rapporterade egenskaper](module-edgeagent-edgehub.md#edgehub-reported-properties), t. ex. *lastDesiredStatus* eller *lastConnectTime*. Ett exempel:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Att lägga till egna mått är valfritt och påverkar inte den faktiska konfigurationen för IoT Edge enheter.

## <a name="layered-deployment"></a>Lager distribution

Lager distributioner är automatiska distributioner som kan kombineras tillsammans för att minska antalet unika distributioner som behöver skapas. Lager distributioner är användbara i scenarier där samma moduler återanvänds i olika kombinationer i många automatiska distributioner.

Lager distributioner har samma grundläggande komponenter som vilken automatisk distribution som helst. De riktar in enheter baserat på taggar i enheten är dubbla och ger samma funktioner kring etiketter, mått och status rapportering. Lager distributioner har också tilldelade prioriteter, men i stället för att använda prioritet för att avgöra vilken distribution som tillämpas på en enhet, avgör prioriteten hur flera distributioner rangordnas på en enhet. Om två lager distributioner till exempel har en modul eller en väg med samma namn, tillämpas den skiktade distributionen med högre prioritet medan den lägre prioriteten skrivs över.

System runtime-modulerna, edgeAgent och edgeHub är inte konfigurerade som en del av en lager distribution. Alla IoT Edge enheter som är riktade mot en lager distribution behöver en standard automatisk distribution som tillämpas först för att tillhandahålla den bas som lager distributioner kan läggas till i.

En IoT Edge enhet kan bara använda en och endast en automatisk automatisk distribution, men den kan tillämpa flera lager automatiska distributioner. Alla lager distributioner som riktas mot en enhet måste ha en högre prioritet än den automatiska distributionen av enheten.

Anta till exempel följande scenario för ett företag som hanterar byggnader. De utvecklade IoT Edge modulerna för att samla in data från säkerhets kameror, rörelse sensorer och hissar. Alla deras byggnader kan dock inte använda alla tre modulerna. Med standard automatiska distributioner måste företaget skapa enskilda distributioner för alla kombinationer av moduler som deras byggnader behöver.

![Vanliga automatiska distributioner måste anpassas till alla moduler](./media/module-deployment-monitoring/standard-deployment.png)

Men när företaget växlar till lager automatiska distributioner upptäcker de att de kan skapa samma kombinationer för sina byggnader med färre distributioner som ska hanteras. Varje modul har sin egen lager distribution och enhets taggarna identifierar vilka moduler som läggs till i varje byggnad.

![Lager automatiska distributioner fören klar scenarier där samma moduler kombineras på olika sätt](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Modul, dubbel konfiguration

När du arbetar med lager distributioner kan du, avsiktligt eller på annat sätt, ha två distributioner med samma modul som är riktad mot en enhet. I dessa fall kan du bestämma om distributionen av högre prioritet ska skriva över modulen med dubbla eller lägga till den. Du kan till exempel ha en distribution som använder samma modul för att 100 olika enheter. Men 10 av enheterna finns på säkra anläggningar och behöver ytterligare konfiguration för att kunna kommunicera via proxyservrar. Du kan använda en lager distribution för att lägga till modulens dubbla egenskaper som gör det möjligt för de 10 enheterna att kommunicera säkert utan att skriva över den befintliga modulen dubbel information från bas distributionen.

Du kan lägga till modul dubbla önskade egenskaper i distributions manifestet. I en standard distribution kan du lägga till egenskaper i **egenskaperna. önskat** avsnitt i modulen, i en lager distribution kan du deklarera en ny delmängd av önskade egenskaper.

I en standard distribution kan du till exempel lägga till modulen simulerad temperatur sensor med följande önskade egenskaper som instruerar den att skicka data med 5 sekunders intervall:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

I en lager distribution som riktar sig till samma enheter, eller en delmängd av samma enheter, kanske du vill lägga till ytterligare en egenskap som anger att den simulerade sensorn skickar 1000 meddelanden och sedan stoppar. Du vill inte skriva över de befintliga egenskaperna, så du skapar ett nytt avsnitt i önskade egenskaper som kallas `layeredProperties`, som innehåller den nya egenskapen:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

En enhet som har båda distributioner tillämpat kommer att avspegla följande egenskaper i modulen för den simulerade temperatur sensorn:

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

Om du ställer in `properties.desired` fältet i modulen dubbla i en lager distribution skrivs de önskade egenskaperna för modulen över i alla distributioner med lägre prioritet.

## <a name="phased-rollout"></a>Stegvis distribution

En stegvis distribution är ett övergripande processen där en operatör distribuerar ändringar till en breddat uppsättning IoT Edge-enheter. Målet är att göra ändringar gradvis för att minska risken för att göra många skala större ändringar. Automatiska distributioner hjälper till att hantera stegvisa distributioner över en flotta av IoT Edge enheter.

En stegvis distribution körs i följande faser och steg:

1. Upprätta en testmiljö för IoT Edge-enheter genom att etablera dem och ställa in en enhetstagg twin som `tag.environment='test'`. Test miljön bör spegla produktions miljön som distributionen kommer att ta slut.
2. Skapa en distribution, inklusive de önskade moduler och konfigurationer. Sök mål villkoret ska rikta testet miljö för IoT Edge-enhet.
3. Verifiera modulkonfigurationen för nya i testmiljön.
4. Uppdatera distribution för att inkludera en delmängd av produktion IoT Edge-enheter genom att lägga till en ny tagg målobjekt villkoret. Kontrollera också att prioriteten för distributionen är högre än andra distributioner riktat till enheterna
5. Kontrollera att distributionen har utförts på den aktuella IoT-enheter genom att granska distributionsstatusen.
6. Uppdatera distributionen för att rikta in alla återstående produktion IoT Edge-enheter.

## <a name="rollback"></a>Återtagande

Distributioner kan återkallas om du får fel eller felaktiga konfigurationer. Eftersom en distribution definierar den absoluta modul konfigurationen för en IoT Edge enhet måste även en ytterligare distribution vara riktad till samma enhet med lägre prioritet även om målet är att ta bort alla moduler.  

Om du tar bort en distribution tas inte modulerna bort från riktade enheter. Det måste finnas en annan distribution som definierar en ny konfiguration för enheterna, även om det är en tom distribution.

Utföra återställningar i följande ordning:

1. Bekräfta att en andra distribution också är inriktad på samma enhetsuppsättning. Om målet för återställningen är att ta bort alla moduler måste får den andra distributionen inte innehålla alla moduler.
2. Ändra eller ta bort villkorsuttrycket mål för distributionen som du vill återställa så att enheterna som inte längre uppfyller villkoret målobjekt.
3. Kontrollera att återställningen har utförts genom att granska distributionsstatusen.
   * Återställas tillbaka distributionen bör inte längre visa status för enheter som har återställts.
   * Den andra distributionen ska nu innehålla Distributionsstatus för de enheter som har återställts.

## <a name="next-steps"></a>Nästa steg

* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
* Mer information om andra IoT Edge-begrepp som den [IoT Edge-körningen](iot-edge-runtime.md) och [IoT Edge-moduler](iot-edge-modules.md).
