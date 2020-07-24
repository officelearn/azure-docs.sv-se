---
title: Live video analys på IoT Edge FAQ – Azure
description: Det här avsnittet innehåller svar på video analys i real tid för IoT Edge vanliga frågor och svar.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011523"
---
# <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar

Det här avsnittet innehåller svar på video analys i real tid för IoT Edge vanliga frågor och svar.

## <a name="general"></a>Allmänt

Vilka systemvariabler kan användas i definition av diagram miljön?

|Variabel   |Beskrivning|
|---|---|
|[System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Representerar en omedelbar tid, vanligt vis uttryckt som datum och tid på dagen.|
|System. GraphTopologyName   |Representerar en medie diagram sto pol Ogin, innehåller skissen på ett diagram.|
|System. GraphInstanceName|  Representerar en medie diagram instans, innehåller parameter värden och refererar till topologin.|

## <a name="configuration-and-deployment"></a>Konfiguration och distribution

Kan jag distribuera Media Edge-modulen till en Windows 10-enhet?
    * Ja. Se artikeln om [Linux-behållare i Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Avbilda från IP-kamera och RTSP-inställningar

* Behöver jag använda ett särskilt SDK på enheten för att skicka in en videoström?
    * Nej. Video analys i real tid med IoT Edge stöder hämtning av media med RTSP-video strömnings protokoll (som stöds på de flesta IP-kameror).
* Kan jag skicka media till direktsänd video analys på IoT Edge med hjälp av RTMP eller utjämna (som ett Media Services live event)?
    * Nej. LVA stöder bara RTSP för att hämta video från IP-kameror.
    * Alla kameror som stöder RTSP-strömning via TCP/HTTP bör fungera. 
* Kan jag återställa eller uppdatera RTSP-källans URL på en diagraminstans?
    * Ja, när diagram instansen är i inaktivt läge.  
* Finns det en RTSP-simulator som kan användas vid testning och utveckling?
    * Ja. Det finns en [RTSP Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge-modul som är tillgänglig för användning i snabb starter och självstudier för att stödja inlärnings processen. Den här modulen tillhandahålls då det är möjligt, men kanske inte alltid är tillgänglig. Vi rekommenderar starkt att du inte använder detta i mer än några timmar. Du bör investera i testning med din faktiska RTSP-källa innan du gör några planer på en produktions distribution.
* Finns det stöd för ONVIF-identifiering av IP-kameror vid nätverksgränsen?
    * Nej, det finns inget stöd för ONVIF-identifiering av enheter vid gränsen.

## <a name="streaming-and-playback"></a>Strömning och uppspelning

* Kan till gångar som registrerats för AMS från kanten spelas upp med Media Services strömmande tekniker som HLS eller tank streck?
    * Ja. Registrerade till gångar kan strömmas på samma sätt som andra till gångar i Azure Media Services. För att strömma innehållet måste du ha en slut punkt för direkt uppspelning skapad och i körnings läge. Genom att använda standard processen för att skapa streaming-direktuppspelning får du till gång till ett HLS-eller tank strecks manifest för strömning till valfritt Player-ramverk. Mer information om hur du skapar publicerings-HLS eller streck manifest finns i [dynamisk paketering](../latest/dynamic-packaging-overview.md).
* Kan jag använda standard innehålls skyddet och DRM-funktionerna i Media Services på en arkiverad till gång?
    * Ja. Alla standard funktioner för dynamisk kryptering och DRM-funktioner är tillgängliga för användning på de till gångar som registrerats från ett medie diagram.
* Vilka spelare kan jag använda för att visa innehåll från de inspelade resurserna?
   * Alla standard spelare som stöder kompatibel Apple HTTP Live Streaming (HLS) version 3 eller version 4 stöds. Dessutom stöds även alla spelare som stöder uppspelning av MPEG-streck.
    Rekommenderade spelare för testning är:

    * [Azure Media Player](../latest/use-azure-media-player.md)
    * [HLS.js](https://hls-js.netlify.app/demo/)
    * [Video.js](https://videojs.com/)
    * [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
    * [Shaka-spelare](https://github.com/google/shaka-player)
    * [ExoPlayer](https://github.com/google/ExoPlayer)
    * [Apple Native HTTP Live Streaming](https://developer.apple.com/streaming/)
    * Edge, Chrome eller Safari inbyggd HTML5 Videos pelare
    * Kommersiella spelare som stöder HLS-eller streck uppspelning
* Vilka är gränserna för strömning av en Media diagram-till gång?
    * Direkt uppspelning av en levande eller inspelad till gång från ett medie diagram använder samma infrastruktur för hög skalnings-och strömnings slut punkt som Media Services stöd för OTT på begäran och live-strömning för media & underhållning, och sändnings kunder. Det innebär att du snabbt och enkelt kan aktivera Azure CDN, Verizon eller Akamai för att leverera ditt innehåll till en mål grupp så litet som några visnings program, eller upp till miljon tals beroende på ditt scenario.

    Innehåll kan levereras med både Apple HTTP Live Streaming (HLS) eller MPEG-tank streck.

## <a name="monitoring-and-metrics"></a>Övervakning och mått

* Kan jag övervaka medie diagrammet på gränsen med Event Grid?
    * Nej. För närvarande stöds inte Event Grid.
* Kan jag använda Azure Monitor för att Visa hälso tillstånd, mått och prestanda för mina medie diagram i molnet eller på gränsen?
    * Nej.
* Finns det några verktyg för att göra det enklare att övervaka modulen Media Services IoT Edge?
    * Visual Studio Code stöder tillägget "Azure IoT Tools" som gör att du enkelt kan övervaka slut punkterna i LVAEdge-modulen. Du kan använda det här verktyget för att snabbt starta övervakning av den IoT Hub inbyggda slut punkten för "händelser" och se de härlednings meddelanden som dirigeras från gräns enheten till molnet. 

    Dessutom kan du använda det här tillägget för att redigera modulen för LVAEdge-modulen för att ändra inställningarna för media Graph.

Mer information finns i artikeln om [övervakning och loggning](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Fakturering och tillgänglighet

* Hur debiteras LiveVideo-analys på IoT Edge?
    * Mer information finns på [sidan med pris](https://azure.microsoft.com/pricing/details/media-services/) information.

## <a name="next-steps"></a>Nästa steg

[Snabb start: kom igång – direktsända video analyser på IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
