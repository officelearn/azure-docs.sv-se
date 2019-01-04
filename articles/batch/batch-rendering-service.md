---
title: Rendering översikt – Azure Batch
description: Med hjälp av Azure för rendering och en översikt över Azure Batch renderingsfunktioner
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: bc8c96345aeb1886696326edd230666ac8b6c41d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542221"
---
# <a name="rendering-using-azure"></a>Rendering med hjälp av Azure

Rendering är processen för att utföra 3D-modeller och konvertera dem till 2D-bilder. 3D-scenfilerna är skrivna i program, till exempel Autodesk 3ds Max, Autodesk Maya och Blender.  Renderingsprogram som Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray och Blender cykler producerar 2D-bilder.  Ibland enda avbildningar skapas från scenfilerna. Dock är det vanligt att modellera och återge flera bilder och sedan kombinera dem i en animering.

Arbetsbelastningen rendering används kraftigt för effekter (VFX) i branschen Media och underhållning. Rendering används också i många andra industires, till exempel reklam, detaljhandel, olja och gas och tillverkning.

Processen för rendering är beräkningsmässigt intensivt; Det kan finnas många bildrutor/bilder för att producera och varje avbildning kan ta flera timmar att återge.  Rendering används därför en perfekt batch-bearbetning som kan använda Azure och Azure Batch för att köra många renderingar parallellt.

## <a name="why-use-azure-for-rendering"></a>Varför använda Azure för rendering?

Av flera orsaker är återgivningen en arbetsbelastning som passar perfekt för Azure och Azure Batch:

* Rendering jobb kan delas upp i många delar som kan köras parallellt med flera virtuella datorer:
  * Animeringar består av många bildrutor och varje ram kan återges parallellt.  Flera virtuella datorer tillgängliga för processen för varje bildruta, desto snabbare alla ramar och animeringen kan framställas.
  * Vissa rendering program kan enda bildrutor delas i flera delar, till exempel paneler eller segment.  Varje del kan återges separat och sedan kombinera till den slutliga avbildningen när alla delar är klar.  Fler virtuella datorer som är tillgängliga, desto snabbare en ram kan återges.
* Rendering projekt kan kräva enorm skala:
  * Enskilda ramar kan vara komplexa och ta flera timmar att rendera, även på avancerad maskinvara; animeringar kan bestå av bildrutor hundratusentals.  En enorm mängd beräkning krävs för att rendera högkvalitativa animeringar inom en rimlig tid.  I vissa fall använts över 100 000 kärnor för att rendera tusentals bildrutor parallellt.
* Rendering projekt är projektbaserade och kräver olika mängder beräkning:
  * Allokera kapacitet för beräkning och lagring vid behov, skala upp eller ned enligt belastning under ett projekt och ta bort den när ett projekt är klar.
  * Betala för kapacitet vid, men inte betala för det när det finns ingen belastning, till exempel mellan projekt.
  * Serva både för ökningar på grund av oväntade ändringar; skala senare om det finns oväntade ändringar sent i ett projekt och dessa ändringar behöver bearbetas på ett strikt schema.
* Välj bland ett brett urval av maskinvaran i enlighet med program, arbetsbelastning och tidsintervall:
  * Det finns ett brett urval av maskinvara som är tillgänglig i Azure som kan allokeras och hanteras med Batch.
  * Beroende på projektet vara kravet för bästa pris/prestanda eller bästa prestanda.  Olika scener och/eller renderingsprogram har olika minneskrav.  Vissa rendering program kan använda GPU: er för bästa prestanda och vissa funktioner. 
* Lågprioriterade virtuella datorer minskade kostnader:
  * Lågprioriterade virtuella datorer är tillgängliga för stora rabatter jämfört med vanliga virtuella datorer på begäran och lämpar sig för vissa typer av jobb.
  * Lågprioriterade virtuella datorer kan allokeras av Azure Batch med Batch ger stor flexibilitet i hur de används för att serva för en rad olika krav.  Batch-pooler kan bestå av både dedikerade och lågprioriterade virtuella datorer, med det är möjligt att ändra olika typer av virtuella datorer när som helst.

## <a name="options-for-rendering-on-azure"></a>Alternativ för återgivning på Azure

Det finns en rad Azure-funktioner som kan användas för arbetsbelastningar.  Vilka funktioner du använder beror på alla befintliga miljö och krav.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Befintliga lokala återgivning-miljö med ett hanteringsprogram för rendering

I de flesta fall är för det för att återge en befintlig lokal grupp som hanteras av en rendering hanteringsprogram som PipelineFX Qube, Royal återge eller Thinkbox tidsgräns.  Kravet är att utöka lokalt rendering servergruppen kapacitet med hjälp av virtuella Azure-datorer.

