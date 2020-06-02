---
title: Media Graph-koncept – Azure
description: Med medie diagram kan du definiera var media ska samlas in, hur de ska bearbetas och var resultaten ska levereras. Den här artikeln innehåller en detaljerad beskrivning av media Graph-konceptet.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: ee055c34fd37d2d1cc5e7d0bb5147c945dcbff94
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84260969"
---
# <a name="media-graph-concept"></a>Media diagram-koncept

## <a name="suggested-pre-reading"></a>Föreslagen för läsning

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)

## <a name="overview"></a>Översikt

Med medie diagram kan du definiera var media ska samlas in, hur de ska bearbetas och var resultaten ska levereras. Ett medie diagram består av noder av källa, processor och mottagare. Diagrammet nedan innehåller en grafisk representation av ett medie diagram.   

![En grafisk representation av ett medie diagram](./media/media-graph/overview.png)


* **Källnoden** möjliggör inhämtning av media i medie diagrammet. Media i den här kontexten kan konceptuellt vara en ljud ström, en video ström, en data ström eller en ström som innehåller ljud, video och/eller data som kombineras tillsammans i en enda ström.
* **Processor noder** möjliggör bearbetning av media i medie diagrammet.
* Med **Sink-noder** kan du leverera bearbetnings resultatet till tjänster och appar utanför medie diagrammet.

## <a name="media-graph-topologies-and-instances"></a>Media Graph-topologier och instanser 

Med live video analys på IoT Edge kan du hantera medie diagram via två koncept – "Graph Topology" och "Graph instance". Med en Graph-topologi kan du definiera en skiss av en graf, med parametrar som plats hållare för värden. Topologin definierar vilka noder som används i medie diagrammet och hur de är anslutna i medie diagrammet. Värdena för parametrarna kan anges när du skapar graf-instanser som refererar till topologin. På så sätt kan du skapa flera instanser som refererar till samma topologi, men med olika värden för de parametrar som anges i topologin. 

## <a name="media-graph-states"></a>Medie diagram status  

Medie diagram kan vara i något av följande tillstånd:

* Inaktiv – representerar det tillstånd där ett medie diagram är konfigurerat men inte aktivt.
* Aktivering – tillståndet när ett medie diagram instansieras (det vill säga över gångs tillståndet mellan inaktiv och aktiv).
* Aktiv – statusen när ett medie diagram är aktivt. 

    > [!NOTE]
    >  Media Graph kan vara aktiv utan data som flödar genom den (till exempel att video källan för indata är offline).
* Inaktive ring – det här är tillståndet när ett medie diagram övergår från aktiv till inaktiv.

Diagrammet nedan illustrerar tillstånds datorn för media Graph.

![Dator för media Graph-tillstånd](./media/media-graph/media-graph-state-machine.png)

## <a name="sources-processors-and-sinks"></a>Källor, processorer och mottagare  

Live video analys på IoT Edge stöder följande typer av noder i ett medie diagram:

### <a name="sources"></a>Källor 

#### <a name="rtsp-source"></a>RTSP-källa 

