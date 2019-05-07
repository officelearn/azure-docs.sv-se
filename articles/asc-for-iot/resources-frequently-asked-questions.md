---
title: Vanliga frågor om Azure Security Center för förhandsversionen av IoT | Microsoft Docs
description: Hitta svar på vanliga frågor om Azure Security Center för IoT-funktioner och -tjänsten.
services: asc-for-iot
ms.service: ASCforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: f15cd14ddc3a489db3abbbd2665d242470b5a821
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200711"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Vanliga och frågor svar om Azure Security Center för IoT  

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln innehåller en lista över vanliga frågor och svar om Azure Security Center (ASC) för IoT. 

## <a name="does-azure-provide-support-for-iot-security"></a>Tillhandahåller Azure stöd för IoT-säkerhet?

Azure ger en integrerad vy för att övervaka och hantera dina IoT-säkerhet som en del av din övergripande säkerhetslösning via Azure Security Center. Om du är programutvecklare kan använda du IoT Hub-vyn för att hantera din IoT-programsäkerhet.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Vad är Azures unika förslagsvärde för IoT-säkerhet?

ASC för IoT kan företag utöka sina befintliga cyberhot säkerhetsvy till hela sin IoT-lösning. Azure ger en heltäckande vy över din företagslösning, så att du kan vidta åtgärder för affärsrelaterade och beslut baserat på din enterprise-säkerhetsposition och insamlade data. Kombineras security med Azure IoT, Azure IoT Edge, Azure Sphere, Azure Central och Azure Security Center kan du skapa den lösning som du vill med säkerheten du behöver.

## <a name="who-is-asc-for-iot-made-for"></a>Vem är ASC för IoT som gjorts? 

ASC för IoT är integrerad i Azure IoT Hub Security och tillhandahåller för dagliga affärslösning säkerhetsåtgärder. ASC för IoT är också integrerat i Azure Security Center-funktioner och ger en integrerad vy för att övervaka och hantera dina IoT-säkerhet som en del av din övergripande säkerhetslösning.

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>Hur ASC för IoT jämfört med tävlingen?

Andra lösningar tillhandahåller en uppsättning funktioner som gör att kunderna kan skapa egna lösningar, tillhandahåller ASC för IoT en unik slutpunkt till slutpunkt IoT säkerhetslösning som ger en bred vy över säkerheten hos alla dina tillhörande Azure-resurser. Azure gör det möjligt för snabb distribution och fullständig integrering med IoT Hub modultvillingar för enkel integrering med befintliga verktyg för enheten.

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>Måste jag vara en Azure Security Center (ASC)-kund?

Nej, men det rekommenderas. Utan ASC, ASC för IoT får begränsad anslutna resursdata och ger en begränsad analys av potentiella attacker, hot och attacker. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Måste jag vara en Azure IoT-kund?

Ja. ASC för IoT förlitar sig på Azure IoT-anslutning och infrastruktur.

## <a name="do-i-have-to-install-an-agent"></a>Måste jag installera en agent?

Agentinstallationen på dina IoT-enheter är inte obligatoriska för att aktivera Microsoft ASC för IoT. Du kan välja mellan följande tre alternativ och få olika säkerhetsnivåer övervaknings- och hanteringsfunktionerna enligt dina val:

1. Installera ASC för IoT security-agenten med eller utan ändringar. Det här alternativet ger den högsta nivån av säkerhetsskäl insikter om enhetsbeteende och åtkomst.

2. Skapa din egen agent och implementera Microsoft ASC för IoT security meddelande schemat. Det här alternativet kan användningen av Microsoft ASC för IoT-analysverktyg på din enhet security-agenten.

3. Inga säkerhets-agentinstallationen på dina IoT-enheter. Det här alternativet aktiverar IoT Hub kommunikation övervakning med funktioner för övervakning och hantering av minskad säkerhet. 

## <a name="what-does-the-asc-for-iot-agent-do"></a>Vad gör ASC för IoT-agenten?

ASC för IoT-agenten tillhandahåller enheten på threat täckning för enhetskonfiguration, beteende, och åtkomst (genom att skanna konfigurationen), process och anslutning. ASC för IoT security-agenten igenom inte företagsrelaterade data eller aktivitet.

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>Var kan jag få ASC för IoT security-agenten?

ASC för IoT security-agenten är öppen källkod och finns på GitHub i 32-bitars och 64-bitars Windows och Linux-versioner: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>Där ASC för IoT-agenten installeras? 

Detaljerad information för distribution av installation och agenten kan hittas i GitHub: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Vilka är de beroenden och krav för agenten?

