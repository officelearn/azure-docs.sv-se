---
title: Vanliga frågor och svar för Azure Security Center för IoT| Microsoft-dokument
description: Hitta svar på de vanligaste frågorna om Azure Security Center för IoT-funktioner och -tjänster.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 639a3f89e470a832279add8d2ed7cf49441611f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571787"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Security Center for IoT  

Den här artikeln innehåller en lista över vanliga frågor och svar om Azure Security Center för IoT. 

## <a name="does-azure-provide-support-for-iot-security"></a>Ger Azure stöd för IoT-säkerhet?

Azure tillhandahåller en integrerad vy för övervakning och hantering av din IoT-säkerhet som en del av din övergripande säkerhetslösning via Azure Security Center. Om du är programutvecklare kan du använda IoT Hub-vyn för att hantera din IoT-programsäkerhet.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Vad är Azures unika värdeerbjudande för IoT-säkerhet?

Azure Security Center för IoT gör det möjligt för företag att utöka sin befintliga cybersäkerhetsvy till hela sin IoT-lösning. Azure ger en heltäckande vy över din affärslösning, så att du kan vidta affärsrelaterade åtgärder och beslut baserat på företagets säkerhetsposition och insamlade data. Kombinerad säkerhet med Azure IoT, Azure IoT Edge och Azure Security Center gör att du kan skapa den lösning du vill ha med den säkerhet du behöver.

## <a name="who-is-azure-security-center-for-iot-made-for"></a>Vem är Azure Security Center för IoT gjord för? 

Azure Security Center för IoT är integrerat i Azure IoT Hub Security och tillhandahåller hantering för dagliga säkerhetsåtgärder för affärslösningar. Azure Security Center för IoT är också integrerat i Azure Security Center-funktioner och ger en integrerad vy för övervakning och hantering av din IoT-säkerhet som en del av din övergripande säkerhetslösning.

## <a name="how-does-azure-security-center-for-iot-compare-to-the-competition"></a>Hur jämför Azure Security Center för IoT konkurrenterna?

Andra lösningar ger en uppsättning funktioner som gör det möjligt för kunder att skapa sina egna lösningar, men Azure Security Center för IoT tillhandahåller en unik heltäckande IoT-säkerhetslösning som ger en bred vy över säkerheten för alla dina relaterade Azure Resurser. Azure möjliggör snabb distribution och fullständig integrering med IoT Hub-modultvillingar för enkel integrering med befintliga verktyg för enhetshantering.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>Måste jag vara kund i Azure Security Center för att kunna använda den här tjänsten?

Nej, men det rekommenderas. Utan Azure Security Center tar Azure Security Center för IoT emot begränsade anslutna resursdata och ger en begränsad analys av din potentiella angreppsyta, hot och potentiella attacker. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Måste jag vara en Azure IoT-kund?

Ja. Azure Security Center för IoT förlitar sig på Azure IoT-anslutning och infrastruktur.

## <a name="do-i-have-to-install-an-agent"></a>Måste jag installera en agent?

Agentinstallation på dina IoT-enheter är inte obligatorisk för att aktivera Microsoft Azure Security Center för IoT. Du kan välja mellan följande tre alternativ och få olika nivåer av säkerhetsövervakning och hanteringsfunktioner enligt ditt val:

1. Installera Azure Security Center för IoT-säkerhetsagent med eller utan ändringar. Det här alternativet ger den högsta nivån av förbättrade säkerhetsinsikter i enhetens beteende och åtkomst.

2. Skapa din egen agent och implementera Microsoft Azure Security Center för IoT-säkerhetsmeddelandeschema. Det här alternativet aktiverar användning av Microsoft Azure Security Center för IoT-analysverktyg ovanpå enhetens säkerhetsagent.

3. Ingen installation av säkerhetsagenter på dina IoT-enheter. Det här alternativet möjliggör kommunikationsövervakning av IoT Hub med nedsatt säkerhetsövervakning och hanteringsfunktioner. 

## <a name="what-does-the-azure-security-center-for-iot-agent-do"></a>Vad gör Azure Security Center för IoT-agent?

Azure Security Center för IoT-agent ger hottäckning på enhetsnivå för enhetskonfiguration, beteende och åtkomst (genom att skanna konfigurationen), bearbeta & anslutning. Azure Security Center för IoT-säkerhetsagent söker inte igenom affärsrelaterade data eller aktiviteter.

## <a name="where-can-i-get-the-azure-security-center-for-iot-security-agent"></a>Var kan jag få Azure Security Center för IoT-säkerhetsagent?

Azure Security Center för IoT-säkerhetsagent är öppen källkod och tillgänglig på GitHub i 32-bitars och 64-bitars Windows- och Linux-versioner: https://github.com/Azure/Azure-Security-IoT.

## <a name="where-does-the-azure-security-center-for-iot-agent-get-installed"></a>Var installeras Azure Security Center för IoT-agent? 

Detaljerad information om installation och agentdistribution finns https://github.com/Azure/Azure-IoT-Securityi GitHub: .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Vilka är agentens beroenden och förutsättningar?

