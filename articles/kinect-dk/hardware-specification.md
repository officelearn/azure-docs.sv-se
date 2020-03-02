---
title: Maskinvaruspecifikationer för Azure Kinect DK
description: Lär dig om komponenterna, specifikationerna och funktionerna i Azure Kinect DK.
author: tesych
ms.author: tesych
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/14/2020
ms.topic: article
keywords: azure, kinect, specs, hardware, DK, capabilities, depth, color, RGB, IMU, microphone, array, depth
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: e0d42a3ce1dd9deb5e73500371c367134ca852e1
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619967"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Maskinvaruspecifikationer för Azure Kinect DK

Den här artikeln beskriver hur Azure Kinect-maskinvaran integrerar Microsofts senaste sensorteknik i ett enda, USB-anslutet tillbehör.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Villkor

Följande förkortningar används genomgående i artikeln.

- NFOV (djupläge med smalt synfält)
- WFOV (djupläge med brett synfält)
- FOV (synfält)
- FPS (bildrutor per sekund)
- IMU (tröghetsmätningsenhet)
- FoI (intressefält)

## <a name="product-dimensions-and-weight"></a>Produktmått och vikt

Azure Kinect-enheten har följande mått och vikt.

- **Dimensioner**: 103 × 39 × 126 mm
- **Vikt**: 440 g

![Azure Kinect DK-mått](./media/resources/hardware-specs-media/dimensions.png)

## <a name="operating-environment"></a>Driftmiljö

Azure Kinect DK är avsett för utvecklare och kommersiella företag som arbetar i följande omgivningsförhållanden:

- **Temperatur**: 10–25 ⁰C
- **Luftfuktighet**: 8–90 % (icke-kondenserande) relativ luftfuktighet

> [!NOTE]
> Användning utanför dessa omgivningsförhållanden kan göra att enheten slutar fungera eller inte fungerar som den ska. Dessa omgivningsförhållanden gäller omgivningen nära enheten vid all form av drift. Om enheten är innesluten i ett externt hölje rekommenderar vi aktiv temperaturkontroll och/eller andra kyllösningar så att enheten alltid körs inom angivna intervall. Enheten har en kylkanal mellan fram- och baksidan. Kontrollera att kylkanalen inte blockeras när du implementerar enheten.

