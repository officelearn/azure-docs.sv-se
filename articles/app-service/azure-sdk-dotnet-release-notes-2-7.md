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
ms.openlocfilehash: 9a69253129cdedc4f5d7e736d5bd8d6a68f95a1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836723"
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Azure SDK för .NET 2.7 och .NET 2.7.1 viktig information
## <a name="overview"></a>Översikt
Det här dokumentet innehåller viktig information för Azure SDK för .NET 2.7 versionen. 

Dokumentet innehåller också viktig information för Azure SDK för .NET 2.7.1 versionen.

Azure SDK 2.7 stöds bara i Visual Studio 2015 och Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) är sist stöds SDK för Visual Studio 2012.

Detaljerad information om den här versionen finns [Azure SDK 2.7 meddelande post](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) och [Azure SDK 2.7.1 meddelande efter](http://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>Azure SDK för .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Logga in förbättringar för Visual Studio 2015
Azure SDK 2.7 för Visual Studio 2015 stöder de nya funktionerna i identity management i Visual Studio 2015.  Detta inkluderar stöd för konton som har åtkomst till Azure via rollbaserad åtkomstkontroll, Cloud Solution Providers, DreamSpark och andra typer av konto och prenumeration.

Logga in förbättringar som ingår i Azure SDK 2.7 är bara tillgängliga i Visual Studio 2015. Stöd för Visual Studio 2013 ingår i Azure SDK 2.7.1.

### <a name="mobile-sdk"></a>Mobila SDK
Uppdatera **Mobilappar** mallar så att den återger senaste [NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) och installationsprocessen.

### <a name="service-bus"></a>Service Bus
Allmän felkorrigeringar och förbättringar. Mer information om uppdateringarna och funktionerna finns i viktig information för senast [Service Bus-NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

### <a name="hdinsight-tools"></a>HDInsight-verktyg
Följande uppdateringar har gjorts i den här versionen. De här uppdateringarna finns i förhandsgranskningen. Mer information finns i [bloggen](http://go.microsoft.com/fwlink/?LinkId=619108).

* Hive diagram för Hive jobb på Tez
* Fullständig Hive DML IntelliSense-stöd
* Stöd för Pig-mall
* Storm-mallar för Azure-tjänster

#### <a name="breaking-changes"></a>Gör ändringar
* Gamla **Storm** projektet måste uppgraderas när du använder den här versionen av verktygen. Mer information finns i [bloggen](http://go.microsoft.com/fwlink/?LinkId=619108).
* Visual Studio Web Express stöds inte längre. Mer information finns i [bloggen](http://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Azure Apptjänst-verktyg
Följande uppdateringar har gjorts för Web verktyg tillägg i den här versionen. Mer information finns i [detta](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blogg. 

* Stöd för DreamSpark-konton som lagts till
* Fullständig ändringen på Azure-verktyg som stöd för den nya Azure Resource Management API: er
* Stöd för Azure App Service som lagts till i [Cloud Explorer](#cloud_explorer)

#### <a name="known-issues"></a>Kända problem
Web App distribution fack noder visas inte under noden platser i Server Explorer och Web App distribution fack underordnade noder laddas inte under Cloud Explorer. Det här problemet har lösts och förberedda för nästa SDK-version. 

### <a name="cloud_explorer"></a>Cloud Explorer för Visual Studio 2015
Azure SDK 2.7 innehåller Cloud Explorer för Visual Studio 2015 där du kan visa dina Azure-resurser, granska deras egenskaper och utföra viktiga developer åtgärder från Visual Studio. 

Cloud explorer har stöd för följande:

* Resursgruppen och resurstypen vyer för dina Azure-resurser 
* Söka efter resurser efter namn (tillgängligt i resurstyp vy)
* Stöd för prenumerationer och resurser som har rollen åtkomstkontroll (RBAC) används 
* Integrerad åtgärdspanel som visar utvecklare åtgärder som är specifika för de valda resurserna. Till exempel: Koppla remote felsökare för virtuella datorer som skapats med hjälp av Resource Manager-stacken visa diagnostikdata för virtuella datorer osv.
* Integrerad egenskapsvyn som visar utvecklare egenskaper som ofta behövs under utveckling och testning 
* Snabb växling av kontot som ska användas vid uppräkning av resurser (kommandot inställningar på verktygsfältet) 
* Filtrering av prenumerationer ska användas vid uppräkning av resurser (kommandot inställningar på verktygsfältet) 
* Djuplänkar till Azure-portalen för hantering av resurser och resursgrupper 

### <a name="azure-resource-manager-tools"></a>Azure Resource Manager-verktyg
Azure Resource Manager-verktyg har uppdaterats för att fungera med rollbaserad åtkomstkontroll (RBAC) och den nya prenumerationstyper.  Ingår i dessa ändringar är möjligheten att använda nya storage-konton, förutom klassiska lagringsutrymme för att lagra artefakter under distributionen.  

Om du använder ett Azure-resursgrupp projekt från en tidigare version av SDK med SDK-2.7, krävs en ny distribution med skriptet för att distribuera med ett nytt lagringskonto i stället för klassiska lagring.  Du uppmanas innan ändringar görs i ditt projekt för att lägga till det nya skriptet.  Kommer att byta namn på det gamla skriptet och du behöver göra några ändringar av det nya skriptet manuellt.

### <a name="storage-explorer-tools"></a>Verktyg för lagring Explorer
* Stöd för att visa Tilläggsblobbar. Mer information i [i det här blogginlägget](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Stöd för att visa Premium Storage-konton via Server Explorer. Server Explorer visas endast sidblobbar för premium storage-konton som de är den enda typ som stöds för premium-lagringskonton.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Azure Data Factory-verktyg för Visual Studio
Introduktion till **Azure Data Factory-verktyg** för Visual Studio. Nedan visas funktionerna som aktiveras. Se [bloggen](http://go.microsoft.com/fwlink/?LinkId=617530) för mer information.

* **Mallen baseras redigering**: Välj Använd alltid baserade mallar, mallar för flytt av data eller databearbetning mallar för att distribuera en lösning för slutpunkt till slutpunkt-integrering och kom igång praktiska snabbt med Data Factory. 
* **Integrering med Solution Explorer för redigering och distribuera Data Factory entiteter**: skapa och distribuera pipelines och relaterade entiteter som Visual Studio-projekt. 
* **Integrering med diagramvyn för visual interaktion när du redigerar**: redigera visuellt pipelines och datauppsättningar med stöd från diagramvyn. 
* **Integrering med Server explorer för bläddring och interaktion med redan distribuerade entiteter**: utnyttja Server Explorer Bläddra som redan har distribuerats datafabriker och motsvarande enheter. Importera en distribuerad datafabrik eller entiteter (Pipeline, länkade tjänsten datauppsättningar) i projektet. 
* **JSON redigering med schemat validering och omfattande intellisense**: konfigurera och redigera JSON-dokument av Data Factory entiteter med omfattande intellisense och schema-validering 
* **Miljöer publicering**: publicera skapats pipelines för utveckling, test eller Prod miljö genom att skapa separata konfigurationsfiler för varje miljö.
* **Pig, Hive och .net-baserat stöd för databearbetning**: stöd för Pig och Hive-skript i Data Factory-projekt. Stöd för refererar till C#-projekt för att hantera .net aktivitet.

## <a name="azure-sdk-for-net-271"></a>Azure SDK för .NET 2.7.1
Följande avsnitt innehåller uppdateringar som introducerades med Azure SDK för .NET 2.7.1 versionen.

### <a name="hdinsight-tools"></a>HDInsight-verktyg
Mer detaljerad förklaring av uppdateringar för HDInsight finns [bloggen](http://go.microsoft.com/fwlink/?LinkId=623831).

* Hive-jobbet Operator-vyn (en ny funktion)
  
    För att hjälpa dig att förstå Hive-frågan bättre funktionen Hive Operator-vyn har lagts till. Om du vill se alla operatorer innanför en nod, dubbelklicka på formhörnen i diagrammet jobb. Hovra över den operatorn om du vill visa mer information om en viss operator.
* Hive fel markör (en ny funktion)
  
    Så att du kan visa grammatikfel direkt Hive fel markör-funktionen har lagts till. Dessutom felmeddelanden har förbättrats och du kan nu se detaljerad grammatikfel omedelbart (förrän den här versionen, var du tvungen att skicka Hive-skript till klustret och vänta ett tag innan du hämtar information om ditt felmeddelande).  
* Storm-topologi diagram (en ny funktion)
  
    Det är mycket viktigt att visualisera när du vill se om din topologi fungerar som förväntat. Vi lagt till visualiseringen för Storm diagram i den här versionen. Du kan visualisera viktiga mått för topologin (till exempel en färg anger väder en vissa bult är ”upptagen” eller inte). Du kan också dubbelklicka på bult/kanal för att visa mer information.
* Stöd för HDInsight-kluster som har skapats i Azure-portalen (buggfix)
  
    Du kan nu använda Visual Studio för att visa och skicka jobb till alla dina HDInsight-kluster oavsett om klustret har skapats.
* Fler IntelliSense-stöd & snabbare Hive-Metadata lästes in (en förbättring)
  
    Vi har förbättrat IntelliSense genom att lägga till mer användarvänlig förslag. Till exempel kan tabellalias nu också att föreslås i IntelliSense så att du lättare kan skriva en fråga. Vi har också förbättrats Hive-metadata läser in så tar bara några sekunder att lista alla databaser, tabeller och kolumner i dina Hive metastore.

Mer detaljerad förklaring av uppdateringar för HDInsight finns [bloggen](http://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>Förbättringar i Visual Studio 2013
* Azure SDK 2.7.1 låter Visual Studio 2013 åtkomst till Azure-konton och via rollbaserad åtkomstkontroll, Cloud Solution Providers och Dreamspark-prenumerationer.
* Med Azure SDK 2.7.1 nya Cloud Explorer verktyget fönstret är nu också tillgängliga i Visual Studio 2013.

### <a name="known-issues"></a>Kända problem
Installera Azure SDK 2.6 eller 2.7.1 för Visual Studio Community 2013 på en icke - engelska OS visas en varning att engelska och icke-engelska resurser av Visual Studio är eventuellt felaktigt matchade. Den här varningen att avvisas på ett säkert sätt. Det görs bara om datorn innehåller inte en tidigare installation av Visual Studio Community 2013 och du installerar SDK på en icke - engelska OS. Varningen visas när språkpaketet gäller RTM-resurser för Visual Studio, men innan det gäller uppdatering 4. Ignorera varningen kan språkpaket att fortsätta och slutföra tillämpa Update 4-versionen av language pack innehållet.

LightSwitch-projekt är inte kompatibelt med den här versionen. Det här problemet kommer att lösas nästa SDK-versionen.

## <a name="also-see"></a>Se även
[Azure SDK 2.7.1 meddelande post](http://go.microsoft.com/fwlink/?LinkId=623850)

[Azure SDK 2.7 meddelande post](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Support och tillbakadragning Information för Azure SDK för .NET och API: er](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

