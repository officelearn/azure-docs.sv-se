---
title: Defender för IoT vanliga frågor och svar
description: Hitta svar på vanliga frågor om Azure Defender för IoT-funktioner och-tjänster.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 97dd4e13754175e9c81ae308b86faae811e4d554
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941801"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Defender för IoT

Den här artikeln innehåller en lista över vanliga frågor och svar om Defender för IoT.

## <a name="does-azure-provide-support-for-iot-security"></a>Tillhandahåller Azure Support för IoT-säkerhet?

Azure tillhandahåller en integrerad vy för övervakning och hantering av din IoT-säkerhet som en del av din övergripande säkerhetslösning via Azure Security Center. Om du är programutvecklare kan du använda IoT Hub vy för att hantera IoT-programmets säkerhet.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Vad är det unika värdet i Azure för IoT-säkerhet?

Defender för IoT gör det möjligt för företag att utöka sin befintliga cyberhot-säkerhetsvy till hela IoT-lösningen. Azure ger en heltäckande vy över din affärs lösning, så att du kan vidta affärsrelaterade åtgärder och beslut baserat på företagets säkerhets position och insamlade data. Med kombinerad säkerhet med Azure IoT, Azure IoT Edge och Azure Security Center kan du skapa den lösning som du vill ha med den säkerhet du behöver.

## <a name="who-is-defender-for-iot-made-for"></a>Vem är Defender för IoT skapat?

Defender for IoT är integrerat i Azure IoT Hub Security och ger hantering för dagliga säkerhets åtgärder för affärs lösningar. Defender för IoT är också integrerat i Azure Security Center funktioner och ger en integrerad vy för övervakning och hantering av din IoT-säkerhet som en del av din övergripande säkerhetslösning.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Hur jämförs Defender för IoT med tävlingen?

Även om andra lösningar tillhandahåller en uppsättning funktioner som gör att kunderna kan skapa sina egna lösningar, ger Defender för IoT en unik lösning från slut punkt till slut punkt som ger en bred vy över säkerheten för alla dina relaterade Azure-resurser. Azure möjliggör snabb distribution och fullständig integrering med IoT Hub modul för enkel integrering med befintliga enhets hanterings verktyg.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>Måste jag vara Azure Security Center kund för att kunna använda den här tjänsten?

Nej, men det rekommenderas. Utan Azure Security Center, kan Defender för IoT ta emot begränsade anslutna resurs data och ger en begränsad analys av den potentiella angrepps ytan, hot och potentiella attacker.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Måste jag vara en Azure IoT-kund?

Ja. Defender för IoT förlitar sig på Azure IoT-anslutning och-infrastruktur.

## <a name="do-i-have-to-install-an-agent"></a>Måste jag installera en agent?

Agent installationen på dina IoT-enheter är inte obligatorisk för att aktivera Defender för IoT. Du kan välja mellan följande tre alternativ för att få olika nivåer av säkerhetsövervakning och hanterings funktioner enligt ditt val:

1. Installera Defender for IoT-Säkerhetsagenten med eller utan ändringar. Det här alternativet ger den högsta nivån av förbättrade säkerhets insikter om enhetens beteende och åtkomst.

1. Skapa en egen agent och implementera schemat för säkerhets meddelanden i Defender for IoT. Med det här alternativet aktive ras användning av Defender för IoT Analysis tools ovanpå enhetens säkerhets agent.

1. Ingen installation av säkerhets agent på IoT-enheter. Med det här alternativet kan IoT Hub kommunikation övervakas med minskad säkerhetsövervakning och hanterings funktioner.

## <a name="what-does-the-defender-for-iot-agent-do"></a>Vad gör Defender för IoT-agenten?

Defender för IoT-agenten ger hot täckning på enhets nivå för enhets konfiguration, beteende och åtkomst (genom att söka i konfigurationen), bearbeta & anslutning. Defender for IoT-Säkerhetsagenten söker inte efter affärsrelaterade data eller aktiviteter.

## <a name="where-can-i-get-the-defender-for-iot-security-agent"></a>Var kan jag hämta säkerhets agenten Defender för IoT?

Defender for IoT-Säkerhetsagenten är öppen källkod och tillgänglig på GitHub i 32-bitars och 64-bitars Windows-och Linux-versioner: https://github.com/Azure/Azure-IoT-Security .

## <a name="where-does-the-defender-for-iot-agent-get-installed"></a>Var installeras Defender för IoT-agenten?

Detaljerad information om installation och agent distribution finns i GitHub: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Vilka är beroendena och kraven för agenten?

Defender for IoT stöder en mängd olika plattformar. Se [vilka enhets plattformar som stöds](how-to-deploy-agent.md) för att verifiera stöd för dina enheter.

