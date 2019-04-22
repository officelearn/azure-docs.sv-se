---
title: Översikt över Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Beskriver enhetsetablering i Azure med Device Provisioning Service och IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 89e0b6b5bedf36c89bf3a54f4baf42f7019625c0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051481"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Enhetsetablering med Azure IoT Hub Device Provisioning Service
Microsoft Azure har en stor uppsättning integrerade offentliga molntjänster för alla dina IoT-lösningsbehov. IoT Hub Device Provisioning Service är en hjälptjänst för IoT Hub som möjliggör zero-touch och just-in-time-etablering till rätt IoT-hubb utan mänsklig interaktion, vilket gör att kunder kan etablera miljontals enheter på ett säkert och skalbart sätt.

## <a name="when-to-use-device-provisioning-service"></a>När Device Provisioning Service ska användas
Det finns många etableringsscenarier där Device Provisioning Service är ett utmärkt val för att ansluta och konfigurera enheter för IoT Hub, bland annat:

* Zero touch-etablering till en enda IoT-lösning utan att hårdkoda IoT Hub-anslutningsinformationen som fabriksinställning (första konfigurationen)
* Belastningsutjämning för enhet mellan flera nav
* Anslutning av enheter till sina ägares IoT-lösning baserat på transaktionsdata för försäljning (flera organisationer i samma installation)
* Anslutning av enheter till en viss IoT-lösning beroende på användningsfall (lösningsisolering)
* Anslutning av en enhet till den IoT-hubb som har lägst fördröjning (geografisk horisontell partitionering)
* Ometablering baserat på en ändring i enheten
* Rotera de nycklar som används av enheten för att ansluta till IoT Hub (när X.509-certifikat inte används för att ansluta)

## <a name="behind-the-scenes"></a>I bakgrunden
Alla scenarier som beskrivs i föregående avsnitt kan utföras med hjälp av etableringstjänsten för zero touch-etablering med samma flöde. Många av de manuella stegen som vanligtvis ingår i etablering automatiseras med Device Provisioning Service för att minska tiden för att distribuera IoT-enheter och minska risken för manuella fel. I följande avsnitt beskrivs vad som händer i bakgrunden när en enhet etableras. Det första steget är manuellt; alla följande steg utförs automatiskt.

![Grundläggande etableringsflöde](./media/about-iot-dps/dps-provisioning-flow.png)

1. Enhetstillverkaren lägger till enhetsregistreringsinformation i registreringslistan i Azure-portalen.
2. Enheten kontaktar den etableringstjänstslutpunkt som anges på fabriken. Enheten skickar identifierande information till etableringstjänsten för att bevisa sin identitet.
3. Etableringstjänsten verifierar enhetens identitet genom att verifiera registrerings-ID och nyckeln mot registreringslistposten via antingen en nonce-kontroll ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) eller standardmässig X.509-verifiering (X.509).
4. Etableringstjänsten registrerar enheten med en IoT-hubb och fyller enhetens [önskade tvillingstatus](../iot-hub/iot-hub-devguide-device-twins.md).
5. IoT-hubben returnerar enhetens ID-information till etableringstjänsten.
6. Etableringstjänsten returnerar IoT-hubbanslutningsinformationen till enheten. Enheten kan nu börja skicka data direkt till IoT-hubben.
7. Enheten ansluter till IoT-hubben.
8. Enheten får önskad status från sin tvillingenhet i IoT-hubben.

## <a name="provisioning-process"></a>Etableringsprocessen
Det finns två separata steg i etableringsprocessen för en enhet där Device Provisioning Service hanterar en del som kan göras oberoende:

* **Tillverkningssteget** där enheten skapas och förberedas på fabriken samt
* **Molnkonfigurationssteget** där Device Provisioning Service konfigureras för automatisk etablering.

Båda dessa steg passar smidigt in i befintliga tillverknings- och etableringsprocesser. Dessutom förenklar Device Provisioning Service vissa etableringsprocesser som kräver mycket manuellt arbete för att skicka anslutningsinformation till enheten.

### <a name="manufacturing-step"></a>Tillverkningssteg
Det här steget handlar om vad som händer vid tillverkningen. De roller som ingår i det här steget innefattar kiseldesigner, kiseltillverkare, integrerare och sluttillverkare av enheten. Det här steget handlar om tillverkningen av själva maskinvaran.

Device Provisioning Service inför inte något nytt steg i tillverkningen. I stället ingår det i det befintliga steget som installerar den första programvaran och (helst) HSM på enheten. I stället för att ett enhets-ID skapas i det här steget programmeras enheten med information om etableringstjänsten, vilket gör att den kan anropa etableringstjänsten för att få sin tilldelning för anslutningsinformation/IoT-lösning när den aktiveras.

I det här steget ger också tillverkaren enhetsdistributören/-operatören identifierande nyckelinformation. Att tillhandahålla den informationen kan vara så enkelt som att bekräfta att alla enheter har ett X.509-certifikat som genereras från ett signeringscertifikat som tillhandahålls av enhetsdistributören/-operatören eller så komplicerat som att extrahera den offentliga delen av en TPM-bekräftelsenyckel från varje TPM-enhet. Dessa tjänster erbjuds av många kiseltillverkare i dag.

### <a name="cloud-setup-step"></a>Molnkonfigurationssteg
Det här steget handlar om att konfigurera molnet för korrekt automatisk etablering. Generellt finns det två typer av användare som ingår i molnkonfigurationssteget: någon som vet hur enheter måste konfigureras i början (en enhetsoperatör) och någon som vet hur enheter ska delas upp mellan IoT-hubbar (en lösningsoperatör).

