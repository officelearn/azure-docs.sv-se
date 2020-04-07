---
title: Implementera ioT Plug and Play Preview modell upptäckt | Microsoft-dokument
description: Som lösningsutvecklare kan du läsa om hur du kan implementera identifiering av IoT Plug and Play-modeller i din lösning.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 94e5e6fb826528aab1a358d44cb51bfcd6219032
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673567"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementera identifiering av IoT Plug and Play Preview-modeller i en IoT-lösning

I den här artikeln beskrivs hur du som lösningsutvecklare kan implementera ioT Plug and Play Preview-modellidentifiering i en IoT-lösning.  Identifiering av IoT Plug and Play-modeller är hur IoT Plug and Play-enheter identifierar sina kapacitetsmodeller och gränssnitt som stöds och hur en IoT-lösning hämtar dessa kapacitetsmodeller och gränssnitt.

Det finns två breda kategorier av IoT-lösningar: specialbyggda lösningar som fungerar med en känd uppsättning IoT Plug and Play-enheter och modelldrivna lösningar som fungerar med alla IoT Plug and Play-enheter.

Den här konceptartikeln beskriver hur du implementerar modellidentifiering i båda typerna av lösningar.

## <a name="model-discovery"></a>Modellidentifiering

När en IoT Plug and Play-enhet först ansluter till din IoT-hubb skickas ett telemetrimeddelande för modellinformation. Det här meddelandet innehåller ID:n för de gränssnitt som enheten implementerar. För att din lösning ska fungera med enheten måste den lösa dessa ID:n och hämta definitionerna för varje gränssnitt.

Här är stegen som en IoT Plug and Play-enhet tar när den använder DPS (Device Provisioning Service) för att ansluta till ett nav:

1. När enheten är påslagen ansluter den till den globala slutpunkten för DPS och autentiserar med en av de tillåtna metoderna.
1. DPS autentiserar sedan enheten och slår upp regeln som talar om vilken IoT-hubb som enheten ska tilldelas. DPS registrerar sedan enheten med det navet.
1. DPS returnerar en IoT Hub-anslutningssträng till enheten.
1. Enheten skickar sedan ett identifieringstelemetrimeddelande till din IoT Hub. Meddelandet identifieringstelemetri innehåller ID:n för de gränssnitt som enheten implementerar.
1. IoT Plug and Play-enheten är nu redo att fungera med en lösning som använder din IoT-hubb.

Om enheten ansluter direkt till din IoT-hubb ansluts den med en anslutningssträng som är inbäddad i enhetskoden. Enheten skickar sedan ett identifieringstelemetrimeddelande till din IoT Hub.

Mer information om telemetrimeddelandet för [modellinformation](concepts-common-interfaces.md) finns i ModelInformation-gränssnittet.

### <a name="purpose-built-iot-solutions"></a>Specialbyggda IoT-lösningar

En specialbyggd IoT-lösning fungerar med en känd uppsättning IoT Plug and Play-enhetskapacitetsmodeller och gränssnitt.

Du har kapacitetsmodellen och gränssnitten för de enheter som ansluter till din lösning i förväg. Använd följande steg för att förbereda din lösning:

1. Lagra gränssnittet JSON filer i en [modell lagringsplats](./howto-manage-models.md) där din lösning kan läsa dem.
1. Skriv logik i din IoT-lösning baserat på de förväntade IoT Plug and Play-kapacitetsmodellerna och gränssnittet.
1. Prenumerera på meddelanden från IoT-hubben som lösningen använder.

NÃ¤r du får ett meddelande fÃ¶r en ny enhetsanslutning sÃ¥ fÃ¶rsÃ¥1:

1. Läs meddelandet om identifieringstelemetri för att hämta ID:n för kapacitetsmodellen och gränssnitten som implementeras av enheten.
1. Jämför ID för kapacitetsmodellen med ID:na för de kapacitetsmodeller som du har lagrat i förväg.
1. Nu vet du vilken typ av enhet som har anslutit. Använd logiken du skrev tidigare för att göra det möjligt för användare att interagera med enheten på rätt sätt.

### <a name="model-driven-solutions"></a>Modelldrivna lösningar

En modelldriven IoT-lösning kan fungera med alla IoT Plug and Play-enheter. Att bygga en modelldriven IoT-lösning är mer komplext, men fördelen är att din lösning fungerar med alla enheter som läggs till i framtiden.

Om du vill skapa en modelldriven IoT-lösning måste du skapa logik mot IoT Plug and Play-gränssnittsprimiterna: telemetri, egenskaper och kommandon. IoT-lösningens logik representerar en enhet genom att kombinera flera telemetri-, egenskaps- och kommandofunktioner.

Din lösning måste också prenumerera på meddelanden från IoT-hubben som används.

När lösningen får ett meddelande om en ny enhetsanslutning gör du så här:

1. Läs meddelandet om identifieringstelemetri för att hämta ID:n för kapacitetsmodellen och gränssnitten som implementeras av enheten.
1. För varje ID läser du den fullständiga JSON-filen för att hitta enhetens funktioner.
1. Kontrollera om varje gränssnitt finns i några cacheminnen som du har skapat för att lagra JSON-filer som hämtats tidigare av din lösning.
1. Kontrollera sedan om det finns ett gränssnitt med det ID:et i den offentliga modelldatabasen. Mer information finns i [Offentlig modelldatabas](howto-manage-models.md).
1. Om gränssnittet inte finns i den offentliga modelldatabasen kan du försöka leta efter det i alla företagsmodelldatabaser som är kända för din lösning. Du behöver en anslutningssträng för att komma åt en företagsmodelldatabas. Mer information finns i [Transaktionsmodelldatabasen](howto-manage-models.md).
1. Om du inte hittar alla gränssnitt i antingen den offentliga modelldatabasen eller i en företagsmodelldatabas kan du kontrollera om enheten kan tillhandahålla gränssnittsdefinitionen. En enhet kan implementera [standardgränssnittet ModelDefinition](concepts-common-interfaces.md) för att publicera information om hur du hämtar gränssnittsfiler med ett kommando.
1. Om du hittade JSON-filer för varje gränssnitt som implementerats av enheten kan du räkna upp enhetens funktioner. Använd logiken du skrev tidigare för att göra det möjligt för användare att interagera med enheten.
1. När som helst kan du anropa API:et för digitala tvillingar för att hämta enhetens kapacitetsmodell-ID och gränssnitts-ID.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om modellidentifiering en IoT-lösning kan du läsa mer om [Azure IoT-plattformen](overview-iot-plug-and-play.md) för att utnyttja andra funktioner för din lösning.
