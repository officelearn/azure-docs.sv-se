---
title: Översikt över rendering - Azure Batch
description: Introduktion av hur du använder Azure för rendering och en översikt över Azure Batch-renderingsfunktioner
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d4423b22c4c8afea5afa9c7040e081665b17ba87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60774037"
---
# <a name="rendering-using-azure"></a>Rendering med hjälp av Azure

Rendering är processen att ta 3D-modeller och konvertera dem till 2D-bilder. 3D-scenfiler skapas i program som Autodesk 3ds Max, Autodesk Maya och Blender.  Renderingsprogram som Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray och Blender Cycles producerar 2D-bilder.  Ibland skapas enstaka bilder från scenfilerna. Det är dock vanligt att modellera och rendera flera bilder och sedan kombinera dem i en animering.

Renderingsarbetsbelastningen används i hög grad för specialeffekter (VFX) inom medie- och underhållningsindustrin. Rendering används också inom flera andra branscher som marknadsföring, detaljhandel, olja och gas samt tillverkning.

Processen för rendering är beräkningsmässigt intensiv; det kan finnas många ramar / bilder att producera och varje bild kan ta många timmar att återge.  Rendering är därför en perfekt arbetsbelastning för batchbearbetning som kan utnyttja Azure och Azure Batch för att köra många renderingar parallellt.

## <a name="why-use-azure-for-rendering"></a>Varför använda Azure för rendering?

Av många skäl är rendering en arbetsbelastning som passar perfekt för Azure och Azure Batch:

* Renderingsjobb kan delas upp i många bitar som kan köras parallellt med flera virtuella datorer:
  * Animeringar består av många bildrutor och varje bildruta kan återges parallellt.  Ju fler virtuella datorer som är tillgängliga för att bearbeta varje bildruta, desto snabbare kan alla bildrutor och animeringen produceras.
  * Vissa renderingsprogram gör att enskilda bildrutor kan delas upp i flera delar, till exempel brickor eller segment.  Varje bit kan återges separat, sedan kombineras till den slutliga bilden när alla bitar är färdiga.  Ju fler virtuella datorer som är tillgängliga, desto snabbare kan en ram återges.
* Renderingsprojekt kan kräva stor skala:
  * Enskilda ramar kan vara komplexa och kräva många timmar att rendera, även på avancerad maskinvara. animationer kan bestå av hundratusentals bildrutor.  En enorm mängd beräkning krävs för att göra högkvalitativa animeringar på en rimlig tid.  I vissa fall har över 100 000 kärnor använts för att rendera tusentals bildrutor parallellt.
* Renderingsprojekt är projektbaserade och kräver varierande beräkningsmängder:
  * Allokera beräknings- och lagringskapacitet när det behövs, skala upp eller ned den efter belastning under ett projekt och ta bort den när ett projekt är klart.
  * Betala för kapacitet när den allokeras, men betala inte för den när det inte finns någon belastning, till exempel mellan projekt.
  * Tillgodose skurar på grund av oväntade förändringar; skala högre om det finns oväntade ändringar sent i ett projekt och dessa ändringar måste bearbetas i ett snävt schema.
* Välj bland ett brett urval av maskinvara enligt program, arbetsbelastning och tidsram:
  * Det finns ett brett utbud av maskinvara som är tillgänglig i Azure som kan allokeras och hanteras med Batch.
  * Beroende på projektet kan kravet vara för bästa pris / prestanda eller bästa övergripande prestanda.  Olika scener och/eller renderingsprogram har olika minneskrav.  Vissa renderingsprogram kan utnyttja GPU:er för bästa prestanda eller vissa funktioner. 
* Virtuella datorer med låg prioritet minskar kostnaderna:
  * Virtuella datorer med låg prioritet är tillgängliga för en stor rabatt jämfört med vanliga virtuella datorer på begäran och är lämpliga för vissa jobbtyper.
  * Virtuella datorer med låg prioritet kan allokeras av Azure Batch, med Batch som ger flexibilitet i hur de används för att tillgodose en bred uppsättning krav.  Batchpooler kan bestå av både dedikerade och lågprioriterade virtuella datorer, med möjlighet att ändra mixen av vm-typer när som helst.

## <a name="options-for-rendering-on-azure"></a>Alternativ för rendering på Azure

Det finns en rad Azure-funktioner som kan användas för rendering av arbetsbelastningar.  Vilka funktioner som ska användas beror på befintliga miljöer och krav.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Befintlig lokal renderingsmiljö med hjälp av ett renderingshanteringsprogram

Det vanligaste fallet är att det finns en befintlig lokal renderingsgrupp som hanteras av ett renderingshanteringsprogram som PipelineFX Qube, Royal Render eller Thinkbox Deadline.  Kravet är att utöka den lokala renderingsbelöningen med hjälp av virtuella Azure-datorer.

