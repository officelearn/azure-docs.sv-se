---
title: IoT Hub Device Provisioning Service - Begrepp för automatisk etablering
description: Den här artikeln innehåller en begreppsmässig översikt över faserna för automatisk etablering av enheter med DPS (IoT Device Provisioning Service), IoT Hub och klient-SDK:er.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c94fa6b851dfc9923628a738a15f7c245204f73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975337"
---
# <a name="auto-provisioning-concepts"></a>Begrepp för automatisk etablering

Som beskrivs i [översikten](about-iot-dps.md)är enhetsetableringstjänsten en hjälptjänst som möjliggör just-in-time-etablering av enheter till en IoT-hubb, utan att kräva mänsklig inblandning. Efter en lyckad etablering ansluter enheter direkt till deras utsedda IoT Hub. Den här processen kallas automatisk etablering och ger en direkt registrering och inledande konfigurationsupplevelse för enheter.

## <a name="overview"></a>Översikt

Automatisk etablering av Azure IoT kan delas upp i tre faser:

1. **Tjänstkonfiguration** - en engångskonfiguration av Azure IoT Hub- och IoT Hub Device Provisioning Service-instanser, upprätta dem och skapa länkning mellan dem.

   > [!NOTE]
   > Oavsett storleken på din IoT-lösning, även om du planerar att stödja miljontals enheter, är detta en **engångskonfiguration**.

