---
title: "Översikt över Azure IoT-hubb enheten etableras | Microsoft Docs"
description: "Beskriver enhetsetableringen i Azure med tjänsten för etablering av enheten och IoT-hubb"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 12/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 021ff1299321ae1aece3a77fc61129517c85697b
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service-preview"></a>Etablering enheter med Azure IoT-hubb Device etablering Service (förhandsgranskning)
Microsoft Azure tillhandahåller en omfattande uppsättning integrerad offentliga molntjänster för alla dina IoT-lösningen behov. IoT-hubb Device etablering Service är en helper-tjänsten för IoT-hubb som möjliggör zero touch, just-in-time etablering till rätt IoT-hubben utan mänsklig inblandning aktiverar kunder att etablera miljoner enheter i en säker och skalbar sätt.

## <a name="when-to-use-device-provisioning-service"></a>När du ska använda etablering av tjänst
Det finns många etablering scenarier där tjänsten enhet etablering är en utmärkt val för hämtning enheter anslutna och konfigurerade för IoT-hubb som:

* Zero touch etablering till en enda IoT-lösning utan hardcoding IoT-hubb anslutningsinformationen på fabriken (installationen)
* Enheter för belastningsutjämning mellan flera nav
* Ansluta enheter till sina ägare IoT-lösning baserad på försäljning transaktionsdata (multitenancy)
* Enheter som ansluter till en viss IoT-lösning beroende på användningsfall (lösning isolering)
* Ansluter en enhet till IoT-hubb med den lägsta fördröjningen (geo-delning)
* Nytt etablering baserat på en ändring i enheten
* Rullande nycklar som används av enheten för att ansluta till IoT-hubb (när du inte använder X.509-certifikat för att ansluta)

## <a name="behind-the-scenes"></a>I bakgrunden
Alla scenarier som beskrivs i föregående avsnitt kan göras med hjälp av tjänsten etablering för zero touch-etablering med samma flöde. Många av de manuella stegen som vanligtvis ingår i etablering automatiserad enhet etablering tjänsten för att minska tiden för att distribuera IoT-enheter och risken för manuella fel. Följande är en beskrivning av vad som händer i bakgrunden och få en enhet som etablerats. Det första steget är manuell, alla följande steg utförs automatiskt.

![Grundläggande flöde för etablering](./media/about-iot-dps/dps-provisioning-flow.png)

1. Enhetstillverkare lägger till registreringsinformation enheten i listan registrering i Azure-portalen.
2. Enheten kontaktar etablering tjänstslutpunkten på fabriken. Enheten skickar tjänsten etablering dess identifieringsinformation för att bevisa sin identitet.
3. Etablering tjänsten bekräftar identiteten för enheten genom att verifiera registrerings-ID och nyckel mot registrering listpost med ett temporärt ID challenge ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) eller standard X.509 verifiering ( X.509).
4. Tjänsten etablering registrerar enheten med en IoT-hubb och fylls enhetens [önskad dubbla tillstånd](../iot-hub/iot-hub-devguide-device-twins.md).
5. IoT-hubben returnerar ID-information för enheten och tjänsten etablering.
6. Tjänsten etablering returnerar anslutningsinformationen för IoT-hubb till enheten. Enheten kan nu börja skicka data direkt till IoT-hubben.
7. Enheten ansluter till IoT-hubb.
8. Enheten får tillståndet från sin enhet dubbla i IoT-hubb.

## <a name="provisioning-process"></a>Etablering pågår
Det finns två olika steg i processen för distribution av en enhet där enheten Etableringstjänsten tar en del som kan göras oberoende:

* Den **tillverkning steg** som enheten skapas och förberedas på fabriken, och
* Den **moln steget av installationen** i som enheten Etableringstjänsten är konfigurerad för automatisk etablering.

Båda dessa steg få plats i sömlöst med befintliga processer för tillverkning och distribution. Enheten Etableringstjänsten förenklar även vissa distributionsprocesser som omfattar en mängd manuellt arbete att hämta anslutningsinformation om på enheten.

### <a name="manufacturing-step"></a>Tillverkning steg
Det här steget är alla om vad som händer på raden tillverkning. Rollerna som ingår i det här steget inkluderar silicon designer, silicon tillverkare, integrator och end tillverkaren av enheten. Det här steget är bekymrad maskinvaruinventeringsfil sig själv.

Etablering av tjänst inför inte ett nytt steg i tillverkningen. i stället kopplar samman i det befintliga steg som installerar den första programvaran- och (helst) HSM på enheten. I stället för att skapa ett enhets-ID i det här steget är enheten helt enkelt programmerad med etableringsinformationen tjänsten så att den anropar tjänsten etablering för att få dess anslutning info/IoT-lösningen tilldelning när den är påslagen.

I det här steget finns också tillverkaren enhet deployer/operator med identifierande viktig information. Det kan vara så enkelt som bekräftar att alla enheter har ett X.509-certifikat som genereras från ett signeringscertifikat som tillhandahålls av enheten deployer/operator, att extrahera den offentliga delen av ett TPM-bekräftelsenyckel från varje TPM-enhet. Dessa tjänster erbjuds av många silicon tillverkare idag.

### <a name="cloud-setup-step"></a>Molnet steget av installationen
Det här steget är om hur du konfigurerar molnet för rätt Automatisk etablering. Vanligtvis det finns två typer av användare ingår i molnet installationen steg: en som vet hur enheter måste du först konfigurera (en operator för enheten) och någon annan som känner till hur enheter är för att delas upp mellan IoT-hubbar (en operator för lösning).

