---
title: Inspektionsverktyget för ArrInspector
description: Användarhandbok för verktyget ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680081"
---
# <a name="the-arrinspector-inspection-tool"></a>Inspektionsverktyget för ArrInspector

ArrInspector är ett webbaserat verktyg som används för att granska en azure remote renderingssession som körs. Det är tänkt att användas för felsökning, för att inspektera strukturen på scenen som återges, visa loggmeddelanden och övervaka live-prestanda på servern.

![ArrInspector (en)](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Ansluta till ArrInspector

När du har hämtat `mixedreality.azure.com`värdnamnet (som slutar på) för ARR-servern ansluter du med [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Den här funktionen `StartArrInspector.html` skapar en på den enhet som programmet körs på. Om du vill starta ArrInspector öppnar du filen med en webbläsare (Edge, Firefox eller Chrome) på en dator. Filen är endast giltig i 24 timmar.

Om appen som `ConnectToArrInspectorAsync` anropar redan körs på en dator:

* Om du använder Unity-integreringen kan den startas automatiskt för dig.
* Annars hittar du filen i *User\\Folders\\LocalAppData\\[your_app] AC\\Temp*.

Om appen körs på en HoloLens:

1. Öppna HoloLens med [Windows Device Portal](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal).
1. Gå till *System > Utforskaren*.
1. Navigera till *användarmappar\\\\LocalAppData\\[your_app]\\AC Temp*.
1. Spara *StartArrInspector.html* på datorn.
1. Öppna *StartArrInspector.html* för att läsa in sessionens ArrInspector.

## <a name="the-performance-panel"></a>Panelen Prestanda

![Prestandapanelen](./media/performance-panel.png)

Den här panelen visar diagram över alla prestandavärden per bildruta som visas av servern. Värdena inkluderar för närvarande ramtid, FPS, CPU och minnesanvändning, minnesstatistik som övergripande RAM-användning, antal objekt, etc.

Om du vill visualisera en av dessa parametrar klickar du på knappen **Lägg till ny** och väljer ett av de tillgängliga värden som visas i dialogrutan. Den här åtgärden lägger till ett nytt rullande diagram på panelen och spårar värdena i realtid. Till höger kan du se *det lägsta,* *högsta* och *aktuella* värdet.

Du kan panorera diagrammet genom att dra dess innehåll med musen, men panorering vågrätt är bara möjligt när ArrInspector är i pausat tillstånd.

Om du håller ned CTRL medan du drar kan du zooma. Vågrät zoomning kan också styras med skjutreglaget längst ned.

Det lodräta området beräknas som standard baserat på de värden som för närvarande visas och värden för min och max visas i textrutorna till höger. När värdena ställs in manuellt, antingen genom att skriva in dem direkt i textrutan eller genom att panorera/zooma, kommer diagrammet att använda dessa värden. Om du vill återställa den automatiska lodräta inramningen klickar du på ikonen i det övre högra hörnet.

![lodrätt intervall](./media/vertical-range.png)

## <a name="the-log-panel"></a>Loggpanelen

![Loggpanel](./media/log-panel.png)

Loggpanelen visar en lista över loggmeddelanden som genereras på serversidan. Vid anslutning kommer det att visa upp till 200 tidigare loggmeddelanden, och kommer att skriva ut nya när de inträffar.

Du kan filtrera listan baserat `[Error/Warning/Info/Debug]` på loggtypen med hjälp av knapparna högst upp.
![Knappar för loggfilter](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Panelen Insamling av tidsdata

![Insamling av tidsinformation](./media/timing-data-capture.png)

Den här panelen används för att samla in tidsinformation från servern och hämta den. Filen använder [Chrome Tracing JSON-formatet](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Om du vill granska data `Chrome://tracing` öppnar du Chrome på webbadressen och drar och släpper den nedladdade filen på sidan. Tidsdata samlas kontinuerligt in i en ringbuffert i fast storlek. När den skrivs ut innehåller fångsten bara information om det omedelbara förflutna, vilket innebär ett par sekunder till några minuter.

## <a name="the-scene-inspection-panel"></a>Panelen Sceninspektion

![Panelen Scen inspektion](./media/scene-inspection-panel.png)

Den här panelen visar strukturen på den renderade scenen. Objekthierarkin finns till vänster, innehållet i det markerade objektet finns till höger. Panelen är skrivskyddad och uppdateras i realtid.

## <a name="the-vm-debug-information-panel"></a>Panelen Felsökning av virtuella datorer

![Informationspanel för felsökning för virtuella datorer](./media/state-debugger-panel.png)

Den här panelen erbjuder vissa felsökningsfunktioner.

### <a name="restart-service"></a>Starta om tjänsten

Knappen **Starta om tjänsten** startar om körningen på den virtuella datorn som arrInspector är ansluten till. Alla anslutna klienter kopplas från och arrInspector-sidan måste laddas om för att ansluta till den omstartade tjänsten.

### <a name="collect-debug-information"></a>Samla in felsökningsinformation

Knappen **Samla in felsökningsinformation för virtuell dator** öppnar en dialogruta som gör att du kan utlösa ARR-instansen för att samla in felsökningsinformation på den virtuella datorn:

![Dialogrutan För felsökning av virtuella datorer](./media/state-debugger-dialog.png)

Felsökningsinformation hjälper Azure Remote Rendering-teamet att analysera eventuella problem som uppstår i en ARR-instans som körs. Dialogrutan har ett textfält för att ge ytterligare information, till exempel steg för att återskapa ett problem.

När du har klickat på knappen **Starta insamling** stängs dialogrutan och insamlingsprocessen börjar. Det kan ta några minuter att samla in information om den virtuella datorn.

![Vm-felsökningsinformationsinsamling pågår](./media/state-debugger-panel-in-progress.png)

När samlingen är klar får du ett meddelande i fönstret ArrInspector. Det här meddelandet innehåller ett ID som identifierar just den här samlingen. Var noga med att spara det här ID:et för att vidarebefordra det till Azure Remote Rendering team.

![Framgång för den virtuella felsökningsinformationssamlingen](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Du kan inte hämta eller på annat sätt komma åt felsökningsinformation för virtuella datorer. Endast Azure Remote Rendering-teamet har åtkomst till insamlade data. Du måste kontakta oss och skicka insamlings-ID tillsammans, för att vi ska undersöka problemet du ser.

## <a name="pause-mode"></a>Pausläge

I det övre högra hörnet kan du med en switch pausa liveuppdateringen av panelerna. Det här läget kan vara användbart för att noggrant inspektera ett visst tillstånd.

![Pausläge](./media/pause-mode.png)

När liveuppdateringen återaktiverar återställs alla paneler.

## <a name="host-configuration"></a>Värdkonfiguration

Som standard ansluter verktyget till ARR-servern som körs på samma värd som betjänar ArrInspector. Du kan dock konfigurera den för att inspektera en annan server, förutsatt att den kör en ARR-instans med verktygsporten öppen.

Det gör du genom att öppna huvudmenyn till vänster om rubrikfältet och välja *Värdkonfiguration*. Klicka på **Lägg till ny värd**och ange namn och värdnamn. För *värdnamn* använder du bara `.mixedreality.azure.com`värdnamnet som `http://` slutar på , inkludera inte eller en port.

![Värdkonfiguration](./media/host-configuration.png)

Om du snabbt vill växla från en värd till en annan använder du rullgardinsmenyn längst upp till höger.

![Värdkombination](./media/host-switch-combo.png)

Värdlistan lagras i webbläsarens lokala lagring, så den bevaras när samma webbläsare öppnas igen.
