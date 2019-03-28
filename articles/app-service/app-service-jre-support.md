---
title: Azure App Service för Windows Java Runtime-stöd
description: Det här avsnittet innehåller Java Runtime-instruktionen support för Azure App Service på Windows.
author: bmitchell287
ms.author: brendm; joshuapa
ms.date: 3/22/2019
ms.topic: article
ms.service: app-service
manager: dougE
ms.openlocfilehash: ab0e9b9f272108745b629d2cb284f6c5ab52c76f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523047"
---
# <a name="java-runtime-statement-of-support-for-app-service-on-windows"></a>Java runtime-instruktionen för stöd för App Service på Windows

## <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azures stöds Java Development Kit (JDK) är [Zulu](https://www.azul.com/downloads/azure-only/zulu/) via [Azul Systems](https://www.azul.com/).

Stora versionsuppdateringar tillhandahålls via nya runtime alternativ i Azure App Service för Windows. Kunder uppdateras till dessa senare versioner av Java genom att konfigurera deras App Service-distribution och ansvarar för att testa och att se till att viktig uppdatering uppfyller deras behov.

Stöds JDKs korrigerade automatiskt kvartalsvis i januari, April, juli och oktober varje år.

## <a name="security-updates"></a>Säkerhetsuppdateringar

Uppdateringar och korrigeringar för större säkerhetsproblem släpps när de blir tillgängliga från Azul Systems. En sårbarhet i ett ”större” har definierats som en grundläggande poäng med 9.0 eller senare på den [NIST vanliga säkerhetsproblem bedömning System, version 2](https://nvd.nist.gov/cvss.cfm).

## <a name="deprecation-and-retirement"></a>Utfasningen och tillbakadragande

Om en stöds Java runtime kommer att dras tillbaka, får Azure-utvecklare som använder den berörda runtime ett utfasningsmeddelande om minst sex månader innan körningen har dragits tillbaka.

## <a name="local-development"></a>Lokal utveckling

Utvecklare kan ladda ned produktion Edition av Azul Zulu Enterprise JDK-Paketet för lokal utveckling från [Azuls hämtningsplats](https://www.azul.com/downloads/azure-only/zulu/).

## <a name="development-support"></a>Stöd för programutveckling

Produktsupport för den [Azure stöder Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) är tillgänglig via Microsoft när du utvecklar för Azure eller [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) med en [kvalificerade Azure-supportplan](https://azure.microsoft.com/support/plans/).

## <a name="runtime-support"></a>Runtime-stöd

Utvecklare kan [öppna ett ärende](/azure/azure-supportability/how-to-create-azure-support-request) med Azul Zulu JDKs via supporten för Azure om de har en [kvalificerade supportavtal](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Nästa steg

Det här avsnittet innehåller Java Runtime-instruktionen support för Azure App Service på Windows.
- Mer information om som är värd för webbprogram med Azure App Service i avsnittet [översikt över App Service](overview.md).
- Information om Java på Azure-utveckling finns [Azure för Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
