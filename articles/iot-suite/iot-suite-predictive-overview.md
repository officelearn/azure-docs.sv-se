<properties
 pageTitle="Förkonfigurerad lösning för förebyggande underhåll | Microsoft Azure"
 description="En beskrivning av den förkonfigurerade lösningen för förebyggande underhåll i Azure IoT."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>


# Översikt över förkonfigurerad lösning för förebyggande underhåll

Den förkonfigurerade lösningen för *förebyggande underhåll* är en av de [förkonfigurerade lösningarna][lnk_preconfigured_solutions] som ingår i [Microsoft Azure IoT Suite][lnk_iot_suite]. Den här lösningen integrerar realtidsinsamling av enhetstelemetri med en förebyggande modell skapad med [Azure Machine Learning][lnk_machine_learning].


Med Azure IoT Suite kan företag snabbt och enkelt ansluta till och övervaka tillgångar och analysera data i realtid. Den förkonfigurerade lösningen för förebyggande underhåll bearbetar dessa data och använder detaljerade instrumentpaneler och visualiseringar för att ge företagen nya insikter som kan öka effektiviteten och förbättra intäkterna.

## Scenariot

Fabrikam är ett regionalt flygbolag som fokuserar på bra kundupplevelser till konkurrenskraftiga priser. En orsak till flygförseningar är underhållsproblem, och flygplansmotorernas underhåll är särskilt krävande. Motorfel under flygning måste undvikas till varje pris. Därför inspekterar Fabrikam regelbundet sina motorer och följer en schemalagd underhållsplan. Flygplansmotorer slits dock inte alltid likadant. En del onödigt underhåll utförs på motorerna. Dessutom kan det uppstå problem som gör att planet blir stående tills underhåll har utförts. Detta medför kostsamma fördröjningar, särskilt om ett flygplan finns på en plats där rätt tekniker eller reservdelar inte är tillgängliga.

Motorerna i Fabrikams flygplan är utrustade med sensorer som övervakar motortillståndet under flygning. Fabrikam använder Azure IoT Suite för att samla in de sensordata som inhämtas under flygturerna. Fabrikams dataspecialister har under flera år samlat in data om motorernas drift och eventuella fel och har tagit fram en modell som kan förutse flygplansmotorernas återstående livslängd. Vad man har identifierat är en korrelation mellan data från fyra av motorsensorerna med motorernas slitage som leder till eventuella problem. Fabrikam fortsätter att utföra regelbundna inspektioner för att säkerställa säkerheten, men nu kan man använda modeller för att beräkna varje motors återstående livslängd efter varje flygning med hjälp av telemetrin som samlas in från motorerna under flygningen. Fabrikam kan nu förutsäga framtida tidpunkter för potentiella fel och planera för underhåll och reparation i förväg.

> [AZURE.NOTE] Lösningsmodellen använder faktiska data om motorernas slitage.

Genom att förutsäga när underhåll krävs kan Fabrikam optimera verksamheten och minska kostnaderna. Underhållssamordnare arbetar med schemaläggare för att planera in underhållet då flygplanet befinner sig på en särskild plats och ser till att tillräckligt med tid avsetts för underhållet utan att orsaka förseningar i schemat. Fabrikam kan schemalägga sina tekniker på motsvarande sätt så att flygplanen servas effektivt utan väntetid. Inventeringskontrollchefer får underhållsplaner så att de kan optimera beställningsprocessen och reservdelslagret. Detta gör att Fabrikam kan minimera flygplanens marktid och minska driftkostnaderna samtidigt som man säkerställer passagerarnas och flygplansbesättningens säkerhet.

Mer information om hur [Azure IoT Suite][lnk_iot_suite] tillhandahåller de funktioner som kunder behöver för att förverkliga potentialen i förebyggande underhåll finns [här][lnk_infographic].

## Så här är lösningen för förebyggande underhåll uppbyggd

Lösningen utnyttjar en befintlig Azure Machine Learning-modell som är tillgänglig som en mall för att demonstrera hur dessa funktioner arbetar med enhetstelemetri som samlas in via IoT Suite-tjänster. Microsoft har byggt en [regressionsmodell][lnk_regression_model] av en flygplansmotor och publicerat den fullständiga mallen, data<sup>\[1\]</sup> och stegvisa anvisningar för hur du använder modellen.

Den förkonfigurerade lösningen för förebyggande underhåll i Azure IoT använder regressionsmodellen som skapats från den här mallen. Den distribueras till din Azure-prenumeration och exponeras via ett automatiskt genererat API. Lösningen innehåller en delmängd av testdata som representerar 4 (av totalt 100) motorer och 4 (av totalt 21) dataströmmar från sensorer som ger ett korrekt resultat från den tränade modellen.

*\[1\] A. Saxena och K. Goebel (2008). ”Turbofan Engine Degradation Simulation Data Set”, NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## Nästa steg

Mer information om hur Azure IoT möjliggör scenarier för förebyggande underhåll finns i artikeln [Få ut värde av Internet of Things][lnk_capture_value].

Gå en [genomgång][lnk-predictive-walkthrough] om den förkonfigurerade lösningen för förebyggande underhåll.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

Du kan även utforska några andra funktioner och möjligheter i de förkonfigurerade lösningarna i IoT Suite:

- [Vanliga frågor och svar om IoT Suite][lnk-faq]
- [IoT säkerhet från grunden][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md



<!--HONumber=Sep16_HO3-->


