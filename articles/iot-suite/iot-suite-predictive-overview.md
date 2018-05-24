---
title: Översikt över lösningsacceleratorn Förutsägande underhåll – Azure | Microsoft Docs
description: En beskrivning av lösningsacceleratorn Förutsägande underhåll i Azure.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 4b1667b6449f9e425ea601f47f91bfe8cce8538e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304215"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Översikt över lösningsacceleratorn Förutsägande underhåll

[Lösningsacceleratorn][lnk_preconfigured_solutions] *Förutsägande underhåll* är en av [Microsoft Azure IoT-lösningsacceleratorerna][lnk_iot_suite] lösningsacceleratorer. Den här lösningen integrerar realtidsinsamling av enhetstelemetri med en förebyggande modell skapad med [Azure Machine Learning][lnk-machine-learning].

Med Azure IoT-lösningsacceleratorer kan du snabbt och enkelt ansluta till och övervaka tillgångar, samt analysera telemetri i realtid på instrumentpaneler och i visualiseringar. I lösningsacceleratorn Förutsägande underhåll ger instrumentpanelerna och visualiseringarna nya insikter som kan öka effektiviteten och förbättra intäkterna.

## <a name="the-scenario"></a>Scenariot

Fabrikam är ett regionalt flygbolag som fokuserar på bra kundupplevelser till konkurrenskraftiga priser. En orsak till flygförseningar är underhållsproblem, och flygplansmotorernas underhåll är särskilt krävande. Fabrikam måste till vilket pris som helst förhindra motorfel under flygningar och inspekterar därför regelbundet sina motorer och schemalägger underhåll med utgångspunkt i en plan. Flygplansmotorer slits dock inte alltid likadant. En del onödigt underhåll utförs på motorerna. Dessutom kan det uppstå problem som gör att planet blir stående tills underhåll har utförts. Om ett flygplan finns på en plats där rätt tekniker eller reservdelar inte är tillgängliga kan dessa problem bli extra kostsamma.

Motorerna i Fabrikams flygplan är utrustade med sensorer som övervakar motortillståndet under flygning. Fabrikam använder lösningsacceleratorn Förutsägande underhåll till att samla in de sensordata som inhämtats under flygresan. Fabrikams dataspecialister har under flera år samlat in data om motorernas drift och eventuella fel och har tagit fram en modell som kan förutse flygplansmotorernas återstående livslängd. Modellen använder en korrelation mellan data från fyra av motorsensorerna och motorförslitningar som med tiden leder till motorfel. Fabrikam fortsätter att utföra regelbundna inspektioner för att säkerställa säkerheten, men nu kan man använda modeller för att beräkna varje motors återstående livslängd efter varje flygning. Modellen använder telemetrin som samlats in från motorerna under flygresan. Fabrikam kan nu förutsäga framtida tidpunkter för potentiella fel och planera för underhåll och reparation i förväg.

> [!NOTE]
> Lösningsmodellen använder faktiska data om motorernas slitage.

Genom att förutsäga när underhåll krävs kan Fabrikam optimera verksamheten och minska kostnaderna.

Underhållskoordinatorer arbetar tillsammans med schemaläggare för att:

- Planera underhållet så att det sammanträffar med tidpunkten då ett flygplan stannar på en viss plats.
- Se till att det finns tillräckligt med tid för flygplanet att vara ur funktion utan att orsaka avbrott i schemat.
- För att schemalägga tekniker så att flygplanen servas effektivt utan väntetid.

Inventeringskontrollchefer får underhållsplaner så att de kan optimera beställningsprocessen och reservdelslagret.

Dessa aktiviteter gör att Fabrikam kan minimera flygplanens marktid och minska driftkostnaderna samtidigt som man säkerställer passagerarnas och flygplansbesättningens säkerhet.

