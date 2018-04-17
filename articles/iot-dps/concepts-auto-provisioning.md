---
title: IoT-hubb enheten Etableringstjänsten - Automatisk etablering begrepp
description: 'Den här artikeln innehåller en översikt över faser i enheten automatiskt-etablering, med hjälp av Etableringstjänsten för IoT-enhet, IoT-hubb och klient-SDK: er.'
services: iot-dps
keywords: ''
author: BryanLa
ms.author: bryanla
ms.date: 03/27/2018
ms.topic: conceptual
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: e743f40a1f8ff71fe93f14217b410df348d9903d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="auto-provisioning-concepts"></a>Automatisk etablering begrepp

Enligt beskrivningen i den [översikt](about-iot-dps.md), etablering av tjänst är en helper-tjänst som gör just-in-time etablering av enheter till en IoT-hubb utan mänsklig inblandning. Efter lyckad etablering ansluta enheter direkt med deras avsedda IoT-hubb. Den här processen kallas för automatisk etablering och tillhandahåller en out box registrering och den inledande konfigurationen för enheter.

## <a name="overview"></a>Översikt

Azure IoT-auto-etablering kan delas i tre faser:

1. **Tjänstkonfigurationen** -en engångskonfiguration för Azure IoT Hub- och IoT-hubb enheten Etableringstjänsten instanser, etablera dem och hur du skapar kopplingen mellan dem.

   > [!NOTE]
   > Oavsett storleken på din IoT-lösning, även om du planerar att stödja miljoner enheter, detta är en **engångskonfiguration**.

