---
title: Skapa ett mappningsprogram med Azure Maps | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du skapar ett program med hjälpmedelsfunktioner med Hjälp av Microsoft Azure Maps.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473327"
---
# <a name="building-an-accessible-application"></a>Bygga ett tillgängligt program

Uppemot 20 % av internetanvändarna har ett behov av tillgängliga webbapplikationer. Därför är det viktigt att se till att ditt program är utformat så att alla användare enkelt kan använda det. I stället för att tänka på tillgänglighet som en uppsättning uppgifter att slutföra, se det som en del av din totala användarupplevelse. Ju mer tillgängligt ditt program är, desto fler personer som kan använda det. 

När det gäller rikt interaktivt innehåll som en karta är några vanliga tillgänglighetsöverväganden:
- Stöd skärmläsaren för användare som har svårt att se webbprogrammet.
- Ha flera metoder för att interagera med och navigera i webbprogrammet, till exempel mus, touch och tangentbord.
- Se till att färgkontrasten är sådan att färgerna inte smälter samman och blir svåra att skilja från varandra. 

Azure Maps Web SDK levereras förbyggt med många hjälpmedelsfunktioner, till exempel:
- Beskrivningar av skärmläsare när kartan flyttas och när användaren fokuserar på en kontroll eller popup.
- Stöd för mus, tryck och tangentbord.
- Stöd för tillgänglig färgkontrast i vägkartstilen.