Det finns en första engångskonfiguration av etableringen som måste utföras. Detta hanteras vanligtvis av lösningsoperatören. När etableringstjänsten har konfigurerats behöver den inte ändras såvida inte användningsfallet ändras.

När tjänsten har konfigurerats för automatisk etablering måste den förberedas inför att registrera enheter. Det här steget utförs av enhetsoperatören, som känner till önskad konfiguration för enheten och är ansvarig för att se till att etableringstjänsten kan garantera enhetens identitet korrekt när enheten söker efter sin IoT-hubb. Enhetsoperatören tar emot den identifierande nyckelinformationen från tillverkaren och lägger till den i registreringslistan. Det kan förekomma efterföljande uppdateringar av registreringslistan allt eftersom nya poster läggs till eller befintliga poster uppdateras med den senaste informationen om enheterna.

## <a name="registration-and-provisioning"></a>Registrering och etablering
*Etablering* innebär olika saker beroende på vilken bransch termen används i. I samband med etablering av IoT-enheter till sina molnlösningar är etablering en tvåstegsprocess:

1. Den första delen är att upprätta den första anslutningen mellan enheten och IoT-lösningen genom att registrera enheten.
2. Den andra delen är att tillämpa korrekt konfiguration på enheten baserat på de särskilda kraven i den lösning som enheten registrerats för.

När båda av dessa två steg har slutförts kan enheten anses ha etablerats fullständigt. Vissa molntjänster tillhandahåller endast det första steget i etableringsprocessen genom att registrera enheter till IoT-lösningens slutpunkt men utan att ge den inledande konfigurationen. Device Provisioning Service automatiserar båda stegen och ger en smidig etableringsfunktion för enheten.

## <a name="features-of-the-device-provisioning-service"></a>Funktion i Device Provisioning Service
Device Provisioning Service innehåller många funktion, vilket gör den utmärkt för enhetsetablering.

* Stöd för **säker attestering** för både X.509- och TPM-baserade identiteter.
* **Registreringslista** som innehåller fullständiga uppgifter för enheter eller grupper av enheter som kan komma att registreras. Registreringslistan innehåller information om önskad konfiguration för enheten när den registreras, och den kan uppdateras när som helst.
* **Principer för flera allokeringar** för att kontrollera hur Device Provisioning Service tilldelar enheter till IoT-hubbar som stöd för dina scenarier.
* **Övervaknings- och diagnostikloggning** för att kontrollera att allt fungerar korrekt.
* **Stöd för flera hubbar** gör att Device Provisioning Service kan tilldela enheter till mer än en IoT-hubb. Device Provisioning Service kan kommunicera med hubbar över flera Azure-prenumerationer.
* **Stöd mellan regioner** gör att Device Provisioning Service kan tilldela enheter till IoT-hubbar i andra regioner.

Du kan lära dig mer om de begrepp och funktioner som ingår i enhetsetablering i [enhetsbegrepp](concepts-device.md), [tjänstebegrepp](concepts-service.md) och [säkerhetsbegrepp](concepts-security.md).

## <a name="cross-platform-support"></a>Stöd för alla plattformar
Liksom alla Azure IoT-tjänster fungerar Device Provisioning Service plattformsoberoende med ett flertal olika operativsystem. Azure erbjuder SDK:er med öppen källkod i en mängd [språk](https://github.com/Azure/azure-iot-sdks), vilket underlättar anslutning av enheter och hantering av tjänsten. Device Provisioning Service stödjer följande protokoll för att ansluta enheter:

* HTTPS
* AMQP
* AMQP över WebSockets
* MQTT
* MQTT över WebSockets

Device Provisioning Service stödjer endast HTTPS-anslutningar för tjänståtgärder.

## <a name="regions"></a>Regioner
Device Provisioning Service är tillgänglig i många regioner. En uppdaterad lista över befintliga och nyligen tillkännagivna regioner för alla tjänster finns på [Azure Regions](https://azure.microsoft.com/regions/) (Azure-regioner). Du kan kontrollera tillgängligheten för Device Provisioning Service på sidan [Azure Status](https://azure.microsoft.com/status/) (Azure-status).

> [!NOTE]
> Device Provisioning Service är global och inte knuten till en viss plats. Du måste dock ange en region där metadata som associeras med din Device Provisioning Service-profil kommer att lagras.

## <a name="availability"></a>Tillgänglighet
Det finns ett 99,9 % serviceavtal för Device Provisioning Service, och du kan [läsa serviceavtalet](https://azure.microsoft.com/support/legal/sla/iot-hub/). I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/) förklaras den garanterade tillgängligheten för Azure som helhet.

## <a name="quotas"></a>Kvoter
Varje Azure-prenumeration har standardkvotgränser som kan påverka IoT-lösningens omfång. Den aktuella gränsen på per prenumerationsbasis är 10 Device Provisioning Services per prenumeration.

Mer information om kvotgränser:

* [Tjänstbegränsningar för Azure-prenumeration](../azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Relaterade Azure-komponenter
Device Provisioning Service automatiserar enhetsetablering med Azure IoT Hub. Läs mer om [IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

## <a name="next-steps"></a>Nästa steg
Nu har du en översikt över etablering av IoT-enheter i Azure. Nästa steg är att testa ett IoT-scenario från slutpunkt till slutpunkt.
> [!div class="nextstepaction"]
> [Konfigurera IoT Hub Device Provisioning Service med Azure-portalen](quick-setup-auto-provision.md)
> [Skapa och etablera en simulerad enhet](quick-create-simulated-device.md)
> [Konfigurera en enhet för etablering](tutorial-set-up-device.md)