Mer information om hur [Azure IoT-lösningsacceleratorer][lnk_iot_suite] tillhandahåller de funktioner som kunder behöver för att förverkliga potentialen i förebyggande underhåll finns [här][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-accelerator-is-built"></a>Så här är lösningsacceleratorn Förutsägande underhåll uppbyggd

Lösningen utnyttjar en befintlig Azure Machine Learning-modell som är tillgänglig som en mall för att demonstrera hur funktionerna arbetar med enhetstelemetri som samlas in via IoT-lösningsacceleratortjänster. Microsoft har byggt en [regressionsmodell][lnk_regression_model] av en flygplansmotor baserat på offentligt tillgängliga data<sup>\[1\]</sup> och stegvisa anvisningar för hur du använder modellen.

Azure IoT-lösningsacceleratorn Förutsägande underhåll använder regressionsmodellen som skapats från den här mallen. Modellen distribueras i din Azure-prenumeration och exponeras via en automatiskt genererad API. Lösningen innehåller en delmängd av testdata som representerar 4 (av totalt 100) motorer och 4 (av totalt 21) dataströmmar. Dessa data är tillräckliga för att tillhandahålla ett korrekt resultat från Trained Model.

*\[1\] A. Saxena och K. Goebel (2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="get-started-with-predictive-maintenance"></a>Kom igång med Förutsägande underhåll

I den här självstudien får etablera lösningsacceleratorn Förutsägande underhåll. Du lär dig också de grundläggande funktionerna i lösningsacceleratorn Förutsägande underhåll. Du har åtkomst till många av dessa funktioner via lösningens instrumentpanel som distribueras tillsammans med lösningsacceleratorn.

Du behöver en aktiv Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.

> [!NOTE]
> Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure][lnk_free_trial].

1. Logga in på [azureiotsuite.com][lnk-azureiotsuite] med din Azure-kontoinformation och skapa en lösning genom att klicka på **+**.
1. Klicka på **Välj** i panelen **Förutsägande underhåll**.
1. Ange ett **Lösningsnamn** för lösningsacceleratorn Förutsägande underhåll.
1. Välj den **region** och **prenumeration** som du vill använda för att etablera lösningen.
1. Klicka på **Skapa lösning** för att påbörja etableringen. Den här processen tar normalt flera minuter.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Vänta tills etableringsprocessen har slutförts

1. Klicka på ikonen för din lösning med statusen **Etablerar**.
1. Observera **etableringsstatusen** när Azure-tjänsterna distribueras i din Azure-prenumeration.
1. När etableringen har slutförts ändras statusen till **Klar**.
1. Klicka på ikonen så ser du informationen om din lösning i den högra rutan. Från det här fönstret kan du starta lösningens instrumentpanel och få åtkomst till Machine Learning-arbetsytan.

> [!NOTE]
> Om det uppstår problem när du distribuerar lösningsacceleratorn kan du läsa [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions] och [Vanliga frågor och svar][lnk-faq]. Om problemen kvarstår så skapa en tjänstbiljett på [portalen][lnk-portal].

Finns det något som du förväntar dig att se men som inte visas för din lösning? Lämna förslag på funktioner i [User Voice](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="view-the-solution"></a>Visa lösningen

I det här avsnittet vägleds du genom lösningens användargränssnitt.

### <a name="predictive-maintenance-dashboard"></a>Instrumentpanel för förebyggande underhåll

Den här sidan i webbappen använder PowerBI JavaScript-kontroller (finns i [PowerBI-visuals-databasen][lnk-powerbi]) för att visualisera:

* Utdata från Stream Analytics-jobben i Blob Storage.
* Den återstående användbara livslängden och antalet cykler per flygplansmotor.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observera molnlösningens beteende

I Azure Portal navigerar du till resursgruppen med det lösningsnamn som du har valt för att visa dina etablerade resurser.

![][img-resource-group]

När du etablerar lösningsacceleratorn får du ett e-postmeddelande med en länk till Machine Learning-arbetsytan. Du kan också navigera till Machine Learning-arbetsytan från sidan [azureiotsuite.com][lnk-azureiotsuite] för din etablerade lösning. En panel är tillgänglig på den här sidan när lösningen har statusen **Redo**.

![][img-machine-learning]

På lösningsportalen kan du se att exemplet etableras med fyra simulerade enheter som representerar två flygplan med två motorer per plan, var och en med fyra sensorer. När du navigerar till lösningsportalen stoppas simuleringen.

![][img-simulation-stopped]

Starta simuleringen genom att klicka på **Starta simulering**. Instrumentpanelen fylls med sensorhistorik, RUL-värden, cykler och RUL-historik.

![][img-simulation-running]

Om RUL-värdet är mindre än 160 (ett godtyckligt tröskelvärde som valts som exempel) visas en varningssymbol på lösningsportalen bredvid RUL-värdet. Dessutom markeras flygplansmotorn i gult på lösningsportalen. Lägg märke till att RUL-värdena har en nedåtgående trend generellt, men med många upp- och nedgångar. Detta mönster beror på de olika cykellängderna och modellens precision.

![][img-simulation-warning]

Den fullständiga simuleringen tar 35 minuter för att slutföra 148 cykler. RUL-tröskelvärdet på 160 nås för första gången vid cirka 5 minuter och båda motorerna når tröskelvärdet ungefär vid 8 minuter.

Simuleringen kör igenom den fullständiga datauppsättningen i 148 cykler och bestämmer den slutliga återstående användbara livslängden och cykelvärdena.

Du kan stoppa simuleringen när du vill, men om du klickar på **Starta simulering** spelas simuleringen upp igen från början av datauppsättningen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur Azure IoT möjliggör scenarier för förebyggande underhåll finns i artikeln [Få ut värde av Internet of Things][lnk_capture_value].

Ta en [genomgång][lnk-predictive-walkthrough] av lösningsacceleratorn Förutsägande underhåll.

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Vanliga frågor och svar om IoT-lösningsacceleratorer][lnk-faq]
* [IoT-säkerhet från grunden][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]:../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md
[lnk_iot_suite]: iot-suite-options.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/