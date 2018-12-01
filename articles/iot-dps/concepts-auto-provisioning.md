---
title: IoT Hub Device Provisioning-tjänsten – Automatisk etablering
description: 'Den här artikeln innehåller en översikt över faserna i enheten Automatisk etablering, med hjälp av IoT Device Provisioning-tjänsten och IoT Hub client SDK: er.'
author: wesmc7777
ms.author: wesmc
ms.date: 06/01/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 10648551728e4f3cb41b82433e4cd0d442f9daeb
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679264"
---
# <a name="auto-provisioning-concepts"></a>Automatisk etablering

Mer information finns i den [översikt](about-iot-dps.md), Device Provisioning-tjänsten är en hjälptjänst som möjliggör just-in-time-etablering av enheter till en IoT-hubb utan mänsklig inblandning. Efter lyckad etablering ansluta enheter direkt med deras avsedda IoT Hub. Den här processen kallas automatisk etablering och tillhandahåller en out-of the box registrerings- och inledande konfiguration för enheter.

## <a name="overview"></a>Översikt

Azure IoT Automatisk etablering kan delas in i tre faser:

1. **Tjänstkonfigurationen** – en enstaka konfiguration av Azure IoT Hub och IoT Hub Device Provisioning Service instanserna, etablera dem och skapa kopplingen mellan dem.

   > [!NOTE]
   > Oavsett hur stor din IoT-lösning, även om du planerar att stödja miljontals enheter, det här är en **engångskonfiguration**.