## <a name="which-data-is-collected-by-the-agent"></a>Vilka data samlas in av agenten?

Anslutning, åtkomst, brand Väggs konfiguration, process lista & operativ systemets bas linje samlas in av agenten.

## <a name="how-much-data-will-the-agent-generate"></a>Hur mycket data kommer agenten att generera?

Genereringen av agent data drivs av enhets-, program-, Anslutnings typ och konfigurationen av kund agenten. På grund av den höga variationen mellan enheter och IoT-lösningar rekommenderar vi att du först distribuerar agenten i en labb-eller test inställning för att se, lära dig och ange den speciella konfiguration som passar dina behov, samtidigt som du mäter mängden genererade data. När du har startat tjänsten ger Defender for IoT-agenten operativa rekommendationer för att optimera agent-genomflödet för att hjälpa dig med konfigurations-och anpassnings processen.

## <a name="how-can-i-control-my-billing"></a>Hur kan jag kontrol lera faktureringen?

Defender för IoT tillhandahåller konfigurerbara agent genomsökningar, databuffertar och möjligheten att skapa anpassade aviseringar som ökar eller minskar mängden data som genereras av agenten.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Använder agent meddelanden kvoten från IoT Hub?

Ja. Agenter som överför data räknas i IoT Hub kvoten.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Vad är nästa steg? Jag har installerat en agent och ser inga aktiviteter eller loggar...

1. Kontrol lera vilken [agent typ som passar din enhets operativ system plattform](how-to-deploy-agent.md)

1. Bekräfta att [agenten körs på enheten](how-to-agent-configuration.md).

1. Kontrol lera att [tjänsten har Aktiver ATS](quickstart-onboard-iot-hub.md) för **säkerhet** i din IoT Hub.

1. Kontrol lera att enheten är [konfigurerad i IoT Hub med Defender för IoT-modulen](quickstart-create-security-twin.md).

Om aktiviteterna eller loggarna fortfarande inte är tillgängliga kontaktar du din Defender for IoT-partner för ytterligare hjälp.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Vad händer när Internet anslutningen slutar fungera?

Agenten fortsätter att köra och lagra data så länge enheten körs. Data lagras i cacheminnet för säkerhets meddelanden enligt storleks konfigurationen. När enheten återfår anslutningen återupptas säkerhets meddelanden som skickas.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Kommer säkerhets agenten att återställa själv om enheten startas om.

Säkerhets agenten är utformad för att köras igen automatiskt med varje omstart av enheten.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Kan agenten påverka enhetens prestanda eller annan installerad program vara?

Agenten använder dator resurser som alla andra program/processer och ska inte störa normal enhets aktivitet. Resursförbrukning på den enhet som agenten körs på är kopplad till konfigurationen och konfigurationen. Vi rekommenderar att du testar din agent konfiguration i en innesluten miljö, tillsammans med samverkan med dina andra IoT-program och-funktioner, innan du försöker distribuera i en produktions miljö.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Jag gör vissa underhåll på enheten. Kan jag stänga av agenten?

Agenten kan inte stängas av.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Finns det något sätt att testa om agenten fungerar korrekt?

Om agenten slutar kommunicera eller Miss lyckas med att skicka säkerhets meddelanden genereras en varning om att en **enhet är tyst** .

## <a name="can-i-create-my-own-alerts"></a>Kan jag skapa mina egna aviseringar?

Ja. Du kan ange en anpassad avisering på en fördefinierad uppsättning beteenden, till exempel IP-adress och öppna portar. Se [skapa anpassade aviseringar](quickstart-create-custom-alerts.md) för att lära dig mer om anpassade aviseringar och hur du gör dem.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Var kan jag se loggar? Kan jag anpassa loggar?

- Visa aviseringar och rekommendationer med hjälp av den anslutna Log Analytics-arbetsytan. Konfigurera lagrings storlek och varaktighet i arbets ytan.

- Rå data från din säkerhets agent kan också lagras i ditt Log Analytics-konto. Överväg storlek, varaktighet, lagrings krav och tillhör ande kostnader innan du ändrar konfigurationen för det här alternativet.

## <a name="why-should-i-add-defender-for-iot-to-the-module-identity-what-is-it-used-for"></a>Varför ska jag lägga till Defender för IoT till modulens identitet? Vad används den för?

Defender för IoT-modulen används för agent konfiguration och hantering.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer igång med Defender för IoT finns i följande artiklar:

- Läs [översikten över](overview.md) Defender för IoT
- Verifiera [tjänst kraven](service-prerequisites.md)
- Lär dig mer om hur du [kommer igång](getting-started.md)
- Förstå [Defender för IoT-säkerhetsaviseringar](concept-security-alerts.md)
