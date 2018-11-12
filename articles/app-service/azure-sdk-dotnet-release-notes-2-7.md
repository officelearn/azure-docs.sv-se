---
title: Azure SDK för .NET 2.7 och .NET 2.7.1 viktig information
description: Azure SDK för .NET 2.7 och .NET 2.7.1 viktig information
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: 877d070a-9bd5-49b3-8fac-6bb5f65c3554
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 01a02296354ebe3d60f0e1fda6a6da8554a265fd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239074"
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Azure SDK för .NET 2.7 och .NET 2.7.1 viktig information
## <a name="overview"></a>Översikt
Det här dokumentet innehåller viktig information för Azure SDK för .NET 2.7 versionen. 

Dokumentet innehåller också viktig information för Azure SDK för .NET 2.7.1 versionen.

Azure SDK 2.7 stöds bara i Visual Studio 2015 och Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) är sist stöds SDK för Visual Studio 2012.

Detaljerad information om den här versionen finns i [Azure SDK 2.7 meddelande post](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) och [Azure SDK 2.7.1 meddelande efter](https://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>Azure SDK för .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Logga in förbättringar för Visual Studio 2015
Azure SDK 2.7 för Visual Studio 2015 stöder de nya funktionerna för identitet i Visual Studio 2015.  Detta inkluderar stöd för konton som har åtkomst till Azure via rollbaserad åtkomstkontroll, Cloud Solution Providers, DreamSpark och andra typer av konto och prenumeration.

Logga in förbättringar som ingår i Azure SDK 2.7 är bara tillgängliga i Visual Studio 2015. Stöd för Visual Studio 2013 ingår i Azure SDK 2.7.1.

### <a name="mobile-sdk"></a>Mobila SDK
Uppdatera **Mobile Apps** mallar för att återspegla senaste [NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) och installationsprocessen.

### <a name="service-bus"></a>Service Bus
Allmän felkorrigeringar och förbättringar. Mer information om uppdateringarna och funktionerna finns i viktig information för senast [Service Bus-NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

### <a name="hdinsight-tools"></a>HDInsight-verktyg
Följande uppdateringar har gjorts i den här versionen. De här uppdateringarna finns i förhandsversion. Mer information finns i [den här bloggen](https://go.microsoft.com/fwlink/?LinkId=619108).

* Hive diagram för Hive-jobb på Tez
* Fullständigt Hive DML-IntelliSense-stöd
* Pig mallstöd
* Storm-mallar för Azure-tjänster

#### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
* Gamla **Storm** projekt måste uppgraderas när du använder den här versionen av verktygen. Mer information finns i [den här bloggen](https://go.microsoft.com/fwlink/?LinkId=619108).
* Visual Studio Web Express stöds inte längre. Mer information finns i [den här bloggen](https://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Azure App Service-verktyg
Följande uppdateringar har gjorts till Web Tools-tillägg i den här versionen. Mer information finns i [detta](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blogg. 

* Stöd för DreamSpark-konton som har lagts till
* Fullständig ändringen till Azure Tools för den nya Azure Resource Management API: er
* Stöd för Azure App Service som lagts till [Cloud Explorer](#cloud_explorer)

#### <a name="known-issues"></a>Kända problem
Web App-distribution fack noderna visas inte under noden platser i Server Explorer och Web App-distribution fack underordnade noder läsas in inte under Cloud Explorer. Det här problemet har lösts och förberedd för nästa SDK-version. 

### <a name="cloud_explorer"></a>Cloud Explorer för Visual Studio 2015
Azure SDK 2.7 innehåller Cloud Explorer för Visual Studio 2015 där du kan visa dina Azure-resurser, granska deras egenskaper och utföra viktiga developer åtgärder från Visual Studio. 

Cloud explorer stöder följande:

* Resursgruppen och resurstypen vyer för dina Azure-resurser 
* Sök efter resurser efter namn (tillgängligt i resurstypen visa)
* Stöd för prenumerationerna och resurserna som har rollen åtkomstkontroll (RBAC) tillämpas 
* Integrerad åtgärdspanel som visar utvecklingsfokuserade åtgärder som är specifika för valda resurser. Till exempel: Koppla fjärrfelsökningsprogrammet för virtuella datorer som skapats med hjälp av Resource Manager-stacken visa diagnostikdata för virtuella datorer osv.
* Integrerad egenskapspanelen som visar utvecklingsfokuserade egenskaper som ofta krävs under utveckling och testning 
* Snabb växling av kontot som ska användas vid uppräkning av resurser (Använd kommandot Settings verktygsfältet) 
* Filtrering av prenumerationer som ska användas vid uppräkning av resurser (Använd kommandot Settings verktygsfältet) 
* Djuplänkar till Azure-portalen för hantering av resurser och resursgrupper 

### <a name="azure-resource-manager-tools"></a>Verktyget Azure Resource Manager
Azure Resource Manager Tools har uppdaterats för att fungera med rollbaserad åtkomstkontroll (RBAC) och nya typer av prenumerationer.  Ingår i de här ändringarna är möjligheten att använda nya storage-konton, förutom klassisk lagring för att lagra artefakter under distributionen.  

Om du använder ett Azure-resursgrupp-projekt från en tidigare version av SDK: N med SDK 2.7, krävs en ny distributionsskriptet för att distribuera med ett nytt lagringskonto i stället för klassisk lagring.  Du uppmanas innan ändringar i projektet att lägga till det nya skriptet.  Skriptet gamla namn och du behöver göra några ändringar till det nya skriptet manuellt.

### <a name="storage-explorer-tools"></a>Storage Explorer-verktyg
* Stöd för att visa Tilläggsblobbar. Mer information i [det här blogginlägget](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Stöd för att visa Premium Storage-konton via Server Explorer. Server Explorer visas endast sidblobar för premium storage-konton som de är den enda typ som stöds för premium storage-konton.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Azure Data Factory-verktyg för Visual Studio
Introduktion till **Azure Data Factory-verktyg** för Visual Studio. Nedan visas funktionerna som aktiveras. Se [den här bloggen](https://go.microsoft.com/fwlink/?LinkId=617530) för mer information.

* **Mallen baserad redigering**: Välj alltid i användning baserad mallar, mallar för flytt av data eller bearbetning av mallar för att distribuera en lösning för integrering från slutpunkt till slutpunkt och kom igång praktiska snabbt med Data Factory. 
* **Integrering med Solution Explorer för att skapa och distribuera Data Factory-entiteter**: skapa och distribuera pipelines och relaterade entiteter som Visual Studio-projekt. 
* **Integrering med diagramvyn för visuell interaktion när du redigerar**: visuellt skapa pipelines och datauppsättningar med stöd i diagramvyn. 
* **Integrering med Server explorer för bläddring och interaktion med redan distribuerade entiteter**: använda Server Explorer bläddrar redan distribuerat datafabriker och motsvarande entiteter. Importera en distribuerad data factory eller entiteter (datauppsättningar, Pipeline, länkad tjänst) till ditt projekt. 
* **JSON-redigering med schemat validering och omfattande intellisense**: effektivt konfigurera och redigera JSON-dokument för Data Factory-entiteter med omfattande intellisense och schema-verifiering 
* **Miljöer publicering**: publicera skapats pipelines för utveckling, testning eller Prod miljön genom att skapa separata konfigurationsfiler för varje miljö.
* **Pig, Hive och .net-baserat stöd för bearbetning av**: stöd för Pig och Hive-skript i Data Factory-projekt. Stöd för refererar till C# projekt för att hantera .net aktivitet.

## <a name="azure-sdk-for-net-271"></a>Azure SDK för .NET 2.7.1
Följande avsnitt innehåller uppdateringar som introducerades med Azure SDK för .NET 2.7.1 versionen.

### <a name="hdinsight-tools"></a>HDInsight-verktyg
Mer detaljerad förklaring om uppdateringar för HDInsight-verktyg finns i [den här bloggen](https://go.microsoft.com/fwlink/?LinkId=623831).

* Operator-vyn för hive-jobb (en ny funktion)
  
    För att hjälpa dig att förstå Hive-frågan bättre, funktionen Hive Operator-vyn har lagts till. Om du vill se alla operatorer innanför ett hörn, dubbelklicka på formhörnen i jobbdiagram. Om du vill visa mer information om en viss operator, hovrar du över operatorn.
* Hive fel markör (en ny funktion)
  
    Så att du kan visa grammatikfel direkt Hive fel markör-funktionen har lagts till. Dessutom felmeddelanden förbättrades och du kan nu se detaljerade grammatikfel direkt (fram till den här versionen var du tvungen att skicka Hive-skript i klustret och vänta en stund innan du hämtar information om felmeddelandet).  
* Storm-topologi Graph (en ny funktion)
  
    Det är mycket viktigt att visualisera när du vill se om din topologi fungerar som förväntat. Vi lagt till visualiseringen för Storm-diagram i den här versionen. Du kan visualisera viktiga mått för topologin (till exempel en färg anger väder en vissa bult är ”upptagen” eller inte). Du kan också dubbelklicka på bult/kanal för att visa mer information.
* Stöd för HDInsight-kluster som har skapats i Azure Portal (en felkorrigering)
  
    Du kan nu använda Visual Studio för att visa och skicka jobb till alla dina HDInsight-kluster oavsett där klustret har skapats.
* Mer IntelliSense-stöd och snabbare Hive-Metadata läser in (en förbättring)
  
    Vi har förbättrat IntelliSense genom att lägga till mer användarvänligt förslag. Till exempel kan tabellalias nu också föreslås i IntelliSense så att du kan enkelt skriva din fråga. Vi har även förbättrat Hive-metadata läser in så att det tar bara några sekunder att lista alla databaser, tabeller och kolumner i dina Hive-metaarkiv.

Mer detaljerad förklaring om uppdateringar för HDInsight-verktyg finns i [den här bloggen](https://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>Förbättringar i Visual Studio 2013
* Azure SDK 2.7.1 gör det möjligt för Visual Studio 2013 åtkomst till Azure-konton och prenumerationer via rollbaserad åtkomstkontroll, Cloud Solution Providers och Dreamspark.
* Med Azure SDK 2.7.1 finns det nya fönstret i Cloud Explorer-verktyget nu även i Visual Studio 2013.

### <a name="known-issues"></a>Kända problem
Installera Azure SDK 2.6 eller 2.7.1 för Visual Studio Community 2013 på en icke - engelska OS visas en varning att engelska och icke-engelska resurserna för Visual Studio kan vara fel. Den här varningen kan ignoreras på ett säkert sätt. Det bara uppstår om datorn inte innehöll en tidigare installation av Visual Studio Community 2013 och du installerar SDK på en icke - engelska OS. Varningen visas när språkpaketet gäller RTM-resurser för Visual Studio, men innan det gäller uppdatering 4. Ignorera varningen kan språkpaket att fortsätta och slutföra tillämpar uppdatering 4-versionen av language pack innehållet.

LightSwitch projekt är inte kompatibelt med den här versionen. Det här problemet löses med nästa SDK-version.

## <a name="also-see"></a>Se även
[Azure SDK 2.7.1 meddelande inlägg](https://go.microsoft.com/fwlink/?LinkId=623850)

[Azure SDK 2.7 meddelande inlägg](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Support och tillbakadragande Information om Azure SDK för .NET och API: er](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

