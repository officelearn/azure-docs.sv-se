---
title: Självstudier om projekt akustiska Unreal-design
titlesuffix: Azure Cognitive Services
description: I den här självstudien beskrivs design arbets flödet för projekt akustiska i Unreal och Wwise.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 5061370f43947341bb05bc30fa596604bc27ce74
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706577"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Själv studie kurs om projekt akustiska Unreal/Wwise-design
I den här självstudien beskrivs design konfigurationen och arbets flödet för projekt akustiska i Unreal och Wwise.

Program varu krav:
* Ett Unreal-projekt med projektet akustiska Wwise-och Unreal-plugin-program

För att få ett Unreal-projekt med projekt akustiska kan du:
* Följ anvisningarna för att lägga till projekt akustiskt i ditt Unreal-projekt genom att följa [Unreal för projekt akustiskt](unreal-integration.md)
* Eller Använd [projektet akustiska exempel projekt](unreal-quickstart.md).

## <a name="setup-project-wide-wwise-properties"></a>Konfigurera Wwise egenskaper för hela projektet
Wwise har globala hinder och ocklusiona kurvor som påverkar hur projektet akustiskt plugin-programmet driver Wwise-ljudet DSP.

### <a name="design-wwise-occlusion-curves"></a>Utforma Wwise ocklusion-kurvor
När Project-Akustisker är aktiva svarar den på ocklusion volym, LPF (Low-pass filter) och HPF-kurvor (High-pass filter) som du anger i Wwise. Vi rekommenderar att du anger typ av volym kurva till linjärt med värdet-100 dB för ett ocklusion-värde på 100.

Med den här inställningen, om projektets akustiska simulering beräknar en ocklusion på-18 dB, kommer den att indata anges till nedanstående kurva vid X = 18, och motsvarande Y-värde är den dämpade som används. Om du vill göra en halv ocklusion anger du slut punkten till-50 dB i stället för-100 dB eller till-200 dB till exaggerate ocklusion. Du kan skräddarsy och finjustera vilken kurva som passar bäst för ditt spel.
 
