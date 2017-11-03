---
title: "Azure SDK för .NET 2.5.1 viktig information"
description: "Azure SDK för .NET 2.5.1 viktig information"
services: app-service
documentationcenter: .net,nodejs,java
author: Juliako
manager: erikre
editor: 
ms.assetid: 8d3d815f-bb58-447e-8ff0-f9b9603c7b00
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/10/2016
ms.author: juliako
ms.openlocfilehash: 357e58665f5cdf6ea9a3fcaee4a390f2b0d2045f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-251-release-notes"></a>Azure SDK för .NET 2.5.1 viktig information
Det här dokumentet innehåller viktig information för Azure SDK för .NET 2.5.1 versionen. 

## <a name="azure-sdk-for-net-251-release-notes"></a>Azure SDK för .NET 2.5.1 viktig information
Följande är nya funktioner och uppdateringar i Azure SDK för .NET 2.5.1.

* Nya features\scenarios rör **Web verktyg Extensions**. 
  
  * Azure Websites har bytt namn till Azure App Service. 
  * Azure API Apps (förhandsgranskning) stöd har lagts till så att kunder kan publicera ASP.NET-projekt som API Apps och sedan använda guiden Lägg till > Azure API-App klient gest i C#-projekt för att generera kod baserat på strukturen för distribuerade API-appen. 
  * Noden webbplatser i Server Explorer är föråldrad i stället för Azure App Service-noden, som innehåller stöd för resursen gruppbaserade gruppering av Azure API Apps och Mobilappar Web Apps.
  * Stöd för Azure Mobile Apps (förhandsversion) har lagts till så att kunder kan skapa nya Mobile Apps-projekt, lägga till Mobile Apps domänkontrollanter, publicera projekt och felsöka program.
  * Lägg till > Azure API-App klient gest stöder nu lokala Swagger JSON-filer, så Web API-utvecklare kan använda NuGets från tredje part som Swashbuckle generera Swagger eller redigera den manuellt. På så sätt kan klienten utvecklare kan använda funktionerna för kodgenerering med för att använda valfri Swagger-slutpunkt i C#-projekt. 
  * Web App och API-App publishing dialogrutor har förbättrats för att stödja Azure Portal begreppet resurs gruppering och val/skapa Azure-resursgrupper och Apptjänstplaner representeras i nya Webbappen och API-App etablering dialogrutan. 
  * Azure API App Server Explorer noder innehåller länkar till API Apps djuplänk i Azure Portal samt andra funktioner som loggen strömning och fjärrfelsökning.
    
    Kända problem och aktuella begränsningar i Azure SDK .NET 2.5.1 [detta](app-service-release-notes.md#known_issues_2_5_1) nedan.
* Nya features\scenarios rör **HDInsight Tools** i Visual Studio har aktiverats i den här versionen. 
  
  * Lokala validering av hive-skript. Klicka på verifiera skriptet i verktygsfältet för att se om det finns några fel i skriptet. 
  * Bättre felsökning av Hive-jobb. Nu kan du felsöka Hive-jobb genom att öppna Yarn-loggar i Visual Studio. Om programmet har prestandaproblem, ger undersöka YARN-loggar användbar information...
  * (Förhandsversion) Nyckelordet automatisk komplettering och IntelliSense stöd för Hive. För att du skriva Hive-skript, HDInsight Tools för Visual Studio för att lägga till nyckelordet automatisk komplettering och IntelliSense-stöd för Hive.
  * Storm-stöd. Du kan nu använda HDInsight Tools för Visual Studio för att utveckla Storm-topologier/kanaler/bultar i C#. Du kan skicka utvecklade topologin till ett Storm-kluster och se status för kanal-topologi/bulten. Du kan använda systemloggar och loggar som kunden för att felsöka din Storm-topologier/bultar/kanaler. Du kan också använda befintliga JAVA tillgångar i Storm på HDInsight.
    
    Mer information finns i [komma igång med HDInsight Hadoop-verktyg för Visual Studio](../hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

## <a id="known_issues_2_5_1"></a>Azure SDK för .NET 2.5.1 kända problem och begränsningar
* Azure API Apps är synlig som mål för Mobile Apps distribution. Web Apps ska endast mål för Mobile Apps förrän en senare version. 
* Azure API-App-etablering kan resultera i lyckades men ibland kan inte uppdatera förlopp i fönstret aktivitet för Azure App Service. Lösningen är att kontrollera statusen för den nya Azure API Apps i Azure Portal. 
* Arkiv > Nytt projekt > API-App > F5 upplevelse resulterar i ett HTTP-fel eftersom det inte finns några default/index.html. Lösningen är att manuellt Bläddra till /api/values-URL. 
* Server Explorer ikoner visas jämna mellanrum, sammanslagna. Starta om VS löser problemet. 
* Om ett undantag vid Web App eller API-App-etablering (till exempel överskred kvoten fel eller Azure API Apps gateway dubblettnamn), visa felen vissa rådata JSON-texten. 
* Skapa återkommande projekt problem när Application Insights kontrolleras vid tidpunkten för skapandet av projektet.
* Ibland kan den genererade koden Azure API-App klient saknar namnområden måste de manuellt (eller importeras automatiskt via Visual Studio ledtrådar) för kod ska kompileras. 
* Mobilapp projekt ska publiceras till webbprogram, men du måste välja en plats som du har skapat som en Mobilapp i Azure-portalen eftersom Mobilapp projekt kräver en databas. 
* Startsidan för Mobile Apps använder termen ”Mobiltjänst” i stället för ”mobila appar” 
* Mobilapp för att skapa projekt kan ta upp till en minut att skapa. 
* Under API-App kommer etablering (i vissa fall) ett fel tillbaka från Azure API reflektion att behörigheterna inte gick att ange korrekt, även om API-appen har etablerats korrekt och är redo för användning. Du kan manuellt ange behörigheter i Azure Portal.
* Application Insights stöds inte på API-App mallar och mallar för Mobilapp.
* API-App-projekt kan inte användas tillsammans med Cloud Service-projekt.
* API-App projektmallar är bara tillgängliga i C#.
* API-App förbrukning via snabbmenyn ”Lägg till Azure API App klient” stöds endast i C#.

