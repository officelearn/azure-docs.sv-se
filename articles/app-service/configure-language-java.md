---
title: Konfigurera Windows Java - appar i Azure App Service | Microsoft Docs
description: Lär dig hur du konfigurerar Java-appar ska köras på Windows standardinstanser i Azure App Service.
keywords: azure app service, web app, windows, oss, java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: efeab014c7d92a6c19d516a121fbc4436925be75
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014563"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurera en Windows Java-app för Azure App Service

Azure App Service kan Java-utvecklare för att snabbt bygga, distribuera och skala sina Tomcat eller Java Standard Edition (SE) paketeras webbprogram på en helt hanterad Windows-baserad tjänst. Distribuera program med Maven-plugin-program från kommandoraden eller i redigerare som IntelliJ, Eclipse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder i App Service. Om du aldrig har använt Azure App Service, bör du läsa den [Java-quickstart](app-service-web-get-started-java.md) första. Allmänna frågor om hur du använder App Service som inte är specifika för Java-utveckling besvaras inom den [App Service Windows vanliga frågor och svar](faq-configuration-and-management.md).

> [!NOTE]
> Hittar du inte det du letar efter? Finns det [Windows OSS vanliga frågor och svar](faq-configuration-and-management.md) eller [Java Linux konfigurationsguide](containers/app-service-linux-java.md) information om distribution och skydda din Java-app.

## <a name="configuring-tomcat"></a>Konfigurera Tomcat

Så här redigerar du Tomcat's `server.xml` eller andra konfigurationsfiler först anteckna din Tomcat huvudversion i portalen.

1. Hitta Tomcat arbetskatalogen för din version genom att köra den `env` kommando. Sök efter miljövariabeln som börjar med `AZURE_TOMCAT`och matchar din huvudversionen. Till exempel `AZURE_TOMCAT85_HOME` pekar på Tomcat-katalogen för Tomcat 8.5.
1. När du har identifierat Tomcat arbetskatalogen för din version, kopiera konfigurationskatalogen till `D:\home`. Till exempel om `AZURE_TOMCAT85_HOME` hade värdet `D:\Program Files (x86)\apache-tomcat-8.5.37`, den fullständiga sökvägen till den kopierade konfigurationskatalogen skulle vara `D:\home\tomcat\conf`.

Slutligen startar du om din App Service. Dina distributioner bör gå till `D:\home\site\wwwroot\webapps` precis som tidigare.

## <a name="java-runtime-statement-of-support"></a>Java runtime-instruktionen för support

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azures stöds Java Development Kit (JDK) är [Zulu](https://www.azul.com/downloads/azure-only/zulu/) via [Azul Systems](https://www.azul.com/).

Stora versionsuppdateringar tillhandahålls via nya runtime alternativ i Azure App Service för Windows. Kunder uppdateras till dessa senare versioner av Java genom att konfigurera deras App Service-distribution och ansvarar för att testa och att se till att viktig uppdatering uppfyller deras behov.

Stöds JDKs korrigerade automatiskt kvartalsvis i januari, April, juli och oktober varje år.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Uppdateringar och korrigeringar för större säkerhetsproblem släpps när de blir tillgängliga från Azul Systems. En sårbarhet i ett ”större” har definierats som en grundläggande poäng med 9.0 eller senare på den [NIST vanliga säkerhetsproblem bedömning System, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Utfasningen och tillbakadragande

Om en stöds Java runtime kommer att dras tillbaka, får Azure-utvecklare som använder den berörda runtime ett utfasningsmeddelande om minst sex månader innan körningen har dragits tillbaka.

### <a name="local-development"></a>Lokal utveckling

Utvecklare kan ladda ned produktion Edition av Azul Zulu Enterprise JDK-Paketet för lokal utveckling från [Azuls hämtningsplats](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Stöd för programutveckling

Produktsupport för den [Azure stöder Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) är tillgänglig via Microsoft när du utvecklar för Azure eller [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) med en [kvalificerade Azure-supportplan](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Runtime-stöd

Utvecklare kan [öppna ett ärende](/azure/azure-supportability/how-to-create-azure-support-request) med Azul Zulu JDKs via supporten för Azure om de har en [kvalificerade supportavtal](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Nästa steg

Det här avsnittet innehåller Java Runtime-instruktionen support för Azure App Service på Windows.

- Mer information om som är värd för webbprogram med Azure App Service i avsnittet [översikt över App Service](overview.md).
- Information om Java på Azure-utveckling finns [Azure för Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
