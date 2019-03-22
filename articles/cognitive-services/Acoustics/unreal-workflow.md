---
title: Projektet Akustik Unreal Design självstudien
titlesuffix: Azure Cognitive Services
description: Den här självstudien beskrivs arbetsflödet för design för projektet Akustik i Unreal och Wwise.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 57bde67ac2259b3847f59f95eaefba9c6fddf13e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316210"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Projektet Akustik Unreal/Wwise Design självstudien
Den här självstudien beskriver design, installation och arbetsflöde för projektet Akustik i Unreal och Wwise.

Nödvändiga program:
* Ett Unreal projekt med det projekt Akustik Wwise och Unreal plugin-program

För att få ett Unreal projekt med projekt Akustik, kan du:
* Följ den [projekt Akustik Unreal integrering](unreal-integration.md) anvisningar för att lägga till projektet Akustik i projektet Unreal
* Du kan också använda den [projekt Akustik exempelprojektet](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Konfigurera projektet hela Wwise egenskaper
Wwise har globala hinder och ocklusion kurvor som påverkar hur projekt Akustik plugin-programmet styr Wwise ljud DSP.

### <a name="design-wwise-occlusion-curves"></a>Utforma Wwise ocklusion kurvor
När projektet Akustik är aktiv den svarar på volymen är spärrat, låg pass filter (LPF) och hög pass filter (HPF) kurvor du anger i Wwise. Rekommenderar vi att ställa in din kurvan volymtyp linjär med värdet-100 dB för en ocklusion värdet 100.

Med den här inställningen om projektet Akustik simuleringen beräknar ett ocklusion av-18 dB det kommer är indata för den nedan kurvan vid X = 18 och motsvarande Y-värdet är dämpning tillämpas. Om du vill göra en halv ocklusion in-50 dB i stället för-100 dB och-200 dB till exaggerate ocklusion. Du kan anpassa och finjustera en kurva som fungerar bäst för ditt spel.
 
![Skärmbild av Wwise är spärrat kurvan redigeraren](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Inaktivera Wwise hinder kurvor
Wwise hinder kurvor påverkar torr avskilt men projekt Akustik använder kontroller för design och simulering framtvinga igång/torr förhållanden. Vi rekommenderar att du inaktiverar hinder volym kurvan. Om du vill hantera wetness, använder du kontrollen Wetness justera beskrivs senare.
 
Om du använder hinder LPF/HPF kurvor för andra ändamål, kontrollerar du att du har angett dem till Y = 0 X = 0 (dvs, det finns inga LPF eller HPF när det finns inga hinder).

![Skärmbild av Wwise hinder kurvan redigeraren](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Utforma projekt Akustik mixer-parametrar
Du kan styra globala eko egenskaper genom att gå till fliken mixer-plugin-programmet i projektet Akustik Bus. Dubbelklicka på ”projekt Akustik Mixer (anpassat)” om du vill öppna mixer plugin-programmets inställningspanelen.

Du kan också se att mixer-plugin-programmet har ett ”utföra Spatialization”-alternativ. Om du föredrar att använda projekt akustiska inbyggda spatialization, markera kryssrutan ”utföra Spatialization” och välja bland HRTF eller panorering. Se till att inaktivera alla torr Aux bussar som du har konfigurerat, annars du höra direkta sättet två gånger. Använd ”Wetness justera” och ”skalfaktor eko tid” till global kontroll på blandningen av eko. Observera att du måste starta om Unreal och sedan återskapa ljudbank innan du stöter på play för att hämta mixer-plugin-programmet config ändringar, till exempel utföra Spatialization kryssrutan.

![Skärmbild av projektet Akustik Wwise mixer-plugin-programmet alternativ](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Ange projektet Akustik design kontroller i hierarkin Wwise aktören mixer
Dubbelklicka på aktören-Mixer till kontrollparametrar för en enskild aktör mixer och sedan klickar du på fliken dess Mixer-plugin-programmet. Här kommer du att kunna ändra eventuella parametrar på nivån per ljud. Dessa värden kombinera med de som angetts på Unreal sida (beskrivs nedan). Till exempel om projektet Akustik Unreal plugin-programmet anger Outdoorness justering på ett objekt till 0,5 och Wwise uppsättningar tillämpas den till-0.25 resulterande Outdoorness justering för som ljud är 0,25.

![Skärmbild av per ljud mixer-inställningar i Wwise aktören mixer hierarki](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Kontrollera att aux bus har torr skicka och utdata bus har provmaterialet skicka
Kom ihåg att nödvändiga aktör-mixer-installationsprogrammet utbyten vanligt torr- och routning i Wwise. Den genererar eko signal på aktören-mixers utdata-buss (inställd på projekt Akustik Bus) och torr signalen längs en användardefinierad aux bus. Den här routning krävs på grund av funktioner för plugin-programmet API för Wwise mixer som projektet Akustik Wwise plugin-programmet använder.

![Skärmbild av Wwise redigerare som visar röst designriktlinjer för projektet Akustik](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Konfigurera avståndet dämpning kurvor
Se till att alla dämpning kurva som används av aktör blandare, med hjälp av projektet Akustik har egna aux skicka inställd på ”utdata bus volym”. Wwise sker som standard för nya dämpning kurvor. Om du migrerar ett befintligt projekt, kontrollera inställningarna kurvan. 

Projektet Akustik simulering har en radie av 45 taxor runt player-plats som standard. I allmänhet rekommenderar vi ställa in din dämpning kurvan-200 dB runt det avståndet. Den här avståndet är inte ett villkor. För vissa låter som förespråkar du en större radie. I sådana fall är villkor för att endast geometri inom 45 m för player-platsen ska delta. Om programmet är i ett rum och en bra källa är utanför utrymme och direkt 100 miljoner, ska det vara korrekt occluded. Om källan är i ett rum och spelaren är utanför och direkt 100 miljoner, vara det inte korrekt occluded.

![Skärmbild av Wwise dämpning kurvor](media/atten-curve.png)

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Konfigurera scen hela projektet Akustik egenskaper

Akustik utrymme aktören visar många av kontrollerna som ändrar funktionssättet för systemet och är användbara vid felsökning.

![Skärmbild av Unreal Akustik utrymme kontroller](media/acoustics-space-controls.png)

* **Akustik Data:** Det här fältet måste tilldelas en tillgång för bakade Akustik från katalogen innehåll/Akustik. Plugin-programmet projekt Akustik läggs automatiskt innehåll/Akustik katalogen till ditt projekt paketerade kataloger.
* **Storlek på panelen:** Omfattningen av region som helst den lyssnare som du vill att Akustik data läses in i RAM-minne. Så länge lyssnare avsökningar omedelbart runt spelaren läses in i, resultatet är samma som läser in akustiska data för alla avsökningar. Större paneler använder mer RAM-minne, men minska disk-i/o
* **Automatisk Stream:** När aktiverad, laddas automatiskt i nya paneler som lyssnaren når gränsen på en inlästa region. När inaktiverad, behöver du läsa in nya paneler manuellt via kod eller skisser
* **Cache-skala:** styr storleken på cacheminnet används för akustiska frågor. En mindre cache använder mindre minne men kan öka CPU-användning för varje fråga.
* **Akustik aktiverat:** En debug-kontroll för att aktivera snabb A / B växla för Akustik simuleringen. Den här kontrollen ignoreras i levererade konfigurationer. Kontrollen är användbart för att söka efter om en viss ljud bugg har sitt ursprung på Akustik beräkningar eller några andra problem i Wwise-projektet.
* **Uppdatera avstånd:** Använd det här alternativet om du vill använda i förväg bakade Akustik-information för avståndet frågor. De här frågorna liknar ray-sändningar, men de har förberäknade så ta mycket mindre CPU. Det är ett exempel på användning för diskreta reflektioner från närmaste ytan till lyssnaren. Om du vill utnyttja det här fullständigt måste du använder kod eller skisser för att fråga avstånd.
* **Rita statistik:** Medan UE'S `stat Acoustics` får du med CPU-information, visa den här statusen visas som lästs in kartan, RAM-användning och annan statusinformation längst upp till vänster på skärmen.
* **Rita Voxels:** Företagsdataskydd voxels Stäng om du vill som visar voxel rutnätet används under körning interpolering lyssnaren. Om underkänt finns i en runtime voxel, misslyckas den akustiska frågor.
* **Rita avsökningar:** Visa alla avsökningar för den här scen. De kommer att olika färger beroende på deras tillstånd för inläsning.
* **Rita avstånd:** Om uppdateringen avstånd är aktiverat, då visas en ruta närmaste yta till lyssnare i quantized riktningar runt lyssnaren.

## <a name="actor-specific-acoustics-design-controls"></a>Aktör-specifika Akustik design kontroller
Kontrollerna design är begränsade till en enskild ljud komponent i Unreal.

![Skärmbild av Unreal ljud Component-kontroller](media/audio-component-controls.png)

* **Multiplikatorn som är spärrat:** Styr ocklusion effekten. Värden > 1 kommer förstärker ocklusion. Värden < 1 minimerar den.
* **Wetness justering:** Ytterligare eko dB
* **Multiplikatorn som decay tid:** RT60 multiplicatively, baserat på utdata från Akustik-simulering
* **Outdoorness justering:** Styr hur utomhus genljudet är. Värden närmare 0 är mer inne, närmare 1 är mer utomhus. Den här justeringen är additiva, så ange den till -1 framtvingar inne, ange värdet till + 1 framtvingar utomhus.
* **Överföringen Db:** Rendera en ytterligare via vägg ljud med den här loudness kombineras med rad med för att se baserat avståndet dämpning.
* **Våt förhållande avståndet otroligt:** Justerar genljudet egenskaper på källan som om det vore närmare/ytterligare direkt utan att påverka den direkta sökvägen.
* **Visa akustiska parametrar:** Visa felsökningsinformation direkt ovanpå komponenten i spelet. (endast för icke-leveranser konfigurationer)

## <a name="blueprint-functionality"></a>Skissen funktioner
Akustik utrymme aktören är tillgängliga via skiss, tillhandahåller funktioner såsom läser in en karta eller ändra inställningar via skript för nivå. Vi tillhandahåller två exempel här.

### <a name="add-finer-grained-control-over-streaming-load"></a>Lägg till mer detaljerad kontroll över belastningen för direktuppspelning
Du kan använda funktionen Force belastningen panel skissen för att hantera den akustiska data som strömmas själv i stället för direktuppspelning automatiskt utifrån player position:

![Skärmbild av skiss Streaming alternativen i Unreal](media/blueprint-streaming.png)

* **Mål:** AcousticsSpace aktör
* **Center Position:** I mitten av den region som behöver data som lästs in
* **Ta bort avsökningar utanför panelen:** Om alternativet är markerat kan tas alla avsökningar inte i det nya området bort från minnet från RAM-minne. Om alternativet är avmarkerat läses det nya området in i minnet medan lämnar befintliga avsökningar också inlästa i minnet
* **Blockera vid slutförande:** Gör panelen läsa in en synkron åtgärd

Panelen måste redan anges innan du anropar Force belastningen panel. Exempelvis kan göra du något som liknar detta för att läsa in en ACE-fil, ange storleken på din panel och strömma i en region:

![Skärmbild av strömning installationsalternativ i Unreal](media/streaming-setup.png)

### <a name="optionally-query-for-surface-proximity"></a>Du kan också fråga om surface närhet
Om du vill se hur nära Surface-enheter finns i en viss riktning runt lyssnaren kan du använda funktionen fråga avstånd. Den här funktionen kan vara användbar för att driva riktad fördröjd reflektioner, eller för andra spel logik som drivs av surface närhet. Frågan är billigare än en ray-cast eftersom resultaten hämtas från Akustik uppslagstabellen.

![Skärmbild av exempelfråga skissen avstånd](media/distance-query.png)

* **Mål:** AcousticsSpace aktör
* **Se ut riktning:** Riktning att fråga, centreras lyssnaren
* **Avståndet:** Om frågan lyckas avståndet till närmaste ytan
* **Returvärdet:** Booleska – SANT om frågan lyckades, annars false

## <a name="next-steps"></a>Nästa steg
* Utforska koncepten bakom den [utforma processen](design-process.md)
* [Skapa ett Azure-konto](create-azure-account.md) att skapa din egen scen