Render-hanteringsprogrammet har antingen Azure-stöd inbyggt eller så gör vi plugin-program som lägger till Azure-stöd. Mer information om de renderingshanterare och funktioner som stöds är aktiverade finns i artikeln om [hur du använder renderingshanterare](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Arbetsflöde för anpassad återgivning

Kravet är att virtuella datorer ska utöka en befintlig renderingsgrupp.  Azure Batch-pooler kan allokera ett stort antal virtuella datorer, tillåta virtuella datorer med låg prioritet som ska användas och dynamiskt automatiskt skalas med virtuella datorer med full pris och tillhandahålla pay-for-use-licensiering för populära renderingsprogram.

### <a name="no-existing-render-farm"></a>Ingen befintlig rendergrupp

Klientarbetsstationer kan utföra rendering, men renderingsarbetsbelastningen ökar och det tar för lång tid att enbart använda arbetsstationskapacitet.  Azure Batch kan användas för att både allokera renderingsbeställa servergruppsberäkning på begäran samt schemalägga renderingsjobb till Azure render-servergruppen.

## <a name="azure-batch-rendering-capabilities"></a>Funktioner för Azure Batch-rendering

Azure Batch gör att parallella arbetsbelastningar kan köras i Azure.  Det gör det möjligt att skapa och hantera ett stort antal virtuella datorer där program installeras och körs.  Den innehåller också omfattande funktioner för finplanering för att köra instanser av dessa program, vilket ger tilldelning av uppgifter till virtuella datorer, kö, programövervakning och så vidare.

Azure Batch används för många arbetsbelastningar, men följande funktioner är tillgängliga för att specifikt göra det enklare och snabbare att köra renderingsarbetsbelastningar.

* VM-avbildningar med förinstallerade grafik- och renderingsprogram:
  * Azure Marketplace VM-avbildningar är tillgängliga som innehåller populära grafik- och renderingsprogram, vilket undviker behovet av att installera programmen själv eller skapar egna anpassade avbildningar med de installerade programmen. 
* Pay-per-use-licensiering för renderingsprogram:
  * Du kan välja att betala för programmen per minut, förutom att betala för virtuella beräknings-datorer, vilket undviker att behöva köpa licenser och eventuellt konfigurera en licensserver för programmen.  Att betala för användning innebär också att det är möjligt att tillgodose varierande och oväntad belastning eftersom det inte finns ett fast antal licenser.
  * Det är också möjligt att använda de förinstallerade programmen med dina egna licenser och inte använda pay-per-use-licensieringen. För att göra detta installerar du vanligtvis en lokal eller Azure-baserad licensserver och använder ett virtuellt Azure-nätverk för att ansluta renderingspoolen till licensservern.
* Plugin-program för klientdesign och modelleringsprogram:
  * Plugin-program gör det möjligt för slutanvändare att använda Azure Batch direkt från klientprogram, till exempel Autodesk Maya, vilket gör det möjligt för dem att skapa pooler, skicka jobb och använda mer beräkningskapacitet för att utföra snabbare renderingar.
* Integrering av renderingshanterare:
  * Azure Batch är integrerat i renderingshanteringsprogram eller plugin-program är tillgängliga för att tillhandahålla Azure Batch-integrering.

Det finns flera sätt att använda Azure Batch, som alla även gäller för Azure Batch-rendering.

* API:er:
  * Skriv kod med [REST,](https://docs.microsoft.com/rest/api/batchservice) [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch)eller andra API:er som stöds.  Utvecklare kan integrera Azure Batch-funktioner i sina befintliga program eller arbetsflöden, oavsett om de är i molnet eller lokalt.  Plugin-programmet [Autodesk Maya](https://github.com/Azure/azure-batch-maya) använder till exempel Batch Python-API:et för att anropa Batch, skapa och hantera pooler, skicka jobb och uppgifter och övervaka status.
* Kommandoradsverktyg:
  * [Azure-kommandoraden](https://docs.microsoft.com/cli/azure/) eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) kan användas för att skriptbatch använda batch.
  * I synnerhet batch CLI-mallens stöd gör det mycket enklare att skapa pooler och skicka jobb.
* Uis:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) är ett plattformsoberoende klientverktyg som också gör att Batch-konton kan hanteras och övervakas, men som ger några rikare funktioner jämfört med Azure-portalgränssnittet.  En uppsättning pool- och jobbmallar tillhandahålls som är skräddarsydda för varje program som stöds och som kan användas för att enkelt skapa pooler och skicka jobb.
  * Azure-portalen kan användas för att hantera och övervaka Azure Batch.
* Plugin-programmet för klientprogram:
  * Plugin-program finns tillgängliga som gör att Batch-rendering kan användas direkt från klientdesign- och modelleringsprogram. Plugin-programmen anropar huvudsakligen batchutforskaren med kontextuell information om den aktuella 3D-modellen.
  * Följande plugin-program finns tillgängliga:
    * [Azure Batch för Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Mixer](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Komma igång med Azure Batch-rendering

Se följande inledande självstudier för att prova Azure Batch-rendering:

* [Använda Batch Explorer för att rendera en Blender-scen](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Använd Batch CLI för att återge en Autodesk 3ds Max-scen](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Nästa steg

Fastställ listan över renderingsprogram och versioner som ingår i Azure Marketplace VM-avbildningar i [den här artikeln](https://docs.microsoft.com/azure/batch/batch-rendering-applications).