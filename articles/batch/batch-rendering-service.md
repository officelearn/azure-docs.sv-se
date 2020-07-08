---
title: Översikt över åter givning
description: Introduktion till att använda Azure för åter givning och en översikt över Azure Batch åter givnings funktioner
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 850f4e3754af790e6b9bab5c68c9bd10849bba37
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965288"
---
# <a name="rendering-using-azure"></a>Rendering med hjälp av Azure

Rendering är processen att ta 3D-modeller och konvertera dem till 2D-bilder. 3D-scenens filer skapas i program som Autodesk 3ds Max, Autodesk Maya och Blender.  Att rendera program som Autodesk Maya, Autodesk Arnold, kaos Group V-Ray och blends ger 2D-bilder.  Ibland skapas enkla avbildningar från scen filen. Det är dock vanligt att modellera och återge flera avbildningar och kombinera dem sedan i en animering.

Arbets belastningen för rendering används kraftigt för specialeffekter (VFX) i medie-och underhållnings branschen. Rendering används också inom flera andra branscher som marknadsföring, detaljhandel, olja och gas samt tillverkning.

Processen för åter givning är i beräknings intensiv. Det kan finnas många ramar/bilder att producera och varje bild kan ta flera timmar att rendera.  Åter givning är därför en perfekt grupp bearbetnings arbets belastning som kan utnyttja Azure och Azure Batch för att köra många åter givningar parallellt.

## <a name="why-use-azure-for-rendering"></a>Varför ska jag använda Azure för rendering?

Av många skäl är åter givning en arbets belastning som passar perfekt för Azure och Azure Batch:

* Åter givnings jobb kan delas upp i många delar som kan köras parallellt med flera virtuella datorer:
  * Animeringar består av många ramar och varje ram kan återges parallellt.  Ju fler virtuella datorer som är tillgängliga för bearbetning av varje ram, desto snabbare kan alla ramar och animeringen skapas.
  * Med vissa åter givnings program kan enskilda ramar delas upp i flera delar, till exempel paneler eller segment.  Varje del kan återges separat och sedan kombineras till den sista bilden när alla delar har slutförts.  Ju fler virtuella datorer som är tillgängliga, desto snabbare kan en ram återges.
* Åter givnings projekt kan kräva enorma skalning:
  * Enskilda ramar kan vara komplexa och kräver många timmar för att kunna återges även på avancerad maskin vara. animeringar kan bestå av hundratals tusentals ramar.  En enorm mängd data krävs för att återge animeringar med hög kvalitet under en rimlig tids period.  I vissa fall har över 100 000 kärnor använts för att återge tusentals ramar parallellt.
* Åter givnings projekt är projektbaserade och kräver varierande beräknings mängder:
  * Tilldela beräknings-och lagrings kapacitet vid behov kan du skala upp eller ned enligt belastningen under ett projekt och ta bort det när projektet är klart.
  * Betala för kapacitet vid tilldelning, men betala inte för det när det inte finns någon belastning, till exempel mellan projekt.
  * Anpassa till burst-överföring på grund av oväntade ändringar. skala högre om det finns oväntade ändringar sent i ett projekt och dessa ändringar måste bearbetas enligt ett tätt schema.
* Välj bland ett brett utbud av maskin vara enligt program, arbets belastning och tidsram:
  * Det finns ett brett utbud av maskin vara som är tillgänglig i Azure och som kan allokeras och hanteras med batch.
  * Beroende på projektet kan kravet vara för bästa pris/prestanda eller bästa övergripande prestanda.  Olika scener och/eller åter givnings program har olika minnes krav.  Vissa åter givnings program kan utnyttja GPU: er för bästa prestanda eller vissa funktioner. 
* Virtuella datorer med låg prioritet minskar kostnaderna:
  * Virtuella datorer med låg prioritet är tillgängliga för en stor rabatt jämfört med vanliga virtuella datorer på begäran och är lämpliga för vissa typer av jobb.
  * Virtuella datorer med låg prioritet kan tilldelas av Azure Batch, med batch som ger flexibilitet för hur de används för att tillgodose en rad olika krav.  Batch-pooler kan bestå av både dedikerade och lågprioriterade virtuella datorer, och det är möjligt att ändra blandningen av VM-typer när som helst.

## <a name="options-for-rendering-on-azure"></a>Alternativ för åter givning av Azure

Det finns ett antal Azure-funktioner som kan användas för att återge arbets belastningar.  Vilka funktioner som ska användas beror på befintliga miljö och krav.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Befintlig lokal åter givnings miljö med hjälp av ett åter givnings hanterings program

Det vanligaste fallet är att det finns en befintlig lokal åter givning-grupp som hanteras av ett program för rendering Management, till exempel PipelineFX Qube, den senaste åter givningen eller tids gränsen.  Kravet är att utöka den lokala åter givnings Server gruppens kapacitet med hjälp av virtuella Azure-datorer.

Program varan för åter givnings hantering har stöd för inbyggd Azure-support eller så gör vi tillgängliga plugin-program som lägger till Azure-support. Mer information om de åter givnings hanterare och funktioner som stöds är aktiverade finns i artikeln om att [använda åter givnings hanterare](./batch-rendering-render-managers.md).