Hanteringsprogramvara för rendering är inbyggda i Azure-support och vi göra tillgängliga plugin-program som lägger till stöd för Azure. För mer information om den stöds rendera chefer och funktioner som aktiverats ska du läsa artikeln om [med rendera chefer](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Anpassade rendering arbetsflöde

Det krävs för virtuella datorer att utöka ett befintligt renderingsgrupp.  Azure Batch-pooler kan allokera stort antal virtuella datorer, kan lågprioriterade virtuella datorer som ska användas och dynamiskt automatiskt skalade med virtuella datorer med fullständig pris och tillhandahåller betala för det du använder licensiering för populära renderingsprogram.

### <a name="no-existing-render-farm"></a>Inga befintliga renderingsservergrupp

Klientdatorer som kan utföra rendering, men rendering arbetsbelastningen ökar och det tar för lång tid att endast använda arbetsstation kapacitet.  Azure Batch kan användas på både allokera rendering servergruppen databearbetning på begäran, samt schemalägga Renderingsjobb till Azure renderingsgrupp.

## <a name="azure-batch-rendering-capabilities"></a>Azure Batch rendering-funktioner

Azure Batch kan parallella arbetsbelastningar som ska köras i Azure.  Det möjliggör skapandet och hanteringen av stora mängder virtuella datorer där program installeras och körs.  Den innehåller också omfattande funktioner för att köra instanser av dessa program och ger tilldelningen av uppgifter till virtuella datorer, köer, program, och så vidare för jobbschemaläggning.

Azure Batch används för många arbetsbelastningar, men följande funktioner är tillgängliga för väljer att göra det enklare och snabbare att köra Renderingsjobb.

* Avbildningar av Virtuella datorer med förinstallerade grafik- och renderingsprogram:
  * Azure Marketplace-VM-avbildningar är tillgängliga som innehåller populära grafik- och renderingsprogram, inte behöver installera program som dig själv eller skapa dina egna anpassade avbildningar med program som har installerats. 
* Betala per användning licensiering för renderingsprogram:
  * Du kan välja att betala för program per minut, förutom att betala för databearbetning virtuella datorer, vilket innebär att du behöver köpa licenser och potentiellt konfigurera en server för programmen.  Betala för användning innebär också att det är möjligt att serva för olika och oväntat eftersom det är inte ett fast antal licenser.
  * Det är också möjligt att använda de förinstallerade programmen med dina egna licenser och inte använda licensieringen där du betalar per användning. Detta gör normalt du installerar en lokal eller Azure-baserade licensiera server och ett Azure-nätverk för att ansluta renderingspool till licensservern.
* Plugin-program för klienten utformning och modellapplikationer:
  * Plugin-program att slutanvändarna ska kunna använda Azure Batch direkt från klientprogrammet, som Autodesk Maya, så att de kan skapa pooler, skicka in jobb och göra användning på mer beräkningskapacitet för att utföra snabbare renderingar.
* Rendera manager-integrering:
  * Azure Batch är integrerad i rendering hanteringsprogram eller plugin-program är tillgängliga för att tillhandahålla Azure Batch-integrering.

Det finns flera sätt att använda Azure Batch, som gäller även för Azure Batch rendering.

* API: er:
  * Skriva kod med hjälp av den [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch), eller andra API: er som stöds.  Utvecklare kan integrera Azure Batch-funktioner i deras befintliga program eller ett arbetsflöde, om molnet eller baserat på plats.  Till exempel den [Autodesk Maya-plugin-programmet](https://github.com/Azure/azure-batch-maya) använder Batch Python-API för att anropa Batch, skapa och hantera pooler, skickar in jobb och aktiviteter och övervaka status.
* Verktyg för kommandoraden:
  * Den [Azure-kommandorad](https://docs.microsoft.com/cli/azure/) eller [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) kan användas för att skriva Batch användning.
  * I synnerhet gör CLI för Batch-mallstöd det mycket enklare att skapa pooler och skicka jobb.
* Användargränssnitt:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) är ett plattformsoberoende klientverktyg som också kan Batch-konton kan hanteras och övervakas, men innehåller vissa förbättrade funktioner jämfört med Azure-portalens användargränssnitt.  En uppsättning mallar för poolen och jobbet är förutsatt att är skräddarsydda för varje program som stöds och kan användas för att enkelt skapa pooler och skicka jobb.
  * Azure-portalen kan användas för att hantera och övervaka Azure Batch.
* Klienten programmet plugin-modulens:
  * Plugin-program är tillgängliga som gör det Batch rendering att användas direkt i klient-design och modellapplikationer. Plugin-programmen anropa främst Batch Explorer-program med detaljerad information om den aktuella 3D-modellen.
  * Följande plugin-programmen är tillgängliga:
    * [Azure Batch för Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Komma igång med Azure Batch rendering

Se följande grundläggande självstudierna för att prova Azure Batch rendering:

* [Använda Batch Explorer för att rendera en scen Blender](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Använda Batch-CLI för att återge en Autodesk 3ds Max-scen](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Nästa steg

Fastställa listan med renderingsprogram och versioner på Azure Marketplace-VM-avbildningarna i [i den här artikeln](https://docs.microsoft.com/azure/batch/batch-rendering-applications).