Azure Security Center för IoT stöder en mängd olika plattformar. Se [Plattformar som stöds](how-to-deploy-agent.md) för att verifiera stöd för dina specifika enheter. 

## <a name="which-data-is-collected-by-the-agent"></a>Vilka uppgifter samlas in av agenten?

Anslutning, åtkomst, brandväggskonfiguration, processlista & OS-baslinjen samlas in av agenten.

## <a name="how-much-data-will-the-agent-generate"></a>Hur mycket data genererar agenten?

Agentdatagenerering drivs av enhets-, program-, anslutningstyp och kundagentkonfiguration. På grund av den höga variabiliteten mellan enheter och IoT-lösningar rekommenderar vi att du först distribuerar agenten i ett labb eller en testinställning för att observera, lära sig och ställa in den specifika konfiguration som passar dina behov, samtidigt som mängden genererade data mäts. När tjänsten har startats tillhandahåller Azure Security Center för IoT-agenten driftsrekommendationer för att optimera agentdataflödet för att hjälpa dig med konfigurations- och anpassningsprocessen.

## <a name="how-can-i-control-my-billing"></a>Hur kan jag kontrollera min fakturering?

Azure Security Center för IoT tillhandahåller konfigurerbara agentsökningar, databuffertar och möjligheten att skapa anpassade aviseringar som ökar eller minskar mängden data som genereras av agenten.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Använder agentmeddelanden kvot från IoT Hub?

Ja. Överförda agentdata räknas i din IoT Hub-kvot. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Vad händer nu? Jag har installerat en agent och ser inga aktiviteter eller loggar...

1. Kontrollera [agenttypen som passar enhetens utsedda OS-plattform](how-to-deploy-agent.md)

1. Bekräfta [att agenten körs på enheten](how-to-agent-configuration.md).

2. Kontrollera att [tjänsten har aktiverats](quickstart-onboard-iot-hub.md) för **säkerhet** i din IoT Hub. 

3. Kontrollera att enheten är [konfigurerad i IoT Hub med Azure Security Center för IoT-modulen](quickstart-create-security-twin.md).  

Om aktiviteterna eller loggarna fortfarande inte är tillgängliga kontaktar du din Azure Security Center för IoT-partner för ytterligare hjälp.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Vad händer när internetuppkopplingen slutar fungera?

Agenten fortsätter att köra och lagra data så länge enheten körs. Data lagras i säkerhetsmeddelandecachen enligt storlekskonfigurationen. När enheten återfår anslutningen återupptas sändningen av säkerhetsmeddelanden. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Om enheten startas om, kommer säkerhetsagenten självåterfunnit sig?

Säkerhetsagenten är utformad för att köras automatiskt när varje enhet startas om.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Kan agenten påverka enhetens prestanda eller annan installerad programvara?

Agenten förbrukar maskinresurser som alla andra program/processer och bör inte störa normal enhetsaktivitet. Resursförbrukning på enheten agenten körs på är kopplad till dess inställningar och konfiguration. Vi rekommenderar att du testar agentkonfigurationen i en innesluten miljö, tillsammans med interoperabilitet med dina andra IoT-program och funktioner, innan du försöker distribuera i en produktionsmiljö.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Jag gör lite underhåll på enheten. Kan jag stänga av agenten?

Agenten kan inte stängas av.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Finns det något sätt att testa om agenten fungerar som det ska? 

Om agenten slutar kommunicera eller misslyckas med att skicka säkerhetsmeddelanden genereras en **enhets tysta** avisering.

## <a name="can-i-create-my-own-alerts"></a>Kan jag skapa mina egna aviseringar?

Ja. Du kan ange en anpassad avisering på förutbestämda uppsättning beteenden som IP-adress och öppna portar. Se [Skapa anpassade aviseringar](quickstart-create-custom-alerts.md) om du vill veta mer om anpassade aviseringar och hur du skapar dem. 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Var kan jag se loggar? Kan jag anpassa loggar?

- Visa aviseringar och rekommendationer med hjälp av din anslutna Log Analytics-arbetsyta. Konfigurera lagringsstorlek och varaktighet på arbetsytan.

- Rådata från din säkerhetsagent kan också lagras i ditt Log Analytics-konto. Överväg storlek, varaktighet, lagringskrav och tillhörande kostnader innan du ändrar konfigurationen av det här alternativet. 

## <a name="why-should-i-add-azure-security-center-for-iot-to-the-module-identity-what-is-it-used-for"></a>Varför ska jag lägga till Azure Security Center för IoT i modulens identitet? Vad används det för?

Azure Security Center för IoT-modulen används för agentkonfiguration och hantering.


## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer igång med Azure Security Center för IoT finns i följande artiklar:


- Läs [översikten](overview.md) över Azure Security Center for IoT
- Verifiera [serviceförutsättningarna](service-prerequisites.md)
- Läs mer om hur [du kommer igång](getting-started.md)
- Förstå [Azure Security Center för IoT-säkerhetsaviseringar](concept-security-alerts.md)

