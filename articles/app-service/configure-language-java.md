---
title: Konfigurera Windows Java-appar – Azure App Service | Microsoft Docs
description: Lär dig hur du konfigurerar Java-appar så att de körs på standard instanser av Windows i Azure App Service.
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
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: c1ea306d8a6b5c1876ac6a9288820e1592dbfda6
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498520"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurera en Windows java-app för Azure App Service

Med Azure App Service kan Java-utvecklare snabbt bygga, distribuera och skala sina Tomcat-eller Java Standard Edition (SE)-paketerade webb program på en helt hanterad Windows-baserad tjänst. Distribuera program med maven-plugin-program från kommando raden eller i redigerings program som IntelliJ, Sol förmörkelse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder i App Service. Om du aldrig har använt Azure App Service bör du läsa igenom Java- [snabb](app-service-web-get-started-java.md) starten först. Allmänna frågor om att använda App Service som inte är särskilt för Java-utveckling besvaras i [vanliga frågor och svar om App Service Windows](faq-configuration-and-management.md).

> [!NOTE]
> Hittar du inte det du letar efter? Information om hur du distribuerar och skyddar Java [](containers/configure-language-java.md) -appen finns i [vanliga frågor och svar om Windows oss](faq-configuration-and-management.md) .

## <a name="configuring-tomcat"></a>Konfigurera Tomcat

Om du vill redigera `server.xml` Tomcat eller andra konfigurationsfiler bör du först anteckna din tomcat-huvud version i portalen.

1. Hitta Tomcat-hemkatalogen för din version genom att `env` köra kommandot. Sök efter den miljö variabel som börjar med `AZURE_TOMCAT`och matchar din huvud version. Till exempel `AZURE_TOMCAT85_HOME` pekar till Tomcat-katalogen för Tomcat 8,5.
1. När du har identifierat arbets katalogen Tomcat för din version, kan du kopiera konfigurations `D:\home`katalogen till. Om `AZURE_TOMCAT85_HOME` till exempel har fått `D:\Program Files (x86)\apache-tomcat-8.5.37`värdet är den nya sökvägen till `D:\home\apache-tomcat-8.5.37`den kopierade katalogen.

Slutligen startar du om din App Service. Dina distributioner bör gå till `D:\home\site\wwwroot\webapps` precis som tidigare.

## <a name="java-runtime-statement-of-support"></a>Stöd för Java Runtime-sats

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azures Java Development Kit (JDK) som stöds är [Zulu](https://www.azul.com/downloads/azure-only/zulu/) via [Azul system](https://www.azul.com/).

Huvud versions uppdateringar kommer att tillhandahållas via nya körnings alternativ i Azure App Service för Windows. Kunder uppdaterar till dessa nyare versioner av Java genom att konfigurera sina App Service-distributioner och ansvarar för att testa och se till att den viktiga uppdateringen uppfyller deras behov.

JDKs som stöds korrigeras automatiskt en gång i kvartalet i januari, april, juli och oktober varje år.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Korrigeringar och korrigeringar för viktiga säkerhets problem kommer att släppas så snart de blir tillgängliga från Azul system. En "större" sårbarhet definieras av en baspoäng på 9,0 eller högre på [NIST vanliga sårbarhets bedömnings system, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Utfasning och pensionering

Om en Java-körning som stöds dras tillbaka kommer Azure-utvecklare som använder den berörda körningen att få ett meddelande om förvarning minst sex månader innan körningen dras tillbaka.

### <a name="local-development"></a>Lokal utveckling

Utvecklare kan ladda ned Production Edition of Azul Zulu Enterprise JDK för lokal utveckling från [Azuls nedladdnings plats](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Utvecklings support

Produkt support för det [Azure-Azul Zulu-JDK som stöds](https://www.azul.com/downloads/azure-only/zulu/) är tillgängligt via Microsoft när du utvecklar för azure eller [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) med ett kvalificerat support avtal för [Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Runtime-stöd

Utvecklare kan [öppna ett problem](/azure/azure-supportability/how-to-create-azure-support-request) med Azul Zulu-JDKs via Azure-support om de har ett [kvalificerat support](https://azure.microsoft.com/support/plans/)avtal.

## <a name="next-steps"></a>Nästa steg

Det här avsnittet innehåller Java Runtime-satsen för stöd för Azure App Service i Windows.

- Om du vill veta mer om att vara värd för webb program med Azure App Service se [Översikt över App Service](overview.md).
- Information om Java på Azure-utveckling finns i [Azure för java dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
