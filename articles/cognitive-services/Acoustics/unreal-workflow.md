---
title: Project Akustik Unreal Design Tutorial
titlesuffix: Azure Cognitive Services
description: Den här självstudien beskriver designarbetsflödet för projektakustik i Unreal och Wwise.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 817a11171c5b4b4ef205e5fbb04f9b6d6d85b248
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854251"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Project Akustik Unreal / Wwise Design Handledning
Den här självstudien beskriver designinställningarna och arbetsflödet för projektakustik i Unreal och Wwise.

Programvaruförutsättning:
* Ett overkligt projekt med project acoustics wwise och overkliga plugins

Om du vill få ett unreal-projekt med Projektakustik kan du:
* Följ [project acoustics unreal integration](unreal-integration.md) instruktioner för att lägga till Project Acoustics till ditt Unreal-projekt
* Du kan också använda [exempelprojektet Projektakustik](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Konfigurera projektomfattande Wwise-egenskaper
Wwise har globala obstruktions- och ocklusionskurvor som påverkar hur Project Acoustics-insticksprogrammet driver Wwise-ljud-DSP.

### <a name="design-wwise-occlusion-curves"></a>Design Wwise ocklusion kurvor
När Project Acoustics är aktivt svarar den på ocklusionsvolymen, lågpassfilter (LPF) och HPF-kurvor (High-Pass Filter) som du ställer in i Wwise. Vi rekommenderar att du ställer in volymkurvan till linjär med värdet -100 dB för ett ocklusionsvärde på 100.

Med den här inställningen, om Project Acoustics-simuleringen beräknar en ocklusion på -18 dB, matas den in till nedanstående kurva vid X=18 och motsvarande Y-värde är den dämpning som tillämpas. Om du vill göra halv ocklusion ställer du in slutpunkten på -50 dB i stället för -100 dB, eller till -200 dB för att överdriva ocklusionen. Du kan skräddarsy och finjustera alla kurva som fungerar bäst för ditt spel.
 
![Skärmbild av Wwise ocklusionkurvaredigerare](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Inaktivera Wwise-hinderkurvor
Wwise-hinderkurvorna påverkar torrnivån isolerat men Project Acoustics använder konstruktionskontroller och simulering för att framtvinga våta/torra förhållanden. Vi rekommenderar att du inaktiverar hindervolymkurvan. Använd den våthetsjusteringsstyrning som beskrivs senare för att utforma vätsvädringskontrollen.
 
Om du använder obstruktion LPF / HPF kurvor för andra ändamål, se till att du har ställt in dem på Y = 0 vid X = 0 (det vill säga det finns ingen LPF eller HPF när det inte finns något hinder).

![Skärmbild av Wwise obstruktionskurvaredigerare](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Design Project Akustik mixer parametrar
Du kan styra globala efterklangsegenskaper genom att besöka mixern plugin fliken i Project Acoustics Bus. Dubbelklicka på "Project Acoustics Mixer (Custom)" för att öppna mixern plugin inställningar panel.

Du kan också se att mixern plugin har en "Utför spatialization" alternativ. Om du hellre vill använda Project Acoustics inbyggda spatialisering markerar du kryssrutan "Utför spatialisering" och väljer bland hrtf- eller panorering. Se till att inaktivera alla Dry Aux-bussar som du har ställt in, annars hör du direktvägen två gånger. Använd "Wetness Adjust" och "Reverb Time Scale Factor" för att utöva global kontroll över reverbblandningen. Observera att du måste starta om Unreal och sedan återskapa soundbanks innan du trycker på play för att plocka upp mixer plugin config ändringar såsom "Perform Spatialization" kryssrutan.

![Skärmbild av Project Acoustics Wwise mixer plugin alternativ](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Ange designkontroller för Projektakustik i Wwise-aktörsmixerhierarkin
Om du vill styra parametrarna för en enskild aktörsmixer dubbelklickar du på Actor-Mixer och klickar sedan på mixerns plugin-program. Här kan du ändra eventuella parametrar på nivån per ljud. Dessa värden kombineras med de som ställs in från Unreal-sidan (beskrivs nedan). Om till exempel project acoustics unreal plugin ställer utomhusjustering på ett objekt till 0,5 och Wwise ställer in den på -0,25, är den resulterande Outdoorness Justering som tillämpas på det ljudet 0,25.

![Skärmbild av inställningar för ljudmixer i Wwise-aktörsmixerhierarkin](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Se till att aux-bussen har torrsänd och utgångsbussen har våtsänd
Kom ihåg att den nödvändiga aktörsblandaren-inställningen utbyter den vanliga torr- och våtroutningen i Wwise. Den ger reverb signal på skådespelaren-mixer utgångsbuss (inställd på Project Acoustics Bus) och torrsignal längs den användardefinierade aux bussen. Denna routing krävs på grund av funktionerna i Wwise mixer plugin API som Project Acoustics Wwise plugin använder.

![Skärmbild av Wwise-redigeraren som visar riktlinjer för röstdesign för Projektakustik](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Ställ in avståndsdämpningskurvor
Kontrollera att alla dämpningskurva som används av aktörsblandare med Project Acoustics har användardefinierad aux-sändning inställd på "utgående bussvolym". Wwise gör detta som standard för nyskapade dämpningskurvor. Om du migrerar ett befintligt projekt kontrollerar du kurvinställningarna.

Som standard har Project Acoustics-simuleringen en radie på 45 meter runt spelarens plats. Vi rekommenderar i allmänhet att ställa in dämpningskurvan till -200 dB runt det avståndet. Det här avståndet är ingen hård begränsning. För vissa låter som vapen du kanske vill ha en större radie. I sådana fall är förbehållet att endast geometri inom 45 m från spelarens plats kommer att delta. Om spelaren är i ett rum och en ljudkälla är utanför rummet och 100m bort, kommer det att vara ordentligt ockluderad. Om källan är i ett rum och spelaren är utanför och 100 m bort, kommer det inte att vara ordentligt ockluderad.

![Skärmbild av Wwise dämpningskurvor](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Utjämning efter blandare ###
 En annan sak du kanske vill göra är att lägga till en post mixer equalizer. Du kan behandla project acoustics-bussen som en typisk reverb-buss (i standardefterknabbläge) och placera ett filter på den för att göra utjämning. Du kan se ett exempel på detta i projektet Project Acoustics Wwise Sample Project.

![Skärmbild av Wwise post-mixer EQ](media/wwise-post-mixer-eq.png)

Ett högpassfilter kan till exempel hjälpa till att hantera basen från närfältsinspelningar som ger hög, orealistisk reverb. Du kan också uppnå mer post-bake kontroll genom att justera EQ genom RTPCs, så att du kan ändra färgen på reverb vid speltid.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Ställ in scenomfattande egenskaper för projektakustik

Akustikutrymmesaktören visar många kontroller som ändrar systemets beteende och är användbara vid felsökning.

![Skärmbild av Overkliga rymdkontroller för akustik](media/acoustics-space-controls.png)

* **Akustik Data:** Det här fältet måste tilldelas en skapad akustiktillgång från katalogen Innehåll/Akustik. Project Acoustics-insticksprogrammet lägger automatiskt till katalogen Innehåll/Akustik i projektets paketerade kataloger.
* **Panelstorlek:** Omfattningen av regionen runt lyssnaren som du vill akustik data laddas i RAM. Så länge lyssnaren sonder omedelbart runt spelaren laddas i, resultaten är samma som lastning akustiska data för alla sonder. Större plattor använder mer RAM-minne, men minskar disk-I/O
* **Automatisk ström:** När den är aktiverad läses automatiskt in i nya paneler när lyssnaren når kanten av ett inläst område. När du är inaktiverad måste du läsa in nya paneler manuellt via kod eller ritningar
* **Cacheskala:** styr storleken på cacheminnet som används för akustiska frågor. En mindre cache använder mindre RAM-minne, men kan öka CPU-användningen för varje fråga.
* **Akustik aktiverad:** En felsökningskontroll för att möjliggöra snabb A/B-växlande av Akustiksimuleringen. Den här kontrollen ignoreras i leveranskonfigurationer. Kontrollen är användbar för att hitta om en viss ljudbugg har sitt ursprung i akustikberäkningarna eller något annat problem i Wwise-projektet.
* **Uppdatera avstånd:** Använd det här alternativet om du vill använda den förbakade akustikinformationen för avståndsfrågor. Dessa frågor liknar ray kastar, men de har förberäknats så ta mycket mindre CPU. En exempelanvändning är för diskreta reflektioner från den närmaste ytan till lyssnaren. Om du vill utnyttja detta fullt ut måste du använda kod eller skisser för att fråga avstånd.
* **Rita statistik:** Medan `stat Acoustics` UE: s kan förse dig med CPU-information, kommer denna statusvisning visa den för närvarande inlästa ACE-filen, RAM-användning och annan statusinformation längst upp till vänster på skärmen.
* **Rita Voxels:** Överlagra voxels nära lyssnaren som visar voxel-rutnätet som används under körning interpolation. Om en sändare finns i en runtime voxel, kommer det att misslyckas akustiska frågor.
* **Rita sonder:** Visa alla sonder för den här scenen. De kommer att vara olika färger beroende på deras belastningstillstånd.
* **Rita avstånd:** Om uppdateringsavstånd är aktiverat visas en ruta på den närmaste ytan till lyssnaren i kvantifierade riktningar runt lyssnaren.

## <a name="actor-specific-acoustics-design-controls"></a>Aktörsspecifika akustikdesignkontroller
Dessa designkontroller är begränsade till en enskild ljudkomponent i Unreal.

![Skärmbild av overkliga ljudkomponentkontroller](media/audio-component-controls.png)

* **Ocklusion multiplikator:** Styr ocklusionseffekten. Värden > 1 förstärker ocklusionen. Värden <1 minimerar det.
* **Våthet Justering:** Ytterligare reverb dB
* **Multiplicerad sönderfallstid:** Styr RT60 multiplicatively, baserat på produktionen av akustiksimuleringen
* **Justering av utomhusbruk:** Styr hur utomhus efterklang är. Värden närmare 0 är mer inomhus, närmare 1 är mer utomhus. Den här justeringen är additiv, så att ställa in den på -1 kommer att genomdriva inomhus, ställa in den på +1 kommer att genomdriva utomhus.
* **Överföring Db:** Återge ytterligare ett genom-väggen-ljud med denna ljudstyrka i kombination med siktlinjebaserad avståndsdämpning.
* **Våta förhållande avstånd warp:** Justerar efterklangsegenskaperna på källan som om den var närmare/längre bort, utan att påverka den direkta banan.
* **Spela på Start:** Växla för att ange om ljudet ska spelas upp automatiskt vid scenstart. Aktiverat som standard.
* **Visa akustiska parametrar:** Visa felsökningsinformation direkt ovanpå komponentens spel. (endast för konfigurationer som inte är avleverans)

## <a name="blueprint-functionality"></a>Skissfunktionalitet
Akustikutrymmesakten är tillgänglig via skiss, vilket ger funktioner som att läsa in en karta eller ändra inställningar via nivåskript. Vi ger två exempel här.

### <a name="add-finer-grained-control-over-streaming-load"></a>Lägg till finarekornig kontroll över direktuppspelningsbelastning
Om du vill hantera den akustiska data som streamas själv i stället för att strömma automatiskt baserat på spelarens position kan du använda funktionen Force Load Tile blueprint:

![Skärmbild av alternativ för direktuppspelning av skiss i Unreal](media/blueprint-streaming.png)

* **Mål:** Den AcousticsSpace skådespelaren
* **Mittposition:** Mitten av regionen som behöver data laddas
* **Ta bort sonder utanför panelen:** Om markerad tas alla avsökningar som inte finns i den nya regionen loss från RAM-minnet. Om det inte är markerat läses den nya regionen in i minnet samtidigt som de befintliga avsökningarna också läses in i minnet
* **Blockera vid slutförande:** Gör att panelen läser in en synkron åtgärd

Panelstorleken måste redan anges innan force load-panelen anropas. Du kan till exempel göra något liknande för att läsa in en ACE-fil, ställa in panelens storlek och strömma i en region:

![Skärmbild av alternativ för direktuppspelningsinställningar i Unreal](media/streaming-setup.png)

Funktionen Läs akustikdata som används i det här exemplet har följande parametrar:

* **Mål:** Den AcousticsSpace skådespelaren.
* **Ny Baka:** Den akustikdatatillgång som ska läsas in. Om du lämnar den här tom/ställa in den till null kommer att lossa den aktuella bakningen utan att läsa in en ny.

### <a name="optionally-query-for-surface-proximity"></a>Valfritt fråga efter yta närhet
Om du vill se hur nära ytorna är i en viss riktning runt lyssnaren kan du använda funktionen Frågeavstånd. Denna funktion kan vara användbar för att köra riktningsfördröjda reflektioner, eller för andra spellogik som drivs av ytnärhet. Frågan är billigare än en ray-cast eftersom resultaten hämtas från akustikens uppslagstabell.

![Skärmbild av exempel på planavståndsfråga](media/distance-query.png)

* **Mål:** Den AcousticsSpace skådespelaren
* **Titta riktning:** Riktningen att fråga i, centrerad på lyssnaren
* **Avstånd:** Om frågan lyckas, avståndet till närmaste yta
* **Returvärde:** Boolesk - sant om frågan lyckades, annars falskt

## <a name="next-steps"></a>Nästa steg
* Utforska begreppen bakom [designprocessen](design-process.md)
* [Skapa ett Azure-konto](create-azure-account.md) för att baka din egen scen


