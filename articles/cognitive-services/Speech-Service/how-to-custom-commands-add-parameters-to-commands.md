---
title: 'Gör så här: lägga till enkla kommandon i anpassade kommandon program i tal tjänsten'
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du lägger till parametrar till anpassade kommandon
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d2a14a501ebcf0913804ce39019a3fa4018ca141
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362381"
---
# <a name="add-parameters-to-commands"></a>Lägga till parametrar till kommandon

I den här artikeln får du lära dig hur du lägger till parametrar till anpassade kommandon. Parametrar är information som krävs av kommandona för att slutföra en uppgift. I komplexa scenarier kan parametrar också användas för att definiera villkor som utlöser anpassade åtgärder.

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * [Så här: skapa program med enkla kommandon](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>Konfigurera parametrar för kommandot TurnOn

Redigera det befintliga **TurnON** -kommandot för att aktivera och inaktivera flera enheter.

1. Eftersom kommandot nu kommer att hantera både på-och-av-scenariot byter du namn på kommandot till **TurnOnOff**.
   1. I det vänstra fönstret väljer du kommandot **TurnON** och väljer sedan knappen med tre punkter (...) bredvid **nytt kommando** överst i fönstret.
   
   1. Välj **Byt namn**. Ändra **namnet** till **TurnOnOff**i **namn på kommando** fönster.

1. Sedan lägger du till en ny parameter i det här kommandot som anger om användaren vill aktivera eller inaktivera enheten.
   1. Välj **Lägg till** finns överst i den mellersta rutan. Välj **parameter**i list rutan.
   1. I den högra rutan, i avsnittet **parametrar** , lägger du till värdet i rutan **namn** som **mikrofonen**.
   1. Välj **obligatoriskt**. I fönstret **Lägg till svar för en obligatorisk parameter** väljer du **enkel redigerare**. I den **första varianten**lägger du till
        ```
        On or Off?
        ```
   1. Välj **Uppdatera**.

       > [!div class="mx-imgBorder"]
       > ![Skapa nödvändigt parameter svar](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Nu konfigurerar vi parameter egenskaperna. Förklaringar av alla konfigurations egenskaper för ett kommando finns i [referenser](./custom-commands-references.md). Konfigurera resten av egenskaperna för parametern enligt följande:
      

       | Konfiguration      | Föreslaget värde     | Beskrivning                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Name               | `OnOff`           | Ett beskrivande namn för parametern                                                                           |
       | Är global          | avmarkerat       | Kryss ruta som anger om ett värde för den här parametern används globalt för alla kommandon i programmet|
       | Obligatorisk           | analysera         | Kryss ruta som anger om ett värde för den här parametern krävs innan kommandot slutförs |
       | Svar för obligatorisk parameter      |Enkel redigerare >`On or Off?`      | En uppmaning om att fråga efter värdet för den här parametern när den inte är känd |
       | Typ               | Sträng          | Typ av parameter, till exempel Number, String, datum tid eller geografi   |
       | Konfiguration      | Acceptera fördefinierade indatavärden från intern katalog | För strängar begränsar detta indata till en uppsättning möjliga värden |
       | Fördefinierade indatavärden     | `on`, `off`           | Uppsättning möjliga värden och deras alias         |
       
        
   1. Om du vill lägga till fördefinierade indatavärden väljer du **Lägg till ett fördefinierat indata** och i fönstret **nytt objekt** , skriver in **namnet** enligt vad som anges i tabellen ovan. I det här fallet använder vi inte alias, så du kan lämna det tomt. 
    > [!div class="mx-imgBorder"]
        > ![Skapa parameter](media/custom-commands/create-on-off-parameter.png)
   1. Välj **Spara** för att spara alla konfigurationer för parametern.
 
 ### <a name="add-subjectdevice-parameter"></a>Lägg till SubjectDevice-parameter 

   1. Välj sedan **Lägg till** igen för att lägga till en andra parameter för att representera namnet på de enheter som kan styras med det här kommandot. Använd följande konfiguration.
   

       | Inställningen            | Föreslaget värde       |
       | ------------------ | --------------------- |
       | Namn               | `SubjectDevice`         |
       | Är global          | avmarkerat             |
       | Obligatorisk           | analysera               |
       | Svar för obligatorisk parameter     | Enkel redigerare >`Which device do you want to control?`    | 
       | Typ               | Sträng                |          |
       | Konfiguration      | Acceptera fördefinierade indatavärden från intern katalog | 
       | Fördefinierade indatavärden | `tv`, `fan`               |
       | Alias ( `tv` )      | `television`, `telly`     |

   1. Välj **Spara**

### <a name="modify-example-sentences"></a>Ändra exempel meningar

För kommandon med parametrar är det bra att lägga till exempel meningar som beskriver alla möjliga kombinationer. Till exempel:

* Fullständig parameter information –`turn {OnOff} the {SubjectDevice}`
* Partiell parameter information –`turn it {OnOff}`
* Ingen parameter information –`turn something`

Exempel på meningar med olika typer av information gör att de anpassade kommandona kan matcha både ensidiga lösningar och flera lösningar med delvis information.

Med det i åtanke redigerar du exempel meningarna så att de använder parametrarna enligt rekommendationerna nedan:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Välj **Spara**.

> [!TIP]
> I exempel menings redigeraren använder du klammerparenteser för att referera till dina parametrar. - `turn {OnOff} the {SubjectDevice}`Använd fliken för automatisk komplettering som backas upp av parametrarna som skapats tidigare.

### <a name="modify-completion-rules-to-include-parameters"></a>Ändra regler för slut för ande för att inkludera parametrar

Ändra den befintliga **ConfirmationResponse**för slut för ande regeln.

1. I avsnittet **villkor** väljer du **Lägg till ett villkor**.
1. I fönstret **nytt villkor** väljer du **nödvändiga parametrar**i listan **typ** . I check listan nedan kontrollerar du både **mikrofonen** och **SubjectDevice**.
1. Välj **Skapa**.
1. I avsnittet **åtgärder** redigerar du den befintliga **svars åtgärden skicka tal** genom att hovra över åtgärden och välja knappen Redigera. Den här gången ska du använda de nyligen skapade **mikrofonen** -och **SubjectDevice** -parametrarna

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Välj **Spara**.

### <a name="try-it-out"></a>Prova nu
1. Välj **träna** ikon överst i den högra rutan.

1. När inlärningen är klar väljer du **test**. Ett **test av** programfönstret visas.
 Prova några interaktioner.

    - Inmatade: Stäng av TV
    - Utdata: OK, stänga av TV
    - Inmatade: Stäng av TV: n
    - Utdata: OK, stänga av TV
    - Inmatade: Stäng av
    - Utdata: vilken enhet vill du styra?
    - Inmatade: TV
    - Utdata: OK, stänga av TV

## <a name="configure-parameters-for-settemperature-command"></a>Konfigurera parametrar för kommandot SetTemperature

Ändra kommandot **SetTemperature** så att det kan ställa in temperaturen enligt användaren.

Lägg till ny parameter **temperatur** med följande konfiguration

| Konfiguration      | Föreslaget värde     |
| ------------------ | ----------------|
| Namn               | `Temperature`           |
| Obligatorisk           | analysera         |
| Svar för obligatorisk parameter      | Enkel redigerare >`What temperature would you like?`
| Typ               | Antal          |


Redigera exemplet yttranden till följande värden.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Redigera befintliga regler för slut för Ande enligt följande konfiguration.

| Konfiguration      | Föreslaget värde     |
| ------------------ | ----------------|
| Villkor         | Obligatorisk parameter > temperatur           |
| Åtgärder           | Skicka tal svar >`Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>Prova nu

**Träna** och **testa** ändringarna med några interaktioner.

- Inmatade: Ange temperatur
- Utdata: vilken temperatur vill du ha?
- Inmatade: 50 grader
- Utdata: OK, ställa in temperatur på 50 grader

## <a name="configure-parameters-to-the-setalarm-command"></a>Konfigurera parametrar för SetAlarm-kommandot

Lägg till en parameter med namnet **datetime** med följande konfiguration.

   | Inställningen                           | Föreslaget värde                     | 
   | --------------------------------- | ----------------------------------------|
   | Namn                              | `DateTime`                               |
   | Obligatorisk                          | analysera                                 |
   | Svar för obligatorisk parameter   | Enkel redigerare >`For what time?`            | 
   | Typ                              | DateTime                                |
   | Standardinställningar för datum                     | Om datumet saknas används idag            |
   | Standardvärden                     | Om tiden saknas i början av dagen     |


> [!NOTE]
> I den här artikeln har vi främst använt parameter typerna String, Number och DateTime. För en lista över alla parameter typer som stöds och deras egenskaper, gå till [referenser](./custom-commands-references.md).


Redigera exempel-yttranden till följande värden.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Redigera befintliga regler för slut för Ande enligt följande konfiguration.

   | Inställningen    | Föreslaget värde                               |
   | ---------- | ------------------------------------------------------- |
   | Åtgärder    | Skicka tal svar –`Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>Prova nu

**Träna** och **testa** ändringarna.
- Inmatade: Ställ in alarm i morgon kl. 12.00
- Utdata: OK, alarm set för 2020-05-02 12:00:00
- Inmatade: Ställ in ett larm
- Utdata: vilken tid?
- Inmatade: 17
- Utdata: OK, alarm set för 2020-05-01 17:00:00


## <a name="try-out-all-the-commands"></a>Prova alla kommandon

Testa alla tre kommandona tillsammans med yttranden som är relaterade till olika kommandon. Observera att du kan växla mellan olika kommandon.

- Inmatade: Ställ in ett larm
- Utdata: för vilken tid?
- Inmatade: slå på TV
- Utdata: OK, aktivera TV på
- Inmatade: Ställ in ett larm
- Utdata: för vilken tid?
- Inmatade: 17
- Utdata: OK, alarm set för 2020-05-01 17:00:00

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: lägga till konfigurationer i parametrar för kommandon](./how-to-custom-commands-add-parameter-configuration.md)