Fullständig information om tillgänglighetskonformance för alla Microsoft-produkter finns [här](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Sök efter "Azure Maps web" för att hitta dokumentet specifikt för Azure Maps Web SDK. 

## <a name="navigating-the-map"></a>Navigera på kartan

Det finns flera olika sätt på vilka kartan kan zoomas, panoreras, roteras och kastas. Följande beskriver alla olika sätt att navigera på kartan.

**Zooma kartan**

- Dubbelklicka på kartan med hjälp av en mus för att zooma in en nivå.
- Rulla hjulet med hjälp av en mus för att zooma kartan.
- Tryck på kartan med två fingrar med hjälp av en pekskärm och nyp ihop för att zooma ut eller sprida isär fingrarna för att zooma in.
- Med hjälp av en pekskärm trycker du dubbel på kartan för att zooma in en nivå.
- Med kartan fokuserad använder du`+`plustecknet ( )`=`eller Likhetstecknet ( ) för att zooma in en nivå.
- Med kartan fokuserad använder du minustecknet,`-`bindestrecket ( eller Understreck (`_`) för att zooma ut en nivå.
- Använda zoomkontrollen med en mus, pek- eller tangentbordsflik/ange tangenter.
- Tryck och `Shift` håll ned knappen och tryck ned vänster musknapp på kartan och dra för att rita ut ett område som kartan ska zoomas in i.

**Panorera kartan**

- Tryck ned med vänster musknapp på kartan med hjälp av en mus.
- Tryck på kartan med en pekskärm och dra i valfri riktning.
- Med kartan fokuserad använder du piltangenterna för att flytta kartan.

**Rotera kartan**

- Tryck ned med höger musknapp på kartan med hjälp av en mus och dra åt vänster eller höger. 
- Tryck på kartan med två fingrar med hjälp av en pekskärm och rotera.
- Med kartan fokuserad använder du skifttangenten och vänster- eller högerpiltangenterna.
- Använda rotationskontrollen med en mus, pek- eller tangentbordsflik/ange tangenter.

**Pitcha kartan**

- Tryck ned med höger musknapp på kartan med musen och dra uppåt eller nedåt. 
- Tryck på kartan med två fingrar med hjälp av en pekskärm och dra dem uppåt eller nedåt.
- Med kartan fokuserad använder du skifttangenten plus upp- eller nedpiltangenterna. 
- Använda tonhöjdskontrollen med en mus, pek- eller tangentbordsflik/ange tangenter.

## <a name="change-the-map-style"></a>Ändra kartformat

Alla utvecklare vill inte att alla möjliga kartformat ska vara tillgängliga i deras program. Om utvecklaren visar stilväljarens kontroll över kartan kan användaren ändra kartformatet med hjälp av musen, en knapptryckning eller tangentbordet med fliken eller ange tangenten. Utvecklaren kan ange vilka kartformat de vill göra tillgängliga i kartstilsväljarens kontroll. Dessutom kan utvecklaren programmässigt ställa in och ändra kartstilen.

**Använd högkontrast**

- När kartkontrollen har lästs in kontrollerar den om hög kontrast är aktiverad och webbläsaren stöder den.
- Kartkontrollen övervakar inte enhetens högkontrastläge. Om enhetsläget ändras kommer kartan inte att göra det. Således måste användaren ladda om kartan genom att uppdatera sidan.
- När hög kontrast upptäcks växlar kartstilen automatiskt till hög kontrast, och alla inbyggda kontroller använder ett format med hög kontrast. ZoomControl, PitchControl, CompassControl, StyleControl och andra inbyggda kontroller använder till exempel en högkontraststil.
- Det finns två typer av hög kontrast, ljus och mörk. Om typen av hög kontrast kan identifieras av kartkontrollerna justeras kartans beteende i enlighet med detta. Om ljus, kommer grayscale_light kartstilen att laddas. Om typen inte kan identifieras eller är mörk läses high_contrast_dark-formaten in.
- Om du skapar anpassade kontroller är det bra att veta om de inbyggda kontrollerna använder ett format med hög kontrast. Utvecklare kan lägga till en css-klass på kartan behållare div att kontrollera. Css klasser som skulle `high-contrast-dark` läggas `high-contrast-light`till är och . Om du vill kontrollera med JavaScript använder du:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

eller, använd:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Kortkommandon

Kartan har ett antal kortkommandon inbyggda som gör det enklare att använda kartan. Dessa kortkommandon fungerar när kartan har fokus.

| Nyckel      | Åtgärd                            |
|----------|-----------------------------------|
| `Tab` | Navigera över kontrollerna och popup-fönsteren på kartan. |
| `ESC` | Flytta fokus från alla element på kartan till kartelementet på den översta nivån. |
| `Ctrl` + `Shift` + `D` | Växla informationsnivå för skärmläsare.  |
| Vänsterpil | Panorera kartan till vänster 100 pixlar |
| Högerpil | Panorera kartan till höger 100 pixlar |
| Nedpil | Panorera kartan nedåt 100 pixlar |
| Uppil | Panorera kartan upp 100 pixlar |
| `Shift`+ uppåtpil | Öka karthöjd med 10 grader |
| `Shift`+ nedåtpil | Minska karthöjd med 10 grader |
| `Shift`+ högerpil | Rotera kartan 15 grader medurs |
| `Shift`+ vänsterpil | Rotera kartan 15 grader moturs |
| Plustecken`+`( <sup>*</sup>) eller`=`Likhetstecken ( ) | Zooma in |
| Minustecken, bindestreck`-` <sup>*</sup>(`_`) eller Understreck ( ) | Zooma ut | 
| `Shift`+ mus dra på kartan för att rita område | Zooma in i området |

<sup>*</sup>Dessa kortkommandon delar vanligtvis samma tangent på ett tangentbord. Dessa genvägar har lagts till för att förbättra användarupplevelsen. Det spelar heller ingen roll om användaren använder skift-tangenten eller inte för dessa genvägar.

## <a name="screen-reader-support"></a>Stöd för Skärmläsare

Användare kan navigera på kartan med hjälp av tangentbordet. Om en skärmläsare körs meddelar kartan användaren om ändringar i dess tillstånd. Användarna meddelas till exempel om kartändringar när kartan panoreras eller zoomas. Som standard innehåller kartan förenklade beskrivningar som utesluter zoomningsnivån och koordinaterna för mitten av kartan. Användaren kan växla detaljnivån för dessa beskrivningar med `Ctrl`  +  `Shift`  +  `D`hjälp av tangentbordet genväg .

All ytterligare information som placeras på baskartan bör ha motsvarande textinformation för skärmläsare användare. Var noga med att lägga till [ARIA-attribut (Accessible Rich Internet Applications),](https://www.w3.org/WAI/standards-guidelines/aria/)alt och title när så är lämpligt. 

## <a name="make-popups-keyboard-accessible"></a>Göra popup-tangentbord tillgängligt

En markör eller symbol används ofta för att representera en plats på kartan. Ytterligare information om platsen visas vanligtvis i ett popup-fönster när användaren interagerar med markören. I de flesta program visas popup-fönster när en användare klickar eller trycker på en markör. Men om du klickar och trycker måste användaren använda en mus respektive en pekskärm. En god praxis är att göra popup-fönster tillgängliga när du använder ett tangentbord. Den här funktionen kan uppnås genom att skapa en popup för varje datapunkt och lägga till den på kartan. 

I följande exempel läses intressanta platser på kartan med hjälp av ett symbollager och en popup-till-kartan läggs till för varje intressant punkt. En referens till varje popup lagras i egenskaperna för varje datapunkt. Det kan också hämtas för en markör, till exempel när du klickar på en markör. När du fokuserar på kartan, genom att trycka på tabbtangenten gör det möjligt för användaren att gå igenom varje popup på kartan.

<br/>

<iframe height='500' scrolling='no' title='Göra ett tillgängligt program' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pennan <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Gör ett tillgängligt</a> program<a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Ytterligare hjälpmedelstips

Här är några ytterligare tips för att göra ditt webbmappningsprogram mer tillgängligt.

- Om du visar många interaktiva punktdata på kartan bör du överväga att minska röran och använda klustring. 
- Se till att förhållandet mellan text/symboler och bakgrundsfärger är 4.5:1 eller mer.
- Håll skärmläsaren (ARIA-, alt- och titelattributen) meddelanden korta, beskrivande och meningsfulla. Undvik onödig jargong och akronymer.
- Försök att optimera meddelanden som skickas till skärmläsaren för att ge kort meningsfull information som är lätt för användaren att smälta. Om du till exempel vill uppdatera skärmläsaren med hög frekvens, till exempel när kartan flyttas, kan du överväga att göra följande:
    - Vänta tills kartan har flyttats för att uppdatera skärmläsaren.
    - Begränsa uppdateringarna till en gång med några sekunders mellanrum. 
    - Kombinera meddelanden tillsammans på ett logiskt sätt. 
- Undvik att endast förmedla information med hjälp av färger. Använd text, ikoner eller mönster för att komplettera eller ersätta färgen. Några överväganden:
    - Om du använder ett bubbellager för att visa det relativa värdet mellan datapunkter bör du överväga att skala radien för varje bubbla, färglägga bubblan eller båda. 
    - Överväg att använda ett symbollager med olika ikoner för olika måttkategorier, till exempel trianglar, stjärnor och fyrkanter. Symbollagret stöder också skalning av ikonens storlek. En textetikett kan också visas.
    - Om du visar linjedata kan bredden användas för att representera vikt eller storlek. Ett mönster för streckmatris kan användas för att representera olika kategorier av linjer. Ett symbollager kan användas i kombination med en linje för att överlagra ikoner längs linjen. Det är användbart att använda en pilikon för att visa linjens flöde eller riktning.
    - Om polygondata visas kan ett mönster, till exempel ränder, användas som ett alternativ till färg. 
- Vissa visualiseringar, till exempel värmekartor, panellager och bildlager, är inte tillgängliga för användare med synnedsättningar. Några överväganden:
    - Låt skärmläsaren beskriva vad lagret visar när det läggs till på kartan. Om till exempel ett väderradarpanellager visas, låt skärmläsaren säga "Väderradardata läggs över på kartan".
- Begränsa mängden funktioner som kräver en muspekare. Dessa funktioner är otillgängliga för användare som använder ett tangentbord eller en pekenhet för att interagera med ditt program. Det är fortfarande bra att ha en hovringsstil för interaktivt innehåll som klickbara ikoner, länkar och knappar.
- Prova att navigera i programmet med tangentbordet. Kontrollera att tabbordningen är logisk.
- Om du skapar kortkommandon kan du försöka begränsa det till två tangenter eller mindre. 

## <a name="next-steps"></a>Nästa steg

Läs mer om hjälpmedel i Webb-SDK-modulerna.

> [!div class="nextstepaction"]
> [Hjälpmedel för ritverktyg](drawing-tools-interactions-keyboard-shortcuts.md)

Läs mer om hur du utvecklar appar med microsoft learn:

> [!div class="nextstepaction"]
> [Hjälpmedel i action-utbildningsväg för digitala märken](https://ready.azurewebsites.net/learning/track/2940)

Ta en titt på de här användbara hjälpmedelsverktygen:
> [!div class="nextstepaction"]
> [Utveckla tillgängliga appar](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-ARIA Översikt](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Utvärderingsverktyg för webbtillgänglighet (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Kontrastkontroll av webAim-färg](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Ingen kaffe vision Simulator](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