### <a name="custom-rendering-workflow"></a>Anpassat åter givnings arbets flöde

Kravet är för virtuella datorer att utöka en befintlig åter givnings Server grupp.  Azure Batch pooler kan allokera ett stort antal virtuella datorer, tillåta att virtuella datorer med låg prioritet används och dynamiskt skalas dynamiskt med virtuella datorer med höga priser och ger betalnings-och användnings licensiering för populära åter givnings program.

### <a name="no-existing-render-farm"></a>Ingen befintlig åter givnings grupp

Klient datorerna kan utföra åter givning, men åter givningen av arbets belastningen ökar och tar för lång tid att endast använda arbets Stations kapacitet.  Azure Batch kan användas för att både allokera data behandling på begäran och schemalägga åter givnings jobb till Azure Render-servergruppen.

## <a name="azure-batch-rendering-capabilities"></a>Azure Batch åter givnings funktioner

Azure Batch gör det möjligt att köra parallella arbets belastningar i Azure.  Det gör det möjligt att skapa och hantera ett stort antal virtuella datorer där programmen är installerade och körs.  Den innehåller också omfattande funktioner för schemaläggning av jobb för att köra instanser av dessa program, vilket ger tilldelning av uppgifter till virtuella datorer, köer, program övervakning och så vidare.

Azure Batch används för många arbets belastningar, men följande funktioner är tillgängliga för att göra det enklare och snabbare att köra åter användning av arbets belastningar.

* VM-avbildningar med förinstallerade program för grafik och åter givning:
  * VIRTUELLA Azure Marketplace-avbildningar är tillgängliga som innehåller populära grafik-och åter givnings program, vilket gör att du inte behöver installera programmen själv eller skapa egna anpassade avbildningar med de installerade programmen. 
* Betala per användning-licensiering för åter givning av program:
  * Du kan välja att betala för programmen per minut, förutom att betala för de virtuella datorerna för beräkning, vilket gör att du slipper köpa licenser och eventuellt konfigurera en licens Server för programmen.  Att betala för användning innebär också att det är möjligt att tillgodose för varierande och oväntade belastning eftersom det inte finns något fast antal licenser.
  * Du kan också använda de förinstallerade programmen med dina egna licenser och inte använda licensieringen betala per användning. För att göra detta installerar du normalt en lokal eller Azure-baserad licens Server och använder ett virtuellt Azure-nätverk för att ansluta åter givnings poolen till licens servern.
* Plugin-program för klient design och modellerings program:
  * Med plugin-program kan slutanvändare använda Azure Batch direkt från klient programmet, till exempel Autodesk Maya, så att de kan skapa pooler, skicka jobb och använda mer beräknings kapacitet för att utföra snabbare åter givning.
* Rendera chefs integrering:
  * Azure Batch är integrerat i program för rendering Management eller plugin-program finns tillgängliga för att tillhandahålla Azure Batch-integrering.

Det finns flera sätt att använda Azure Batch, som alla gäller även för Azure Batch åter givning.

* API:er:
  * Skriv kod med hjälp av [rest](/rest/api/batchservice), [.net](/dotnet/api/overview/azure/batch), [python](/python/api/overview/azure/batch), [Java](/java/api/overview/azure/batch)eller andra API: er som stöds.  Utvecklare kan integrera Azure Batch funktioner i befintliga program eller arbets flöden, oavsett om de är molnbaserade eller baserade lokalt.  [Autodesk Maya-plugin-programmet](https://github.com/Azure/azure-batch-maya) använder till exempel batch python-API: et för att anropa batch, skapa och hantera pooler, skicka jobb och uppgifter och övervaknings status.
* Kommando rads verktyg:
  * [Kommando rads](/cli/azure/) -eller [Azure PowerShell](/powershell/azure/overview) för Azure kan användas för att använda skript för batch-användning.
  * I synnerhet gör stödet för batch CLI det mycket enklare att skapa pooler och skicka jobb.
* UIs:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) är ett klient verktyg för flera plattformar som också tillåter att batch-konton hanteras och övervakas, men ger till gång till en del rikare funktioner jämfört med Azure Portal gränssnittet.  En uppsättning pool-och jobbmallar tillhandahålls som är skräddarsydda för varje program som stöds och som kan användas för att enkelt skapa pooler och skicka jobb.
  * Azure Portal kan användas för att hantera och övervaka Azure Batch.
* Klient programmets plugin-program:
  * Plugin-program är tillgängliga som tillåter att batch-rendering används direkt i klientens design-och modellerings program. Plugin-programmen anropar huvudsakligen Batch Explorer program med sammanhangsbaserad information om den aktuella 3D-modellen.
  * Följande plugin-program är tillgängliga:
    * [Azure Batch för Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Komma igång med Azure Batch åter givning

Se följande inledande självstudier för att prova Azure Batch åter givning:

* [Använda Batch Explorer för att återge en över gångs scen](./tutorial-rendering-batchexplorer-blender.md)
* [Använd batch CLI för att rendera en Autodesk 3ds Max-scen](./tutorial-rendering-cli.md)

## <a name="next-steps"></a>Nästa steg

Ta reda på listan över åter givnings program och versioner som finns på virtuella Azure Marketplace-avbildningar i [den här artikeln](./batch-rendering-applications.md).