2. **Enhetsregistrering** -processen så att enhetsetableringstjänstinstans medveten om de enheter som kommer att försöka registrera i framtiden. [Registrering](concepts-service.md#enrollment) åstadkoms genom att konfigurera enheten identitetsinformation i etableringstjänsten, som en ”enskild registrering” för en enskild enhet eller en ”gruppregistrering” för flera enheter. Identitet är baserad på den [attesteringsmetod](concepts-security.md#attestation-mechanism) enheten har utformats för att använda, vilket gör att etableringstjänsten intyga att enhetens äkthetsbeviset under registreringen:

   - **TPM**: konfigurerats som en ”enskild registrering”, enhetens identitet baseras på TPM registrerings-ID och offentlig bekräftelsenyckeln. Med hänsyn till att TPM är en [specifikationen](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/), tjänsten förväntar sig bara intyga per specifikationen oavsett TPM-implementeringen (maskinvara eller programvara). Se [Device provisioning: styrkande av identitet med betrodd Plattformsmodul](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) mer information om TPM-baserad attestering. 

   - **X509**: konfigurerats som en ”enskild registrering” eller ”gruppregistrering”, enhetens identitet baseras på ett digitalt certifikat från X.509 som överförts till registreringen som en PEM- eller cer-fil.

   > [!IMPORTANT]  
   > Även om det inte är ett krav för att använda Device Provisioning-tjänsten, rekommenderar vi att dina enheten använder en maskinvarusäkerhetsmodul (HSM) att lagra känsliga enheten ID-information, till exempel nycklar och X.509-certifikat.

3. **Enhetsregistrering och konfiguration** - initierad när datorn startas med programvara för enhetsregistrering, som har skapats med Device Provisioning-tjänsten klient-SDK som är lämpliga för mekanismen för enheten och attestering. Programvaran upprättar en anslutning till etableringstjänsten för autentisering av enheten och efterföljande registrering i IoT Hub. Vid lyckad registrering tillhandahålls enheten med sina IoT Hub unikt enhets-ID och anslutningen, så att den kan hämta den ursprungliga konfigurationen och börja telemetri. I produktionsmiljöer, kan det inträffa att den här fasen veckor eller månader efter de föregående två faserna.

## <a name="roles-and-operations"></a>Roller och åtgärder

Faserna beskrivs i föregående avsnitt kan spänna över flera veckor eller månader, på grund av produktion scenarier som tillverkning, frakt, tull processen och så vidare. De kan också omfatta aktiviteter flera flera roller som är beroende av de olika enheterna som ingår. Det här avsnittet tar en närmare titt på de olika roller och åtgärder som rör varje fas och illustrerar flödet som i ett sekvensdiagram. 

Automatisk etablering också placerar krav på enhetens tillverkare, specifikt för att aktivera attesteringsmetod. Tillverkningsprocesser kan också inträffa oberoende av tidsinställningen för automatisk etablering faser, särskilt i de fall där nya enheter skaffas när automatisk etablering har redan skapats.

En serie med Snabbstarter finns i innehållsförteckningen till vänster för att förklara Automatisk etablering via praktisk erfarenhet. För att underlätta/underlätta learning, används programvara för att simulera en fysisk enhet för registrering och registrering. Vissa Snabbstarter måste du utföra åtgärder för flera roller, inklusive åtgärder för icke-existerande roller, på grund av den simulerade natur i Snabbstarter.

| Roll | Åtgärd | Beskrivning | 
|------| --------- | ------------| 
| Tillverkare | Koda identitets- och registrerings-URL | Baserat på den attesteringsmetod som används, ansvarar tillverkaren för kodning enhetsinformation för identitet och Registreringswebbadress Device Provisioning-tjänsten.<br><br>**Snabbstarter**: eftersom enheten simuleras finns ingen tillverkare roll. Se rollen utvecklare mer information om hur du kan få den här informationen som används i kodning ett exempelprogram för registrering. |  
| | Ange enhetens identitet | Tillverkaren är ansvarig för kommunikation till operatorn (eller en avsedda agent) eller registrera den direkt till Device Provisioning-tjänsten via API: er som avsändaren av identitet enhetsinformation.<br><br>**Snabbstarter**: eftersom enheten simuleras finns ingen tillverkare roll. I rollen operatör mer information om hur du får enhetens identitet som används för att registrera en simulerad enhet i din instans av Device Provisioning-tjänsten. | 
| Operator | Konfigurera automatisk etablering | Den här åtgärden motsvarar den första fasen av Automatisk etablering.<br><br>**Snabbstarter**: utföra rollen operatör konfigurera Device Provisioning-tjänsten och IoT Hub-instanser i Azure-prenumerationen. | Konfigurera automatisk etablering |
|  | Registrera enhetsidentitet | Den här åtgärden motsvarar den andra fasen för automatisk etablering.<br><br>**Snabbstarter**: utföra rollen operatör registrera den simulerade enheten i din instans av Device Provisioning-tjänsten. Enhetens identitet bestäms av metoden attestering som simulerade i snabbstarten (TPM eller X.509). Se rollen Developer för attestering information. | 
| Enhetsetableringstjänst,<br>IoT Hub | \<alla åtgärder\> | Dessa roller är uppfyllda via IoT-tjänster som du konfigurerar i din Azure-prenumeration för både en produktionsimplementering med fysiska enheter och Snabbstarter med simulerade enheter. Roller/operations fungerar likadant, som IoT-tjänster är Likgiltig etablering av fysiska eller simulerade enheter. | 
| Developer | Skapa/distribuera programvara för enhetsregistrering | Den här åtgärden motsvarar den tredje fasen av Automatisk etablering. Utvecklaren är ansvarig för att skapa och distribuera programvara för enhetsregistrering till enheten med hjälp av lämplig SDK.<br><br>**Snabbstarter**: registrering exempelprogrammet skapar du simulerar en riktig enhet för plattform/på valfritt språk, som körs på din arbetsstation (i stället för att distribuera den till en fysisk enhet). Registreringen programmet utför samma åtgärder som en distribuerad till en fysisk enhet. Du kan ange metoden attestering (TPM eller X.509-certifikat), plus Registreringswebbadress och ”ID-omfång” i din instans av Device Provisioning-tjänsten. Enhetens identitet bestäms av SDK attestering logiken vid körning, baserat på den metod som du anger: <ul><li>**TPM-attestering** -utvecklingsdatorn körs en [TPM-simulatorn programmet](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). När du kör, används ett separat program för att extrahera TPM: s ”bekräftelsenyckeln” och ”registrerings-ID” för användning i registrerar enhetens identitet. SDK attestering logiken använder också simulatorn under registreringen, för att presentera en signerad SAS-token för autentisering och verifiering för registrering.</li><li>**X509 attestering** – du kan använda ett verktyg för att [generera ett certifikat](how-to-use-sdk-tools.md#x509-certificate-generator). När genereras, skapar du den certifikatfil som krävs för att använda i registrering. SDK attestering logiken använder också certifikatet under registreringen, för att presentera för autentisering och verifiering för registrering.</li></ul> | 
| Enhet | Starttillstånd och registrera | Den här åtgärden motsvarar den tredje fasen av Automatisk etablering, uppfyllas av programvara för enhetsregistrering bygger av utvecklaren. Se rollen utvecklare mer information. Vid den första starten: <ol><li>Programmet ansluter med Enhetsetableringstjänstens instans, per global URL och tjänsten ”ID-omfång” anges under utveckling.</li><li>När du är ansluten, autentiseras enheten mot attestering metoden och identitet som har angetts under registreringen.</li><li>När autentiseringen är enheten registrerad med IoT Hub-instansen som anges av etablering tjänstinstansen.</li><li>Vid lyckad registrering returneras ett unikt enhets-ID och IoT Hub-slutpunkten till Registreringsprogram för att kommunicera med IoT Hub.</li><li> Därifrån kan enheten kan hämta första [enhetstvillingen](~/articles/iot-hub/iot-hub-devguide-device-twins.md) för konfiguration och påbörjar processen med att telemetri rapportdata.</li></ol>**Snabbstarter**: eftersom enheten simuleras registrering-programvaran körs på utvecklingsdatorn.| 

Följande diagram sammanfattar de roller och Sekvenseringen av åtgärder vid enhet Automatisk etablering:
<br><br>
[![Automatisk etablering sekvens för en enhet](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Du kan också tillverkaren kan också utföra åtgärden ”registrera enhetsidentitet” med hjälp av Device Provisioning Service API: er (i stället för via operatorn). En detaljerad beskrivning av den här ordningsföljd och mycket mer, finns det [noll touch-enhetsregistrering med Azure IoT-video](https://youtu.be/cSbDRNg72cU?t=2460) (med början vid markörens 41:00)

## <a name="roles-and-azure-accounts"></a>Roller och Azure-konton

Hur varje roll har mappats till ett Azure-konto är scenario-beroende och det finns ett ganska stort antal scenarier som kan ingå. De vanliga mönster nedan bör bidra till en allmän förståelse om hur Allmänt roller mappas till ett Azure-konto.

#### <a name="chip-manufacturer-provides-security-services"></a>Kretstillverkaren ger säkerhetstjänster

I det här scenariot hanterar tillverkaren säkerhet för nivå 1-kunder. Det här scenariot kan föredras av dessa nivå-1-kunder eftersom de inte behöver hantera detaljerade säkerhet. 

Tillverkaren introducerar säkerhet i Maskinvarusäkerhetsmoduler (HSM). Den här säkerhetsuppdateringen kan innehålla tillverkaren skaffa nycklar, certifikat, etc. från potentiella kunder som redan har DPS-instanser och registreringskonfiguration för grupper. Tillverkaren kan även skapa den här säkerhetsinformationen för sina kunder.

I det här scenariot kan finnas det två Azure-konton som ingår:

- **Kontot #1**: sannolikt delas mellan rollerna operator och utvecklare viss grad. Den här parten kan köpa HSM-kretsar från tillverkaren. Dessa kretsar är visade DPS-instanser som är associerade med kontot nr 1. Den här parten kan med DPS registreringar låna ut enheter till flera nivå två kunder genom att omkonfigurera Enhetsinställningar för registrering i DPS. Den här parten kan också ha IoT-hubbar som allokerats för slutanvändaren serverdelssystem att samverka med för att komma åt enhetstelemetri osv. I det senare fallet kanske du inte behövs ett andra konto.

- **Kontot #2**: slutanvändare, nivå två kunder kan ha sina egna IoT-hubbar. Den part som är associerade med kontot nr 1 bara punkter lånade enheter till rätt hubben i det här kontot. Den här konfigurationen kräver länkar DPS och IoT hubs över Azure-konton kan du göra med Azure Resource Manager-mallar.

#### <a name="all-in-one-oem"></a>Allt-i-ett OEM

Tillverkaren kan vara en ”allt-i-ett OEM-tillverkare” där bara ett enda konto skulle behövas. Tillverkaren hanterar säkerhets- och etablering från slutpunkt till slutpunkt.

Tillverkaren kan tillhandahålla ett molnbaserade program till kunder som köper enheter. Det här programmet skulle gränssnitt med IoT Hub som tilldelats av tillverkaren.

Varuautomater eller automatiserade kaffe datorer representerar exemplen i det här scenariot.




## <a name="next-steps"></a>Nästa steg

Du kan det vara bra att bokmärk den här artikeln som en referens som du gå igenom det motsvarande Snabbstart för automatisk etablering. 

Börja med att du har slutfört snabbstarten ”ställa in automatisk etablering” som bäst passar föredrar management verktyg som vägleder dig genom ”Service konfigurationsfasen”:

- [Konfigurera automatisk etablering med Azure CLI](quick-setup-auto-provision-cli.md)
- [Konfigurera automatisk etablering med Azure portal](quick-setup-auto-provision.md)
- [Konfigurera automatisk etablering med en Resource Manager-mall](quick-setup-auto-provision-rm.md)

Fortsätt sedan med en ”automatiskt etablera en simulerad enhet”-Snabbstart som passar din attesteringsmetod för enheten och Enhetsetableringstjänsten SDK/språkinställning. I den här snabbstarten gå igenom de olika faserna för ”enhetsregistrering” och ”enhetsregistrering och konfiguration”: 

|  | Simulerad enhet attesteringsmetod | Snabbstart SDK/språk |  |
|--|--|--|--|
|  | Trusted Platform Module (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | X.509-certifikat | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