2. **Enhetsregistrering** -processen att göra tjänstinstansen enheten etablering medveten om de enheter som kommer att försöka registrera i framtiden. [Registrering](concepts-service.md#enrollment) görs genom att konfigurera enheten identitetsinformation i tjänsten allokering som en ”enskilda registrering” för en enstaka enhet eller en ”grupp registrering” för flera enheter. Identiteten är baserat på den [attestering mekanism](concepts-security.md#attestation-mechanism) enheten är utformat för att använda, vilket gör att tjänsten etablering att bekräfta att enhetens äkthetsbeviset under registreringen:

   - **TPM**: konfigurerad som en ”enskilda registrering” enhetens identitet baseras på TPM registrerings-ID och offentliga bekräftelsenyckeln. Med hänsyn till att TPM är en [specifikationen]((https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)), tjänsten bara förväntar att bekräfta per specifikationen oavsett TPM-implementeringen (maskinvara eller programvara). Se [enhetsetableringen: identitet attestering med TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/) mer information om TPM-baserade attestering. 

   - **X509**: konfigurerad som en ”enskilda registrering” eller ”grupp registrering” enhetens identitet baseras på ett digitalt certifikat från X.509 som överförs till registreringen som en .pem eller .cer-fil.

   > [!IMPORTANT]  
   > Även om inte ett krav för att använda enheten Provisioning Services, rekommenderar vi att du använder en maskinvarusäkerhetsmodul (HSM) att lagra känsliga enhetens identitetsinformation, till exempel nycklar och X.509-certifikat för enheten.

3. **Enhetsregistrering och konfiguration** - initierat när datorn startas av registrering programvara som skapas med en tjänst för etablering klient SDK för mekanismen för enheten och attestering. Programvaran upprättar en anslutning till tjänsten etablering för autentisering av enheten och efterföljande registrering i IoT-hubben. Enheten har angetts med dess IoT-hubb unikt enhets-ID och anslutningen, så att den kan ta den inledande konfigurationen och påbörja telemetri vid lyckad registrering. I produktionsmiljöer, kan den här fasen inträffa veckor eller månader efter föregående två faser.

## <a name="roles-and-operations"></a>Roller och åtgärder

Faserna beskrivs i föregående avsnitt kan omfatta veckor eller månader, på grund av produktion verkligheten som tillverkning tid leverans, tull process, osv. Dessutom omfattar de aktiviteter över flera roller som anges de olika enheterna som ingår. Det här avsnittet tar en djupare inblick i olika roller och åtgärder som rör varje fas och sedan illustrerar flödet i ett sekvensdiagram. 

Automatisk etablering även placeras krav på enhetens tillverkare för att aktivera mekanismen för attestering. Tillverkar operations kan även uppstå oberoende av val av tidpunkt för automatisk etablering faser, speciellt i fall där nya enheter skaffas när automatisk etablering finns redan.

En serie Snabbstart finns i innehållsförteckningen till vänster för att förklara Automatisk etablering via praktisk erfarenhet. För att underlätta/förenkla learning, används programvara för att simulera en fysisk enhet för registrering och registrering. Vissa Snabbstart måste du utföra åtgärder för flera roller, inklusive åtgärder för obefintlig roller på grund av Snabbstart för den simulerade.

| Roll | Åtgärd | Beskrivning | 
|------| --------- | ------------| 
| Tillverkare | Koda identitet och registrering URL | Baserat på attestering mekanismen som används, ansvarar tillverkaren för kodning identitet enhetsinformation och URL: en för etablering av tjänst registrering.<br><br>**Snabbstart**: eftersom enheten simuleras, det finns ingen tillverkare roll. Se rollen utvecklare mer information om hur du kan få den här informationen som används för att koda ett exempelprogram för registrering. |  
| | Ange enhets-ID | Tillverkaren är ansvarig för kommunikation med operatorn (eller en avsedda agent) eller registrera den direkt till enheten Etableringstjänsten via API: er som skickat enhetsinformation för identitet.<br><br>**Snabbstart**: eftersom enheten simuleras, det finns ingen tillverkare roll. Finns i rollen operatör mer information om hur du få enhetens identitet som används för att registrera en simulerad enhet i din enhet etablering Service-instans. | 
| Operator | Konfigurera automatisk etablering | Den här åtgärden motsvarar den första fasen av Automatisk etablering.<br><br>**Snabbstart**: utföra rollen operatör konfigurera enheten Etableringstjänsten och IoT-hubb instanser i din Azure-prenumeration. | Ställa in automatisk-etablering |
|  | Registrera enhetsidentitet | Den här åtgärden motsvarar den andra fasen för automatisk etablering.<br><br>**Snabbstart**: utföra rollen operatör registreras den simulerade enheten i din enhet etablering tjänstinstansen. Enhetens identitet bestäms av metoden attestering som simulerade i Snabbstart (TPM eller X.509). Se rollen Developer för attestering av information. | 
| Etablering tjänst<br>IoT Hub | \<alla åtgärder\> | För både en produktionsimplementering med fysiska enheter och Snabbstart med simulerade enheter, är dessa roller uppfyllda via IoT-tjänster som du konfigurerar i din Azure-prenumeration. Roller/operations fungerar exakt detsamma, IoT-tjänster är Likgiltig etablering av fysiska kontra simulerade enheter. | 
| Developer | Skapa/distribuera programvara för registrering | Den här åtgärden motsvarar den tredje fasen av Automatisk etablering. Utvecklaren är ansvarig för att skapa och distribuera programmet registreringen till enheten med hjälp av lämplig SDK.<br><br>**Snabbstart**: registrering exempelprogrammet som du skapar simulerar en verklig enhet för din plattform/språk, som körs på din arbetsstation (i stället för att distribuera den till en fysisk enhet). Registreringen programmet utför samma åtgärder som en distribuerad till en fysisk enhet. Du kan ange attestering-metoden (TPM eller X.509-certifikat), plus URL: en registrering och ”ID omfattning” Device etablering Service-instans. Enhetens identitet bestäms av SDK attestering logiken vid körning, baserat på den metod som du anger: <ul><li>**TPM-nyckelattestering** -utvecklingsdatorn kör en [TPM simulator programmet](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). När körs används ett separat program för att extrahera TPM ”bekräftelsenyckel” och ”registrerings-ID” för användning i registrera enhetens identitet. SDK attestering logiken använder också simulatorn under registreringen presentera en signerad SAS-token för autentisering och registrering av verifiering.</li><li>**X509 attestering** -du använda ett verktyg som [generera ett certifikat](how-to-use-sdk-tools.md#x509-certificate-generator). När genereras, skapar du den certifikatfil som krävs för registrering. SDK attestering logiken använder också certifikatet under registreringen för autentisering och registrering av verifiering.</li></ul> | 
| Enhet | Start- och registrera | Den här åtgärden motsvarar den tredje fasen av Automatisk etablering, uppfyllas av registrering enhetsprogrammet skapat av utvecklaren. Se rollen utvecklare mer information. Vid den första starten: <ol><li>Programmet ansluter till enheten etablering tjänstinstansen, per globala URL och service ”ID” angivna omfånget under utvecklingen.</li><li>När du är ansluten, autentisera enheten mot attestering metoden och identitet som har angetts under registreringen.</li><li>När autentiseringen är registrerad enheten med IoT-hubb-instans som anges av etablering tjänstinstansen.</li><li>Lyckad registrering är ett unikt enhets-ID och IoT-hubb endpoint tillbaka till registreringen programmet för att kommunicera med IoT-hubben.</li><li> Därifrån kan enheten hämtar första [enheten dubbla](~/articles/iot-hub/iot-hub-devguide-device-twins.md) för konfiguration och påbörja reporting telemetridata.</li></ol>**Snabbstart**: eftersom enheten simuleras registreringen programmet körs på utvecklingsdatorn.| 

I följande diagram visas roller och sekvensering av åtgärder under auto-enhetsetableringen:
<br><br>
![Automatisk etablering sekvens för en enhet](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png) 

> [!NOTE]
> Du kan också tillverkaren kan också utföra åtgärden ”registrera enhetsidentitet” med enheten etablering Service API: er (i stället för via operatorn). En detaljerad beskrivning av den här ordningsföljd och mer finns i [noll touch-enhetsregistrering med Azure IoT video](https://myignite.microsoft.com/sessions/55087) (börjar på markör 41:00)

## <a name="next-steps"></a>Nästa steg

Du kan det vara bra att skapa ett bokmärke i den här artikeln som referens, som du gå igenom motsvarande Snabbstart för automatisk etablering. 

Börja genom att fylla i ”ställa in automatisk etablering” Snabbstart som bäst passar önskemål verktyget hantering som vägleder dig genom fasen ”Service configuration”:

- [Konfigurera automatisk-etablering med hjälp av Azure CLI](quick-setup-auto-provision-cli.md)
- [Konfigurera automatisk-etablering med hjälp av Azure Portal](quick-setup-auto-provision.md)
- [Konfigurera automatisk-etablering med hjälp av en Resource Manager-mall](quick-setup-auto-provision-rm.md)

Fortsätt sedan med ett ”automatisk-etablera en simulerad enhet” Snabbstart som passar din enhet attestering mekanism och etablering av tjänst SDK/språkinställning. I den här snabbstarten gå igenom ”enhetsregistrering” och ”enhetsregistrering och konfiguration” faser: 

|  | Simulerade enheten attestering mekanism | Snabbstart SDK/språk |  |
|--|--|--|--|
|  | Trusted Platform Module (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | X.509-certifikat | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