ASC för IoT stöder en mängd olika plattformar. Se [stöds enhetsplattformar](how-to-deploy-agent.md) verifiering av stöd för specifika enheter. 

## <a name="which-data-is-collected-by-the-agent"></a>Vilka data som samlas in av agenten?

Anslutning, åtkomst, brandväggskonfiguration, processlistan och grundregler för operativsystem samlas in av agenten.

## <a name="how-much-data-will-the-agent-generate"></a>Hur mycket data genereras agenten?

Agenten data generation drivs av enhet, program, anslutningstypen och konfiguration av customer agent. På grund av hög ombytlighet mellan enheter och IoT-lösningar rekommenderar vi första distribuera agenten i en testlabbet eller test att notera, Läs och ange specifik konfiguration som passar dina behov, mätning av mängden data som genereras. När du har startat tjänsten innehåller ASC för IoT-agenten operativa rekommendationer för att optimera agenten dataflöde som hjälper dig med processen konfiguration och anpassning.

## <a name="how-can-i-control-my-billing"></a>Hur kan jag kontrollera Mina kostnader?

ASC för IoT innehåller konfigurerbara agenten genomsökningar databuffertarna och möjligheten att skapa anpassade varningar som kan öka eller minska mängden data som genereras av agenten.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Använder agenten meddelanden upp kvot från IoT Hub?

Ja. Agenten överförs data räknas i din IoT Hub-kvot. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Vad är nästa steg? Jag har installerat en agent och ser inte några aktiviteter eller loggar

1. Kontrollera den [agenttypen passar avsedda OS-plattform för din enhet](how-to-deploy-agent.md)

1. Bekräfta de [agenten körs på enheten](how-to-agent-configuration.md).

2. Kontrollera den [tjänsten har aktiverats](quickstart-onboard-iot-hub.md) till **Security** i IoT Hub. 

3. Kontrollera att enheten är [konfigurerats i IoT Hub med ASC för IoT-modulen](quickstart-create-security-twin.md).  

Kontakta din ASC om aktiviteter eller loggar fortfarande tillgänglig för IoT-partner för ytterligare hjälp.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Vad händer när Internetanslutningen slutar fungera?

Agenten fortsätter att köra och lagra data så länge enheten kör. Data lagras i cacheminnet för statusmeddelanden i security enligt storlekskonfiguration. När enheten har uppnått anslutning, återuppta säkerhetsvarningar skicka. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Om enheten startas om, kommer säkerhetsagenten själv återställa?

Säkerhetsagenten har utformats för att köra automatiskt med varje omstart av enheten.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Agenten kan påverka prestanda för enheten eller andra installerade program?

Agenten förbrukar datorresurser som alla andra program/process och bör inte störa normal enhetsaktivitetsloggar. Resursförbrukning på enheten som agenten körs används tillsammans med dess installationen och konfigurationen. Vi rekommenderar att du testar din agentkonfiguration i en innesluten miljö, tillsammans med samverkan med andra IoT-program och funktioner, innan du distribuerar i en produktionsmiljö.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Jag gör vissa Underhåll på enheten. Kan jag inaktivera agenten?

Agenten kan inte stängas av.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Finns det ett sätt att testa om agenten fungerar korrekt? 

Om agenten slutar kommunicera eller misslyckas med att skicka meddelanden för säkerhet, en **enheten är tyst** avisering.

## <a name="can-i-create-my-own-alerts"></a>Kan jag skapa min egen aviseringar?

Ja. Du kan ange en anpassad avisering på förutbestämd uppsättning beteenden, till exempel IP-adress och öppna portar. Se [skapa anpassade varningar](quickstart-create-custom-alerts.md) mer information om anpassade varningar och hur du blir. 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Var kan jag se loggarna? Kan jag Anpassa loggarna?

- Visa aviseringar och rekommendationer med hjälp av anslutna Log Analytics-arbetsytan. Konfigurera lagringsstorlek och varaktighet i arbetsytan.

- Rådata från security-agenten kan också lagras i Log Analytics-konto. Överväg att storlek, varaktighet, krav och relaterade kostnader innan du ändrar konfigurationen av det här alternativet. 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>Varför bör jag lägga till ASC för IoT modulen identitet? Vad används den för?

ASC för IoT-modulen används för agentkonfiguration och hantering.


## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du kommer igång med ASC för IoT, finns i följande artiklar:


- Läs ASC för IoT [översikt](overview.md)
- Kontrollera den [tjänsten krav](service-prerequisites.md)
- Mer information om hur du [komma igång](getting-started.md)
- Förstå [ASC för IoT-säkerhetsvarningar](concept-security-alerts.md)