Läs [säkerhetsinformationen för produkten](https://support.microsoft.com/help/4023454/safety-information).

## <a name="depth-camera-supported-operating-modes"></a>Driftlägen som stöds av djupkameran

Azure Kinect DK integrerar en Microsoft-designad 1-megapixel ToF-djupkamera (Time-of-Flight) med [bildsensorn som presenterades vid ISSCC 2018](https://docs.microsoft.com/windows/mixed-reality/ISSCC-2018). Djupkameran stöder de lägen som anges nedan:

 | Läge            | Lösning | FoI       | FPS                | Driftintervall* | Exponeringstid |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV (ej intervall)   | 640 × 576    | 75 ° × 65 °   | 0, 5, 15, 30       | 0,5–3,86 m       | 12,8 ms        |
| NFOV 2 × 2 (intervall) (SW) | 320 × 288    | 75 ° × 65 °   | 0, 5, 15, 30       | 0,5–5,46 m       | 12,8 ms        |
| WFOV 2 × 2 (intervall) | 512 × 512    | 120 ° × 120 ° | 0, 5, 15, 30       | 0,25–2,88 m      | 12,8 ms        |
| WFOV (ej intervall)   | 1 024 × 1 024  | 120 ° × 120 ° | 0, 5, 15           | 0,25–2,21 m      | 20,3 ms        |
| Passiv IR      | 1 024 × 1 024  | Ej tillämpligt       | 0, 5, 15, 30       | Ej tillämpligt              | 1,6 ms         |

\*15 % till 95 % reflektans vid 850 nm, 2,2 μW/cm<sup>2</sup>/nm, standardavvikelse för slumpfel. ≤ 17 mm, typiskt systematiskt fel < 11 mm + 0,1 % av avståndet utan flervägsstörning. Djupet kan anges utanför det angivna driftintervallet ovan. Det beror på ett objekts reflektivitet.

## <a name="color-camera-supported-operating-modes"></a>Driftlägen som stöds av färgkameran

Azure Kinect DK har en OV12A10 12MP CMOS-sensor med rullande slutare. De inbyggda driftlägena anges nedan:

|             RGB-kameraupplösning (HxV)  |          Bredd–höjd-förhållande  |          Formatalternativ   |          Bildfrekvens (FPS)  |          Nominellt FOV (HxV) (efterbearbetning)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3 840 × 2 160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90 ° × 59 °                              |
|       2 560 × 1 440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90 ° × 59 °                              |
|       1 920 × 1 080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90 ° × 59 °                              |
|       1 280 × 720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90 ° × 59 °                              |
|       4 096 × 3 072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90 ° × 74,3 °                            |
|       2 048 × 1 536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90 ° × 74,3 °                            |

RGB-kameran är USB-videoklasskompatibel och kan användas utan Sensor SDK. RGB-kamerans färgområde: BT.601 fullt omfång [0..255]. 

> [!NOTE]
> Sensor SDK stöder färgbilder i BGRA-pixelformat. Det här är inte ett inbyggt läge som stöds av enheten och kräver extra processorbelastning. Värdprocessorn används för konvertering från MJPEG-bilder som tas emot från enheten.

## <a name="rgb-camera-exposure-time-values"></a>Värden för RGB-kamerans exponeringstider

Tabellen nedan innehåller acceptabla manuella exponeringsvärden för RGB-kameran:

| exp| 2^exp | 50 Hz   |60 Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250000|  80000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1000000| 120000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="depth-sensor-raw-timing"></a>Tidsinställning för rådata i djupsensorn

Djupläge | IR <br>Pulser | Puls <br>Bredd  | Inaktiv <br>Perioder| Inaktivitetstid | Exponering <br> Tid
-|-|-|-|-|-
NFOV (ej intervall) <br>  NFOV 2xx (intervall) <br> WFOV 2 × 2 (intervall) | 9 | 125 us | 8 | 1450 us | 12,8 ms 
WFOV (ej intervall)                                            | 9 | 125 us | 8 | 2390 us | 20,3 ms


## <a name="camera-field-of-view"></a>Kamerans synfält

Nästa bild visar djupkamerans och RGB-kamerans synfält, eller de vinklar som sensorerna ”ser”. Det här diagrammet visar RGB-kameran i 4:3-läge.

![Kamerans FOV](./media/resources/hardware-specs-media/camera-fov.png)

Den här bilden visar kamerans synfält framifrån från ett avstånd på 2 000 mm.

![Kamerans FOV framifrån](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Med djup i NFOV-läge har RGB-kameran bättre pixelöverlappning med 4:3- än med 16:9-upplösning.

## <a name="motion-sensor-imu"></a>Rörelsesensor (IMU)

Den integrerade tröghetsmätningsenheten (IMU) är en LSM6DSMUS och har både accelerometer och gyroskop. Accelerometern och gyroskopet samplas samtidigt vid 1,6 kHz. Samplingarna rapporteras till värden vid 208 Hz.

## <a name="microphone-array"></a>Mikrofonmatris

Azure Kinect DK integrerar en cirkulär matris med sju mikrofoner med hög kvalitet som visas som en USB-standardenhet för ljudklass 2.0. Alla 7 kanaler är tillgängliga. Prestandaspecifikationerna är som följer:

- Känslighet: -22 dBFS (94 dB SPL, 1 kHz)
- Signal-till-brus-förhållande > 65 dB
- Akustisk överbelastningspunkt: 116 dB

![Mikrofonhuvud](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Azure Kinect DK är en USB 3-kompositenhet som exponerar följande maskinvaruslutpunkter för operativsystemet:

Leverantörs-ID:t är 0x045E (Microsoft); se följande produkt-ID-tabell:

|    USB-gränssnitt        |    PNP-IP    |     Anteckningar            |
|-------------------------|--------------|----------------------|
|    USB 3.1 Gen1-hubb    |    0x097A    |    Primär hubb    |
|    USB 2.0-hubb         |    0x097B    |    HS  USB          |
|    Djupkamera       |    0x097C    |    USB 3.0            |
|    Färgkamera       |    0x097D    |    USB 3.0            |
|    Mikrofoner        |    0x097E    |    HS USB          |

## <a name="indicators"></a>Indikatorer

Enheten har en indikator för kameraströmning på framsidan som kan inaktiveras via programmering med hjälp av Sensor SDK.

Statuslampan bakom enheten indikerar enhetens tillstånd:

| När lampan     | Betyder det att                                                   |
|-----------------------|------------------------------------------------------------|
| Lyser med fast vitt sken           | Enheten är på och fungerar korrekt.                         |
| Blinkar med vitt sken        | Enheten är på men har ingen USB 3.0-dataanslutning.   |
| Blinkar med gult sken        | Enheten har inte tillräckligt med ström.               |
| Blinkar med växelvis gult och vitt sken  | Uppdatering av den inbyggda programvaran eller en återställning pågår                    |

## <a name="power-device"></a>Strömförsörjningsenhet

Enheten kan strömförsörjas på två sätt:

1. Med den medföljande strömkällan. Data ansluts med en separat ”USB Type-C till USB Type-A”-kabel.
2. Med en ”Type C till Type-C”-kabel för både ström och data.

En ”Type C till Type C”-kabel medföljer inte Azure Kinect DK.

> [!NOTE]
> - Den medföljande strömkabeln är en ”USB Type-A till enstiftskontakt”-anslutning. Använd den medföljande väggkontakten med den här kabeln. Enheten kan dra mer ström än vad två standard USB Type-A-portar kan ge.
> - USB-kablarna är viktiga och vi rekommenderar att du använder kablar av hög kvalitet samt att du kontrollerar att de fungerar innan du fjärrdistribuerar enheten.

> [!TIP]
> Så här väljer du en lämplig ”Type-C till Type-C”-kabel:
> - Den [USB-certifierade kabeln](https://www.usb.org/products) måste ha stöd för både ström och data.
> - En passiv kabel bör vara mindre än 1,5 m lång. Om den är längre använder du en aktiv kabel. 
> - Kabeln måste ha stöd för minst > 1,5 A. Annars måste du ansluta till en extern strömkälla.

Verifiera kabeln:

- Anslut enheten via kabeln till värddatorn.
- Kontrollera att alla enheter räknas upp korrekt i Enhetshanteraren i Windows. Djup- och RGB-kameran bör visas som i exemplet nedan.

  ![Azure Kinect DK i Enhetshanteraren](./media/resources/hardware-specs-media/device-manager.png)

- Kontrollera att kabeln kan strömma stabilt på alla sensorer i Azure Kinect Viewer, med följande inställningar:

  - Djupkamera: NFOV (ej intervall)
  - RGB-kamera: 2 160 p
  - Mikrofoner och IMU aktiverade

## <a name="what-does-the-light-mean"></a>Vad betyder lampan?

Strömindikatorn är en LED-lampa på baksidan av Azure Kinect DK. Färgen på lampan ändras beroende på enhetens status.

![Bilden visar baksidan av Azure Kinect DK. Det finns tre numrerade beskrivningar: en för en LED-indikator och, under den, två för kablar.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

Följande komponenter har märkts ut i den här bilden:

1. Strömindikator
1. Strömkabel (ansluts till strömkällan)
1. USB-C-datakabel (ansluts till datorn)

Kontrollera att kablarna är anslutna på samma sätt som i bilden. Titta sedan i följande tabell och se vad strömlampans olika lägen betyder.

|När lampan: |Betyder det att: |Och du bör: |
| ---| --- | --- |
|Lyser med fast vitt sken |Enheten är på och fungerar som den ska. |Använd enheten. |
|Inte lyser |Enheten är inte ansluten till datorn. |Kontrollera att den runda strömkabeln är ansluten till enheten och till USB-strömadaptern.<br /><br />Kontrollera att USB-C-kabeln är ansluten till enheten och till datorn. |
|Blinkar med vitt sken |Enheten är på men har ingen USB 3.0-dataanslutning. |Kontrollera att den runda strömkabeln är ansluten till enheten och till USB-strömadaptern.<br /><br />Kontrollera att USB-C-kabeln är ansluten till enheten och till en USB 3.0-port på datorn.<br /><br />Anslut enheten till en annan USB 3.0-port på datorn.<br /><br />Öppna Enhetshanteraren på datorn (**Start** > **Kontrollpanelen** > **Enhetshanteraren**) och kontrollera att datorn har en USB 3.0-värdstyrenhet som stöds. |
|Blinkar med gult sken |Enheten har inte tillräckligt med ström. |Kontrollera att den runda strömkabeln är ansluten till enheten och till USB-strömadaptern.<br /><br />Kontrollera att USB-C-kabeln är ansluten till enheten och till datorn. |
|Lyser gult och blinkar sedan med vitt sken |Enheten är på och en uppdatering av den inbyggda programvaran tas emot, eller så återställs enhetens fabriksinställningarna. |Vänta tills strömindikatorn lyser med vitt fast sken. Mer information finns i [Återställa Azure Kinect DK](reset-azure-kinect-dk.md). |

## <a name="power-consumption"></a>Strömförbrukning

Azure Kinect DK förbrukar upp till 5,9 W. Den specifika strömförbrukningen är användningsberoende.

## <a name="calibration"></a>Kalibrering

Azure Kinect DK kalibreras på fabriken. Kalibreringsparametrarna för visuella sensorer och tröghetssensorer stöder programmatisk frågekörning via Sensor SDK.

## <a name="device-recovery"></a>Enhetsåterställning

Enhetens inbyggda programvara kan återställas till den ursprungliga inbyggda programvaran med knappen under låsstiftet.

![Azure Kinect DK-återställningsknapp](./media/resources/hardware-specs-media/recovery.png)

Information om hur du återställer enheten finns i [instruktionerna här](reset-azure-kinect-dk.md).

## <a name="next-steps"></a>Nästa steg

- [Använda Azure Kinect Sensor SDK](about-sensor-sdk.md)
- [Konfigurera maskinvara](set-up-azure-kinect-dk.md)
