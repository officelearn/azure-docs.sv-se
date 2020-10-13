---
title: Betrodda program som Azure IoT Edge moduler
description: Använd Open enklaven SDK och API för att skriva betrodda program och distribuera dem som IoT Edge moduler för konfidentiell dator användning
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: kgremban
ms.openlocfilehash: d81cc6c94c04c683362fd12cd6777c304a4b0a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361984"
---
# <a name="confidential-computing-at-the-edge"></a>Konfidentiell data behandling vid gränsen

Azure IoT Edge stöder betrodda program som körs i säkra enclaves på enheten. Kryptering ger säkerhet för data under överföring eller i vila, men enclaves tillhandahåller säkerhet för data och arbets belastningar när de används. IoT Edge stöder Open enklaven som standard för att utveckla betrodda program.

Säkerhet har alltid varit ett viktigt fokus i Sakernas Internet (IoT) eftersom det ofta finns IoT-enheter i världen i stället för att säkras i en privat anläggning. Den här exponeringen innebär att enheter riskerar att manipuleras och förfalskning eftersom de är fysiskt tillgängliga för dåliga aktörer. IoT Edge-enheter har ännu fler behov av förtroende och integritet eftersom de tillåter att känsliga arbets belastningar körs vid gränsen. Till skillnad från vanliga sensorer och motstånd är dessa intelligenta gräns enheter potentiellt utsatta för känsliga arbets belastningar som tidigare bara kördes i skyddade moln eller lokala miljöer.

[IoT Edge Security Manager](iot-edge-security-manager.md) åtgärdar en del av den konfidentiella data behandlings utmaningen. Säkerhets hanteraren använder en maskinvaru-säkerhetsmodul (HSM) för att skydda identitets arbets belastningar och pågående processer för en IoT Edge enhet.

En annan aspekt av konfidentiell data behandling är att skydda de data som används i gränsen. En *betrodd körnings miljö* (tee) är en säker, isolerad miljö på en processor och kallas ibland för en *enklaven*. Ett *betrott program* är ett program som körs i en enklaven. På grund av enclaves är betrodda program skyddade från andra appar som körs i huvud processorn eller i TEE.

## <a name="trusted-applications-on-iot-edge"></a>Betrodda program på IoT Edge

Betrodda program krypteras vid överföring och i vila och endast dekrypteras för körning i en betrodd körnings miljö. Den här standarden är sann för betrodda program som distribueras som IoT Edge moduler.

Utvecklaren skapar det betrodda programmet och paketerar det som en IoT Edge-modul. Programmet krypteras innan det flyttas till behållar registret. Programmet förblir krypterat under IoT Edge distributions processen tills modulen startas på IoT Edge enheten. När det betrodda programmet finns i enhetens TEE dekrypteras det och kan börja köras.

![Diagram – betrodda program krypteras i IoT Edge moduler tills de distribueras till den säkra enklaven](./media/deploy-trusted-applications/trusted-applications-encrypted.png)

Betrodda program på IoT Edge är ett logiskt tillägg till [Azures konfidentiella data behandling](../confidential-computing/overview.md). Arbets belastningar som körs inom säker enclaves i molnet kan också distribueras för att köras inom säker enclaves vid gränsen.

## <a name="open-enclave"></a>Öppna enklaven

[Open ENKLAVEN SDK](https://openenclave.io/sdk/) är ett projekt med öppen källkod som hjälper utvecklare att skapa betrodda program för flera plattformar och miljöer. Open enklaven SDK fungerar inom en enhets betrodda körnings miljö, medan det öppna enklaven-API: et fungerar som ett gränssnitt mellan TEE och den icke-TEE bearbetnings miljön.

Open enklaven stöder flera maskinvaruplattformar. IoT Edge-stöd för enclaves kräver för närvarande Open Portable TEE-operativsystemet (OP-TEE OS). Läs mer i [Öppna ENKLAVEN SDK för OP-tee OS](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

Den öppna enklaven-lagringsplatsen innehåller också exempel som hjälper utvecklare att komma igång. Om du vill ha mer information väljer du någon av de inledande artiklarna:

* [Skapa öppna enklaven SDK-exempel på Linux](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Skapa öppna enklaven SDK-exempel i Windows](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Maskinvara

För närvarande är den enda enhet som stöds med [Scalys](https://scalys.com/trustbox-industrial/) för att distribuera betrodda program som IoT Edge moduler. Förvaltaren är byggd på sidan Edge-och TRUSTe-EdgeXL, både i förväg inläst med Open enklaven SDK och Azure IoT Edge.

Mer information finns i [komma igång med Open enklaven för Scalys-förtroendet](https://aka.ms/scalys-trustbox-edge-get-started).

## <a name="develop-and-deploy"></a>Utveckla och distribuera

När du är redo att utveckla och distribuera ett betrott program kan du använda [Microsoft Open enklaven](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) -tillägget för Visual Studio Code. Du kan använda antingen Linux eller Windows som utvecklings dator för att utveckla moduler för domänen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du börjar utveckla betrodda program som IoT Edge moduler med [Open enklaven-tillägget för Visual Studio Code](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)