En RTSP-källa möjliggör hämtning av media från en [RTSP](https://tools.ietf.org/html/rfc2326) -Server. RTSP används för att upprätta och kontrol lera medie sessioner mellan en server och en klient. Noden RTSP-källa i medie diagrammet fungerar som en klient och kan upprätta en session med den angivna RTSP-servern. Många enheter, till exempel de flesta [IP-kameror](https://en.wikipedia.org/wiki/IP_camera) har en inbyggd RTSP-server. [ONVIF](https://www.onvif.org/) bestämmer att RTSP ska stödjas i sin definition av [profiler G, S & T-](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) kompatibla enheter. Noden RTSP-källa i medie diagram kräver att du anger en RTSP-URL, tillsammans med autentiseringsuppgifter för att aktivera en autentiserad anslutning.

#### <a name="iot-hub-message-source"></a>IoT Hub meddelande källa 

Precis som andra [IoT Edge moduler](../../iot-edge/iot-edge-glossary.md#iot-edge-module)kan live video analys i IoT Edge-modulen ta emot meddelanden via [IoT Edge hubben](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Dessa meddelanden kan skickas från andra moduler eller appar som körs på gräns enheten eller från molnet. Sådana meddelanden kan levereras (dirigeras) till en [namngiven inmatning](../../iot-edge/module-composition.md#sink) i modulen. Med en IoT Hub meddelande källa kan sådana meddelanden dirigeras till en Media Graph-instans. Dessa meddelanden eller signaler kan sedan användas internt i medie diagrammet, vanligt vis för att aktivera signal portar (se [signal grind processor](#signal-gate-processor) nedan). 

Du kan till exempel ha en IoT Edge-modul som genererar ett meddelande när en dörr öppnas. Meddelandet från modulen kan dirigeras till IoT Edge Hub, där det kan dirigeras till IoT Hub-meddelande källan för ett medie diagram. I medie grafen kan IoT Hub-meddelande källan skicka händelsen till en signal grind processor, som sedan kan aktivera inspelning av videon från en RTSP-källa till en fil. 

### <a name="processors"></a>Processorer  

#### <a name="motion-detection-processor"></a>Processor för rörelse identifiering 

Med processen för identifiering av rörelser kan du identifiera rörelse i live video. Den undersöker inkommande video och avgör om det finns rörelse i videon. Om rörelsen upptäcks, skickas den på videon till den underordnade noden och en händelse skapas. Rörelse identifierings processor (tillsammans med andra Media diagram-noder) kan användas för att utlösa inspelning av den inkommande videon när en rörelse upptäcks.

#### <a name="frame-rate-filter-processor"></a>Filter processor för RAM hastighet  

Med filter processorn för RAM hastighet kan du sampla ramar från den inkommande video strömmen med en angiven bild hastighet. På så sätt kan du minska antalet ramar som skickas till Datastream-noder (till exempel HTTP Extension processor) för vidare bearbetning.

#### <a name="http-extension-processor"></a>Processor för HTTP-tillägg 

Med HTTP-tillägget kan du koppla ditt eget AI till ett medie diagram. HTTP-tillägget tar som inkodade video bild rutor och vidarebefordrar sådana ramar till en HTTP-slutpunkt. Processorn har möjlighet att autentisera med HTTP-slutpunkten om det behövs. Dessutom har processorn inbyggd avbildnings-Formatter som tillåter skalning och kodning av video ramar innan de vidarebefordras. Skalning har alternativ för bild-höjd-förhållandet som ska bevaras, fyllas i eller sträckas ut medan kodningen innehåller alternativ för olika bild kodning, till exempel JPEG, PNG eller BMP.

#### <a name="signal-gate-processor"></a>Signal grind processor  

Med signal grind processorn kan Media vidarebefordras villkorligt från en nod till en annan. Den fungerar även som en buffert som tillåter synkronisering av medier och händelser. Ett exempel på användnings fall är att infoga en signal grind processor mellan en RTSP-källa och en till gångs mottagare och använda utdata från motion detektor-processorn för att utlösa porten. Med ett sådant medie diagram kan du bara utlösa inspelning av media när rörelse upptäcks i den inkommande videon. 

### <a name="sinks"></a>Mottagare  

#### <a name="asset-sink"></a>Till gångs mottagare  

Med en till gångs mottagare kan ett medie diagram skriva medie data (video och/eller ljud) till en Azure Media Services till gång. Se avsnittet [till gång](terminology.md#asset) för mer information om till gångar och deras roll vid inspelning och uppspelning av media.  

#### <a name="file-sink"></a>Fil mottagare  

Filen Sink gör det möjligt för ett medie diagram att skriva medie data (video och/eller ljud) till en plats i det lokala fil systemet på den IoT Edge enheten. Fil mottagaren måste vara underordnad en Signals grind processor. Detta begränsar längden på utdatafilerna till värden som anges i egenskaperna för signal porten processor.

#### <a name="iot-hub-message-sink"></a>IoT Hub meddelande mottagare  

Med en IoT Hub meddelande mottagare kan du publicera händelser till IoT Edge Hub. Edge Hub kan sedan dirigera data till andra moduler eller appar på gräns enheten eller till IoT Hub i molnet (per vägar som anges i distributions manifestet). IoT Hub meddelande mottagare kan acceptera händelser från överordnade processorer, till exempel en rörelse identifierings processor, eller från en extern härlednings tjänst via HTTP-tilläggs processor.

## <a name="scenarios"></a>Scenarier

Genom att använda en kombination av noderna källa, processor och mottagare, som definieras ovan, kan du bygga medie diagram för olika scenarier. Några exempel scenarier är följande

* [Kontinuerlig inspelning av video](continuous-video-recording-concept.md)
* [Händelse-baserad videoinspelning](event-based-video-recording-concept.md)
* [Video analys utan videoinspelning](analyze-live-video-concept.md)

## <a name="next-steps"></a>Nästa steg

Följ [snabb starten: kör live video analys med din egen modell](use-your-model-quickstart.md) artikel för att se hur du kan köra rörelse identifiering i en Live-videofeed.