Det finns en enstaka installationen av etableringen som måste inträffa och den här uppgiften hanteras vanligtvis av operatorn lösning. När tjänsten etablering har konfigurerats kan den inte ändras om användningsfallet ändras.

När tjänsten har konfigurerats för automatisk etablering, måste den vara beredd att registrera enheter. Detta görs av operatorn enheten som vet du önskad konfiguration av enheten och är ansvarig för att se till att tjänsten allokering kan korrekt intygar enhetens identitet när det gäller söker efter dess IoT-hubb. Operatorn enheten tar identifierande nyckelinformationen från tillverkaren och lägger till den i listan över registrering. Det kan finnas efterföljande uppdateringar i listan med registrering som att lägga till nya poster eller poster har uppdaterats med den senaste informationen om enheter.

## <a name="registration-and-provisioning"></a>Registrering och etablering
*Etablering* innebär olika saker, beroende på bransch som termen används. I samband med att etablera IoT-enheter med deras molnlösning, är etablering en process i två steg:

1. Den första delen är att upprätta den första anslutningen mellan enheten och IoT-lösningen genom att registrera enheten.
2. Den andra delen är tillämpar korrekt konfiguration baserat på de särskilda kraven i lösningen som den har registrerats för enheten.

Endast när båda dessa två steg har utförts kan vi säga att enheten har etablerats fullständigt. Vissa molntjänster endast ger det första steget i etableringsprocessen registrerar enheter till slutpunkten för IoT-lösning, men ger inte den inledande konfigurationen. Enheten Etableringstjänsten automatiserar båda dessa steg för att ge en allokering smidigt för enheten.

## <a name="features-of-the-device-provisioning-service"></a>Funktioner i enheten etablering av tjänst
Etablering av tjänst har många funktioner som gör det perfekt för etablering enheter.

* **Skydda attestering** stöd för både X.509 och TPM-baserade identiteter.
* **Registrering listan** som innehåller enheter eller grupper av enheter som kan vid en tidpunkt registrera hela posten. Registrering innehåller information om önskad konfiguration för enheten när den registrerar och den kan uppdateras när som helst.
* **Flera resursallokeringsprinciper** att kontrollera hur enheten Etableringstjänsten tilldelar enheter IoT-hubbar stöd för dina scenarier.
* **Övervaknings- och diagnostikfunktionerna loggar** att kontrollera att allt fungerar korrekt.
* **Stöd för flera hubb** vilket gör att enheten Etableringstjänsten tilldela enheter till mer än en IoT-hubb. Etablering av tjänst kan tala med nav över flera Azure-prenumerationer.
* **Stöd för Cross-region** vilket gör att enheten Etableringstjänsten tilldela enheter till IoT-hubbar i andra regioner.

Du kan lära dig mer om koncept och funktioner som ingår i enhetsetableringen i [enheten begrepp](concepts-device.md), [tjänsten begrepp](concepts-service.md), och [säkerhetsbegrepp](concepts-security.md).

## <a name="cross-platform-support"></a>Plattformsoberoende stöd
Enheten Etableringstjänsten, precis som alla Azure IoT-tjänster fungerar plattformsoberoende med olika operativsystem. Azure erbjuder Öppna källa SDK: er i en mängd olika [språk](https://github.com/Azure/azure-iot-sdks) att underlätta ansluta enheter och hantera tjänsten. Etablering av tjänst stöder följande protokoll för att ansluta enheter:

* HTTPS
* AMQP
* AMQP över websockets
* MQTT
* MQTT över websockets

Etablering av tjänst stöder endast HTTPS-anslutningar för tjänståtgärder.

## <a name="regions"></a>Regioner
Etablering av tjänst är tillgänglig i många områden. Vi har en uppdaterad lista av befintliga och nya meddelats regioner för alla tjänster på [Azure-regioner](https://azure.microsoft.com/regions/). Du kan se där tjänsten etablering enhet är tillgänglig på den [Azure Status](https://azure.microsoft.com/status/) sidan.

> [!NOTE]
> Etablering av tjänst är global och inte knuten till en plats. Du måste dock ange en region där metadata som associeras med tjänstprofilen enheten etablering kommer att finnas.

## <a name="availability"></a>Tillgänglighet
Upprätthåller vi ett 99,9% serviceavtal för etablering av tjänst, och du kan [läsa SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/). I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/) förklaras den garanterade tillgängligheten för Azure som helhet.

## <a name="quotas"></a>Kvoter
Varje Azure-prenumeration har standard kvotgränser som kan påverka omfånget för IoT-lösningen. Den aktuella gränsen på grundval av per prenumeration är 10 enhet Provisioning Services per prenumeration.

Mer information om kvotgränserna:

* [Tjänstbegränsningarna för Azure-prenumeration](../azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Relaterade Azure-komponenter
Enheten Etableringstjänsten automatiserar enhetsetableringen med Azure IoT-hubben. Lär dig mer om [IoT-hubb](https://docs.microsoft.com/en-us/azure/iot-hub/).

## <a name="next-steps"></a>Nästa steg
Nu har du en översikt över etablering IoT-enheter i Azure. Nästa steg är att testa en slutpunkt till slutpunkt IoT-scenariot.
> [!div class="nextstepaction"]
> [Ställ in IoT-hubb etablering tjänst med Azure-portalen](quick-setup-auto-provision.md)
> [skapa och etablera en simulerad enhet](quick-create-simulated-device.md)
> [konfigurera enhet för etablering](tutorial-set-up-device.md)
