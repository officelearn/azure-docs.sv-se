---
title: Nätverkskrav
description: Nätverkskrav och bästa nätverkspraxis för optimal upplevelse
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 73bbfad4c0535fa00b1aa53764eb52acb83124f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680380"
---
# <a name="network-requirements"></a>Nätverkskrav

En stabil nätverksanslutning med låg latens till ett Azure-datacenter är avgörande för en bra användarupplevelse i Azure Remote Rendering. Dåliga nätverksförhållanden kan resultera i tappade anslutningar, instabila, skakis eller "hoppning" hologram, och märkbar fördröjning vid uppdatering av serversidan scendiagrammet.

## <a name="guidelines-for-network-connectivity"></a>Riktlinjer för nätverksanslutning

De exakta nätverkskraven beror på ditt specifika användningsfall, till exempel antalet och frekvensen av ändringar i fjärrscendiagrammet samt komplexiteten i den renderade vyn, men det finns ett antal riktlinjer för att säkerställa att din upplevelse är så bra som möjligt:

* Din internetanslutning måste stödja minst **50 Mbit/s nedströms** och **10 Mbit/s uppströms** konsekvent för en enda användarsession med Azure Remote Rendering, förutsatt att det inte finns någon konkurrerande trafik i nätverket. Vi rekommenderar högre priser för bättre upplevelser. Med fler användare i samma nätverk skalas dessa krav upp på motsvarande sätt.
* Med **5 GHz Wi-Fi-bandet** brukar ge bättre resultat än 2,4 GHz Wi-Fi-bandet, även om båda ska fungera.
* Om det finns andra Wi-Fi-nätverk i närheten kan du undvika att använda Wi-Fi-kanaler som används av dessa andra nätverk. Du kan använda nätverksskanningsverktyg som [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) för att kontrollera om kanalerna i ditt Wi-Fi-nätverk använder är fria från konkurrerande trafik.
* Undvik **att använda Wi-Fi-repeatrar** eller LAN-over-powerline-vidarebefordran.
* **Undvik konkurrerande bandbreddsintensiv trafik** – till exempel video- eller spelstreaming – i samma Wi-Fi-nätverk.
* Att ha **bra Wi-Fi-signalstyrka** är viktigt. Om möjligt, håll dig nära din Wi-Fi-åtkomstpunkt och undvik hinder mellan klientenheten och åtkomstpunkterna.
* Se till att du alltid ansluter till **närmaste Azure-datacenter** för din [region](regions.md). Ju närmare datacenter, desto lägre nätverksfördröjning, vilket har en enorm effekt på hologramstabiliteten.

## <a name="network-performance-tests"></a>Nätverksprestandatester

Om du vill få en första förståelse för om kvaliteten på nätverksanslutningen är tillräcklig för att köra Azure Remote Rendering finns det befintliga onlineverktyg som du kan använda. Vi rekommenderar starkt att du kör dessa onlineverktyg från en någorlunda kraftfull bärbar dator som är ansluten till samma Wi-Fi som den enhet som du planerar att köra ditt Azure Remote Rendering-klientprogram på. Resultat som erhållits från att köra testerna på en mobiltelefon eller HoloLens2 är vanligtvis mindre användbara, eftersom de har visat sig visa betydande variation på lågeffektsenheter. Den plats där du placerar den bärbara datorn bör vara ungefär på samma plats där du förväntar dig att använda enheten som kör ditt Azure Remote Rendering-klientprogram.

Här är några enkla steg för ett snabbt test av nätverksanslutningen:

1. **Kör ett nätverkstestverktyg som www.speedtest.net för att hämta data om den övergripande svarstiden och bandbredden uppströms/nedströms för nätverksanslutningen.**
Välj en server närmast dig och kör testet. Även om servern inte kommer att vara Det Azure-datacenter som Azure Remote Rendering kommer att ansluta till, är de resulterande data fortfarande användbara för att förstå prestanda för din internetanslutning och Wi-Fi.
   * **Minimikrav för** Azure Remote Rendering: Ca 40 Mbps nedströms och 5 Mbps uppströms.
   * **Rekommenderas** för Azure Remote Rendering: Ca. 100 Mbps nedströms och 10 Mbps uppströms.
Vi rekommenderar att du kör testet flera gånger och tar de sämsta resultaten.
1. **Använd ett verktyg som www.azurespeed.com som mäter svarstid till Azure-datacenter**. Välj det Azure-datacenter som stöds av Azure Remote Rendering som är närmast dig (se [regioner som stöds)](regions.md)och kör ett **svarstidstest**. Om det finns variation i de tal du ser, ge resultaten lite tid att stabilisera.
   * **Minimikrav för** Azure Remote Rendering: Svarstiden bör konsekvent vara mindre än 100 ms.
   * **Rekommenderas** för Azure Remote Rendering: Svarstiden bör konsekvent vara mindre än 70 ms.

Låg latens är inte en garanti för att Azure Remote Rendering fungerar bra i nätverket, men vi har oftast sett det fungera bra i situationer där dessa tester har godkänts.
Om du stöter på artefakter som instabila, skakis eller hoppar hologram när du kör Azure Remote Rendering läser du [felsökningsguiden](../resources/troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Återge en modell med Unity](../quickstarts/render-model.md)