2. **Enhetsregistrering** - processen att göra instansen för enhetsetableringstjänst medveten om de enheter som kommer att försöka registrera sig i framtiden. [Registreringen](concepts-service.md#enrollment) sker genom att enhetsidentitetsinformation konfigureras i etableringstjänsten, antingen som en "individuell registrering" för en enda enhet eller en "gruppregistrering" för flera enheter. Identiteten baseras på den [attesteringsmekanism](concepts-security.md#attestation-mechanism) som enheten är konstruerad för att använda, vilket gör det möjligt för etableringstjänsten att intyga att enhetens äkthet under registreringen:

   - **TPM**: konfigurerad som en "individuell registrering", enhetens identitet baseras på TPM-registrerings-ID och den offentliga godkännandenyckeln. Med tanke på att TPM är en [specifikation](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)förväntar sig tjänsten endast att intyga enligt specifikationen, oavsett TPM-implementering (maskinvara eller programvara). Se [Enhetsetablering: Identitetsintyg med TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) för mer information om TPM-baserad attestation. 

   - **X509**: konfigurerad som antingen en "individuell registrering" eller "gruppregistrering", enhetens identitet baseras på ett X.509 digitalt certifikat, som överförs till registreringen som en .pem eller .cer-fil.

   > [!IMPORTANT]  
   > Även om det inte är en förutsättning för att använda Enhetsetableringstjänster rekommenderar vi starkt att enheten använder en HSM (Hardware Security Module) för att lagra känslig enhetsidentitetsinformation, till exempel nycklar och X.509-certifikat.

3. **Enhetsregistrering och konfiguration** - initieras vid uppstart av registreringsprogramvara, som är byggd med hjälp av en Enhetsetableringstjänstklient SDK som är lämplig för enheten och attesteringsmekanismen. Programvaran upprättar en anslutning till etableringstjänsten för autentisering av enheten och efterföljande registrering i IoT Hub. Vid lyckad registrering får enheten sin IoT Hub unika enhets-ID och anslutningsinformation, så att den kan hämta sin ursprungliga konfiguration och påbörja telemetriprocessen. I produktionsmiljöer kan denna fas inträffa veckor eller månader efter de två föregående faserna.

## <a name="roles-and-operations"></a>Roller och operationer

De faser som diskuteras i föregående avsnitt kan sträcka sig över veckor eller månader, på grund av produktionen realiteter som tillverkningstid, sjöfart, tullprocessen, etc. Dessutom kan de sträcka sig över aktiviteter över flera roller med tanke på de olika inblandade entiteterna. Det här avsnittet tar en djupare titt på de olika roller och åtgärder som är relaterade till varje fas och illustrerar sedan flödet i ett sekvensdiagram. 

Automatisk etablering ställer också krav på enhetstillverkaren, som är specifika för att aktivera attesteringsmekanismen. Tillverkningsåtgärder kan också ske oberoende av tidpunkten för automatisk etablering faser, särskilt i de fall där nya enheter upphandlas efter automatisk etablering redan har fastställts.

En serie snabbstarter finns i innehållsförteckningen till vänster, för att förklara automatisk etablering genom praktisk erfarenhet. För att underlätta/förenkla inlärningsprocessen används programvara för att simulera en fysisk enhet för registrering och registrering. Vissa snabbstarter kräver att du fullgör åtgärder för flera roller, inklusive åtgärder för icke-existerande roller, på grund av snabbstartarnas simulerade natur.

| Roll | Åtgärd | Beskrivning |
|------| --------- | ------------|
| Tillverkare | Koda url för identitet och registrering | Baserat på den attestation mekanism som används, är tillverkaren ansvarig för kodning enhetens identitetsinformation, och enheten etablering tjänstens registreringsadress.<br><br>**Snabbstarter:** Eftersom enheten simuleras finns det ingen tillverkarroll. Mer information om hur du hämtar den här informationen finns i kodning av ett exempelregistreringsprogram. |
| | Ange enhetsidentitet | Som upphovsman till enhetsidentitetsinformationen ansvarar tillverkaren för att kommunicera den till operatören (eller en utsedd agent) eller direkt registrera den till enhetsetableringstjänsten via API:er.<br><br>**Snabbstarter:** Eftersom enheten simuleras finns det ingen tillverkarroll. Mer information om hur du hämtar enhetsidentiteten finns i rollen Operatör. |
| Operator | Konfigurera automatisk etablering | Den här åtgärden motsvarar den första fasen av automatisk etablering.<br><br>**Snabbstarter:** Du utför operatorrollen och konfigurerar enhetsetableringstjänsten och IoT Hub-instanserna i din Azure-prenumeration. |
|  | Registrera enhetsidentitet | Den här åtgärden motsvarar den andra fasen av automatisk etablering.<br><br>**Snabbstarter:** Du utför operatorrollen och registrerar den simulerade enheten i instansen av enhetsetableringstjänsten. Enhetsidentiteten bestäms av attestation-metoden som simuleras i Snabbstarten (TPM eller X.509). Se utvecklarrollen för att intyga information. |
| Tjänsten för etablering av enheter,<br>IoT Hub | \<alla verksamheter\> | För både en produktionsimplementering med fysiska enheter och snabbstarter med simulerade enheter uppfylls dessa roller via IoT-tjänsterna som du konfigurerar i din Azure-prenumeration. Rollerna/åtgärderna fungerar exakt på samma sätt, eftersom IoT-tjänsterna är likgiltiga för etablering av fysiska kontra simulerade enheter. |
| Developer | Skapa/distribuera registreringsprogram | Den här åtgärden motsvarar den tredje fasen av automatisk etablering. Utvecklaren ansvarar för att bygga och distribuera registreringsprogramvaran till enheten med hjälp av lämplig SDK.<br><br>**Snabbstarter**: Exempelregistreringsprogrammet som du skapar simulerar en riktig enhet, för din plattform/språk som körs på din arbetsstation (i stället för att distribuera den till en fysisk enhet). Registreringsprogrammet utför samma åtgärder som en som distribueras till en fysisk enhet. Du anger attestation-metoden (TPM- eller X.509-certifikatet), plus registrerings-URL:en och "ID-scopet" för din enhetsetableringstjänstinstans. Enhetsidentiteten bestäms av SDK-attestation-logiken vid körning, baserat på den metod du anger: <ul><li>**TPM attestation** - din utveckling arbetsstation kör en [TPM simulator program](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). När du kör, ett separat program används för att extrahera TPM: s "Endorsement Key" och "Registrering ID" för användning för att registrera enhetens identitet. SDK-attestation-logiken använder också simulatorn under registreringen för att presentera en signerad SAS-token för autentisering och registreringsverifiering.</li><li>**X509 attestation** - du använder ett verktyg för att [generera ett certifikat](how-to-use-sdk-tools.md#x509-certificate-generator). När du har genererat den certifikatfil som krävs för användning i registreringen. SDK-attestation-logiken använder också certifikatet under registreringen för att presentera för autentisering och registreringsverifiering.</li></ul> |
| Enhet | Starta och registrera | Den här åtgärden motsvarar den tredje fasen av automatisk etablering, som uppfylls av enhetens registreringsprogram som skapats av utvecklaren. Mer information finns i utvecklarrollen. Vid första uppstart: <ol><li>Programmet ansluter till instansen Enhetsetableringstjänst, enligt den globala URL och tjänsten "ID-scope" som anges under utvecklingen.</li><li>När enheten har anslutits autentiseras den mot den attestation-metod och identitet som anges under registreringen.</li><li>När enheten har autentiserats registreras den med IoT Hub-instansen som anges av etableringstjänstinstansen.</li><li>När en unik enhets-ID och IoT Hub-slutpunkt har registrerats returneras den till registreringsprogrammet för kommunikation med IoT Hub.</li><li> Därifrån kan enheten dra ner sitt ursprungliga [enhetstvillingtillstånd](~/articles/iot-hub/iot-hub-devguide-device-twins.md) för konfiguration och påbörja rapporteringsprocessen för telemetridata.</li></ol>**Snabbstarter:** Eftersom enheten simuleras körs registreringsprogrammet på din utvecklingsarbetsstation.|

I följande diagram sammanfattas roller och sekvensering av åtgärder under automatisk etablering av enheter:
<br><br>
[![Automatisk etableringssekvens för en enhet](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Alternativt kan tillverkaren också utföra åtgärden "Registrera enhetsidentitet" med api:er för enhetsetableringstjänst (i stället för via operatören). En detaljerad diskussion om den här sekvenseringen med mera finns i registreringen av [Nolltouchenhet med Azure IoT-video](https://youtu.be/cSbDRNg72cU?t=2460) (med början vid markör 41:00)

## <a name="roles-and-azure-accounts"></a>Roller och Azure-konton

Hur varje roll mappas till ett Azure-konto är scenarioberoende och det finns en hel del scenarier som kan vara inblandade. De gemensamma mönstren nedan bör bidra till att ge en allmän förståelse för hur roller i allmänhet mappas till ett Azure-konto.

#### <a name="chip-manufacturer-provides-security-services"></a>Chiptillverkaren tillhandahåller säkerhetstjänster

I det här fallet hanterar tillverkaren säkerhet för nivå-ett-kunder. Det här scenariot kan föredras av dessa nivå-ett-kunder eftersom de inte behöver hantera detaljerad säkerhet. 

Tillverkaren introducerar säkerhet i maskinvarusäkerhetsmoduler (HSM). Den här säkerheten kan omfatta att tillverkaren hämtar nycklar, certifikat etc. från potentiella kunder som redan har installation av DPS-instanser och registreringsgrupper. Tillverkaren kan också generera denna säkerhetsinformation för sina kunder.

I det här fallet kan det finnas två Azure-konton inblandade:

- **Konto #1**: Sannolikt delas över operatören och utvecklare roller till viss del. Denna part kan köpa HSM-chips från tillverkaren. Dessa marker pekar på DPS-instanser som är associerade med #1. Med DPS-registreringar kan den här parten låna ut enheter till flera nivå två-kunder genom att konfigurera om enhetsregistreringsinställningarna i DPS. Den här parten kan också ha IoT-hubbar som tilldelats för slutanvändarbacksdelsystem att samverka med för att komma åt enhetstelemetri etc. I det senare fallet kanske ett andra konto inte behövs.

- **Konto #2:** Slutanvändare, nivå två kunder kan ha sina egna IoT-hubbar. Den part som är associerad med Konto #1 pekar bara ut hyrda enheter till rätt hubb i det här kontot. Den här konfigurationen kräver länkning av DPS- och IoT-hubbar över Azure-konton, vilket kan göras med Azure Resource Manager-mallar.

#### <a name="all-in-one-oem"></a>Allt-i-ett-OEM-tillverkare

Tillverkaren kan vara en "Allt-i-ett OEM" där endast en enda tillverkare konto skulle behövas. Tillverkaren hanterar säkerhet och etablering från till.

Tillverkaren kan tillhandahålla ett molnbaserat program till kunder som köper enheter. Denna ansökan skulle samverka med IoT Hub tilldelas av tillverkaren.

Varuautomater eller automatiserade kaffemaskiner utgör exempel för det här scenariot.




## <a name="next-steps"></a>Nästa steg

Det kan vara bra att bokmärka den här artikeln som en referenspunkt när du arbetar dig igenom motsvarande snabbstartar för automatisk etablering. 

Börja med att slutföra en snabbstart för automatisk etablering av automatisk etablering som bäst passar dina inställningar för hanteringsverktyg, som leder dig genom fasen "Tjänstkonfiguration":

- [Konfigurera automatisk etablering med Azure CLI](quick-setup-auto-provision-cli.md)
- [Konfigurera automatisk etablering med Azure-portalen](quick-setup-auto-provision.md)
- [Ställa in automatisk etablering med hjälp av en Resource Manager-mall](quick-setup-auto-provision-rm.md)

Fortsätt sedan med en snabbstart för en "Automatisk etablering av en simulerad enhet" som passar enhetens attestationmekanism och enhetsetableringstjänst SDK/språkinställningar. I den här snabbstarten går du igenom faserna "Enhetsregistrering" och "Enhetsregistrering och konfiguration": 

|  | Mekanism för simulerad enhetsintyg | Snabbstart SDK/Språk |  |
|--|--|--|--|
|  | Trusted Platform Module (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | X.509 certifikat | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