![Skärm bild av Wwise ocklusion Curve Editor](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Inaktivera Wwise som hindrar kurvor
Wwise-hinder påverkar den torra nivån i isoleringen men projekt akustiska använder design kontroller och simulering för att framtvinga våta/torra förhållanden. Vi rekommenderar att du inaktiverar volym kurvan för hinder. Om du vill utforma wetness använder du wetness justera kontroll som beskrivs senare.
 
Om du använder LPF/HPF-kurvor i andra syfte, se till att du har angett dem till Y = 0 vid X = 0 (det vill säga det inte finns någon LPF eller HPF när det inte finns något hinder).

![Skärm bild av Wwise-böjd kurv redigerare](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Utforma projekt akustiska mixer parametrar
Du kan styra globala reverb-egenskaper genom att gå till fliken mixer-plugin i projektets akustiska buss. Dubbelklicka på "Project Akustisker mixer (anpassad)" om du vill öppna Inställningar panelen för mixer-plugin-programmet.

Du kan också se att mixer-plugin-programmet har alternativet "utföra spatialization". Om du hellre vill använda en inbyggd spatialization i Project kan du markera kryss rutan "utföra spatialization" och välja från HRTF eller panorering. Se till att inaktivera alla torra AUX-Busses som du har skapat, annars hör du direkt sökvägen två gånger. Använd "wetness justera" och "reverb Time Scale Factor" för att träna global kontroll över reverb-mixen. Obs! Du måste starta om Unreal och sedan återskapa soundbanks innan du trycker på Play för att hämta konfigurations ändringar för mixer-plugin, till exempel kryss rutan utföra spatialization.

![Skärm bild av plugin-alternativ för Wwise-mixer i Project](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Ange projekt akustiska design kontroller i hierarkin Wwise aktör – mixer
Om du vill kontrol lera parametrar för en enskild aktörs mixer dubbelklickar du på aktör-mixer och klickar sedan på dess mixer-plugin-flik. Här kan du ändra parametrar på nivån per ljud. Dessa värden kombineras med de som anges från Unreal-sidan (beskrivs nedan). Om till exempel ett projekt akustiskt Unreal-plugin-program ställer in utöknings justering för ett objekt till 0,5 och Wwise ställer in det på-0,25, är den resulterande återställnings justeringen som tillämpas på det ljudet 0,25.

![Skärm bild av inställningarna per ljud mixer i Wwise aktör – mixer-hierarki](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Se till att AUX-bussen har torr sändning och att utmatnings bussen har våt överföring
Kom ihåg att den obligatoriska aktörs mixer-installationen förändrar den vanliga torra och våta routningen i Wwise. Den producerar reverb-signal på aktörens utmatnings buss (anges till Project Akustiske Bus) och torr signal längs den användardefinierade AUX-bussen. Den här dirigeringen krävs på grund av funktionerna i API: t Wwise mixer plugin som Project Akustiskr Wwise-pluginprogrammet använder.

![Skärm bild av Wwise-redigeraren med röst design rikt linjer för projekt akustiskt](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Konfigurera avstånds dämpade kurvor
Se till att det finns en användardefinierad AUX-överförings uppsättning för "utgående buss volym" med hjälp av en dämpad kurva som används av aktör-mixers med hjälp av projekt ljud. Wwise gör detta som standard för nyligen skapade dämpnings kurvor. Om du migrerar ett befintligt projekt kontrollerar du inställningarna för kurvan.

Som standard har projektets akustiska simulering en radie på 45 meter runt Player-platsen. Vi rekommenderar vanligt vis att ställa in en dämpnings kurva till-200 dB runt det avståndet. Det här avståndet är inte ett fast villkor. För vissa ljud som vapen kanske du vill ha en större radie. I sådana fall är det villkoret att endast geometrin inom 45 m från Player-platsen kommer att delta. Om spelaren är i ett rum och en ljud källa är utanför rummet och 100 miljoner kommer den att vara korrekt Occluded. Om källan är i ett rum och spelaren ligger utanför och 100 m, kommer den inte att vara korrekt Occluded.

![Skärm bild av Wwise-dämpade kurvor](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Post mixer-equalizer ###
 En annan sak som du kanske vill göra är att lägga till en equalizer för posts mixer. Du kan behandla projektets akustiska buss som en typisk reverb-buss (i standard läget för reverb) och ange ett filter för att utföra en utjämning. Du kan se ett exempel på detta i Project Akustisker Wwise-exempelprojektet.

![Skärm bild av Wwise post-mixer EQ](media/wwise-post-mixer-eq.png)

Ett högt pass-filter kan till exempel användas för att hantera basen från inspelade inspelningar som ger boomy, reverb. Du kan också få mer kontroll efter bakgrunds kontroll genom att justera EQ genom RTPCs, så att du kan ändra färgen på reverb vid speltidpunkt.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Konfigurera egenskaper för projekt akustiskt för hela scenen

Skådespelaren för akustiskt utrymme visar många kontroller som ändrar systemets funktion och är användbara vid fel sökning.

![Skärm bild av Unreal-akustiskt utrymmes kontroller](media/acoustics-space-controls.png)

* **Akustiska data:** Det här fältet måste tilldelas en bakade-akustisk till gång från innehålls-/akustiska katalogen. Plugin-programmet för Project-akustiskt lägger automatiskt till katalogen innehåll/akustiskt i projektets paketerade kataloger.
* **Panel storlek:** Omfattningen av regionen runt den lyssnare som du vill att akustiska data ska läsas in i RAM-minnet. Så länge som lyssnare avsökningar omedelbart runt spelaren läses in, är resultatet detsamma som att läsa in akustiska data för alla avsökningar. Större paneler använder mer RAM-minne, men minska disk-I/O
* **Automatisk ström:** När aktive rad läses automatiskt in i nya paneler när lyssnaren når kanten på en inläst region. När du har inaktiverat måste du läsa in nya paneler manuellt via kod eller ritningar
* **Skalning av cache:** styr storleken på cacheminnet som används för akustiska frågor. En mindre cache använder mindre RAM-minne, men kan öka processor användningen för varje fråga.
* **Akustiska funktioner:** En fel söknings kontroll för att aktivera snabb A/B-växling av den akustiska simuleringen. Den här kontrollen ignoreras i skeppnings konfigurationer. Kontrollen är användbar för att ta reda på om ett visst ljud fel uppstår i de akustiska beräkningarna eller något annat problem i Wwise-projektet.
* **Uppdaterings avstånd:** Använd det här alternativet om du vill använda den bakade akustiska informationen för distans frågor. Dessa frågor liknar Ray-sändningar, men de har förberäknats för att ta mycket mindre processor kraft. Ett exempel på användningen är för diskreta reflektioner från den närmaste ytan till lyssnaren. För att helt utnyttja detta måste du använda kod eller skisser för att fråga avstånd.
* **Rita statistik:** Även om UE `stat Acoustics` kan förse dig med CPU-information visar denna status visar den inlästa ACE-filen, RAM-användningen och annan statusinformation längst upp till vänster på skärmen.
* **Rita Voxels:** Överläggs voxels nära lyssnaren som visar Voxel-rutnätet som används under körnings interpolation. Om en sändare är inuti en runtime-Voxel, kommer den att Miss känna sig akustiska frågor.
* **Rita avsökningar:** Visa alla avsökningar för den här scenen. De kommer att vara olika färger beroende på inläsnings tillstånd.
* **Rita avstånd:** Om uppdaterings avstånd är aktiverat visas en ruta på den närmaste ytan för lyssnaren i quantized riktningar runt lyssnaren.

## <a name="actor-specific-acoustics-design-controls"></a>Kontroll av skådespelare-speciella akustiska symboler
Dessa design kontroller är begränsade till en enskild ljud komponent i Unreal.

![Skärm bild av kontroller för Unreal Audio-komponenter](media/audio-component-controls.png)

* **Ocklusion multiplikator:** Styr ocklusion-påverkan. Värdena > 1 kommer att utvidga ocklusion. Värdena < 1 kommer att minimeras.
* **Wetness justering:** Ytterligare reverb-databas
* **Nedbrytnings tids multiplikator:** Styr RT60-multiplicatively, baserat på utdata från den akustiska simuleringen
* **Justering av dörren:** Styr hur utomhus Reverberation är. Värden närmare 0 är fler indörrer, närmare 1 är mer utomhus. Den här justeringen är additiv, så om du anger den till-1 framtvingas inaktive ras inaktive ras.
* **Överförings databas:** Rendera ett extra "t-The-vägg"-ljud med den här loudness kombinerat med insikter som baseras på branschspecifika avstånd.
* **Avstånd för våt ratio:** Justerar Reverberation egenskaper på källan som om det var närmare/längre bort, utan att den direkta sökvägen påverkas.
* **Spela upp vid start:** Växla för att ange om ljudet ska spelas upp automatiskt när scenen startas. Aktiverat som standard.
* **Visa akustiska parametrar:** Visa felsöknings information direkt ovanpå komponenten i spelet. (endast för konfigurationer som inte är leverans)

## <a name="blueprint-functionality"></a>Skiss funktioner
Den akustiska utrymmes aktören kan nås via skissen, vilket ger funktioner som att läsa in en karta eller ändra inställningar via nivå skript. Vi tillhandahåller två exempel här.

### <a name="add-finer-grained-control-over-streaming-load"></a>Lägg till detaljerad kontroll över strömnings belastning
Om du vill hantera uppspelningen av akustiska data själv i stället för strömning automatiskt baserat på spelarens position kan du använda den tvingande inläsnings panelens skiss funktion:

![Skärm bild av alternativen för direkt uppspelning av skisser i Unreal](media/blueprint-streaming.png)

* **Fokusera** AcousticsSpace-aktören
* **Mitt position:** Mitten av regionen som kräver inlästa data
* **Ta bort avsökningar utanför panelen:** Om det här alternativet markeras tas alla avsökningar inte i den nya regionen bort från RAM-minnet. Om alternativet är avmarkerat läses den nya regionen in i minnet samtidigt som befintliga avsökningar också läses in i minnet
* **Blockera vid slut för ande:** Gör att panelen läser in en synkron åtgärd

Panel storleken måste redan anges innan Tvingad load-panel anropas. Du kan till exempel göra något som liknar detta för att läsa in en ACE-fil, ange storlek på din panel och strömma i en region:

![Skärm bild av installations alternativ för strömning i Unreal](media/streaming-setup.png)

Data skiss funktionen Load akustiska data som används i det här exemplet har följande parametrar:

* **Fokusera** AcousticsSpace-aktören.
* **Nytt bak:** Den akustiska data till gången ska läsas in. Om du lämnar den här tomma/inställningen till null inaktive ras den aktuella bagerien utan att en ny inläsning görs.

### <a name="optionally-query-for-surface-proximity"></a>Fråga efter Surface-närhet
Om du vill se hur nära lyssnare är i en viss riktning, kan du använda funktionen för att sträcka ut. Den här funktionen kan vara användbar för vägbeskrivning av försenade reflektioner eller för annan spel logik som styrs av Surface närhet. Frågan är billigare än en Ray-Cast eftersom resultaten hämtas från ljud uppslags tabellen.

![Skärm bild av exempel på skiss avstånds fråga](media/distance-query.png)

* **Fokusera** AcousticsSpace-aktören
* **Utseende riktning:** Riktningen att fråga i, centrerad vid lyssnaren
* **Mellanrummet** Om frågan lyckas, avståndet till närmaste yta
* **Retur värde:** Boolesk-True om frågan lyckades, annars FALSE

## <a name="next-steps"></a>Nästa steg
* Utforska koncepten bakom [design processen](design-process.md)
* [Skapa ett Azure-konto](create-azure-account.md) för att bageria din egen scen


