---
title: Förstå Azure IoT-enhetsetableringstjänst MQTT-stöd | Microsoft-dokument
description: Utvecklarguide – stöd för enheter som ansluter till DPS-enhetstjänsten (Azure IoT Device Provisioning Service) med hjälp av MQTT-protokollet.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: ea6ece7e34ddb9c25f9f8349239ab3a1c3405abf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973381"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Kommunicera med din DPS med MQTT-protokollet

DPS gör det möjligt för enheter att kommunicera med DPS-enhetens slutpunkt med hjälp av:

* [MQTT v3.1.1](https://mqtt.org/) på port 8883
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) över WebSocket på port 443.

DPS är inte en fullfjädrad MQTT-mäklare och stöder inte alla beteenden som anges i MQTT v3.1.1-standarden. I den här artikeln beskrivs hur enheter kan använda MQTT-beteenden som stöds för att kommunicera med DPS.

All enhetskommunikation med DPS måste säkras med TLS/SSL. Därför stöder DPS inte osäkra anslutningar via port 1883.

 > [!NOTE] 
 > DPS stöder för närvarande inte enheter som använder [TPM-attestationmekanismen](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) över MQTT-protokollet.

## <a name="connecting-to-dps"></a>Ansluta till DPS

En enhet kan använda MQTT-protokollet för att ansluta till ett DPS med något av följande alternativ.

* Bibliotek i [Azure IoT-etablering SDK: er](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks).
* MQTT-protokollet direkt.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Använda MQTT-protokollet direkt (som en enhet)

Om en enhet inte kan använda enhetens SDK:er kan den fortfarande ansluta till de offentliga enhetsslutpunkterna med MQTT-protokollet på port 8883. I **CONNECT-paketet** bör enheten använda följande värden:

* Använd **registrationId**för fältet **ClientId** .

* För fältet **Användarnamn** `{idScope}/registrations/{registration_id}/api-version=2019-03-31`använder `{idScope}` du , där är [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) för DPS.

* Använd en SAS-token för fältet **Lösenord.** Formatet för SAS-token är detsamma som för både HTTPS- och AMQP-protokollen:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`ResourceURI ska vara i `{idScope}/registrations/{registration_id}`formatet . Principnamnet ska `registration`vara .

  > [!NOTE]
  > Om du använder X.509-certifikatautentisering krävs inte SAS-tokenlösenord.

  Mer information om hur du genererar SAS-token finns i avsnittet säkerhetstoken i [Kontrollåtkomst till DPS](how-to-control-access.md#security-tokens).

Följande är en lista över DPS implementeringsspecifika beteenden:

 * DPS stöder inte funktionaliteten i **CleanSession-flaggan** som är inställd på **0**.

 * När en enhetsapp prenumererar på ett ämne med **QoS 2**beviljar DPS maximal QoS-nivå 1 i **SUBACK-paketet.** Därefter levererar DPS meddelanden till enheten med QoS 1.

## <a name="tlsssl-configuration"></a>TLS/SSL-konfiguration

Om du vill använda MQTT-protokollet direkt *måste* klienten ansluta via TLS 1.2. Försök att hoppa över det här steget misslyckas med anslutningsfel.


## <a name="registering-a-device"></a>Registrera en enhet

Om du vill registrera en enhet via `$dps/registrations/res/#` DPS bör en enhet prenumerera med hjälp av **ämnesfilter**. Jokertecknet på flera `#` nivåer i ämnesfiltret används bara för att enheten ska kunna ta emot ytterligare egenskaper i ämnesnamnet. DPS tillåter inte användning `#` av `?` eller jokertecken för filtrering av underämnen. Eftersom DPS inte är en allmän pub-sub meddelandemäklare, stöder den bara de dokumenterade ämnesnamnen och ämnesfiltren.

Enheten ska publicera ett registermeddelande `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` till DPS med **ämnesnamn**. Nyttolasten ska innehålla device [registration-objektet](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) i JSON-format.
I ett lyckat scenario får enheten `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` ett svar på ämnesnamnet där x är återförsöksvärdet på några sekunder. Nyttolasten för svaret kommer att innehålla [RegistrationOperationStatus-objektet](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) i JSON-format.

## <a name="polling-for-registration-operation-status"></a>Avsökning för status för registreringsåtgärd

Enheten måste regelbundet avsöka tjänsten för att få resultatet av enhetens registrering. Förutsatt att enheten redan har prenumererat på `$dps/registrations/res/#` ämnet som anges ovan, kan den `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` publicera ett get-statusmeddelande till ämnesnamnet. Åtgärds-ID:et i det här meddelandet ska vara det värde som tas emot i responsemeddelandet RegistrationOperationStatus i föregående steg. I det lyckade fallet kommer `$dps/registrations/res/200/?$rid={request_id}` tjänsten att svara på ämnet. Nyttolasten för svaret kommer att innehålla registrationOperationStatus-objektet. Enheten bör fortsätta att avsöka tjänsten om svarskoden är 202 efter en fördröjning som är lika med återförsök-efter-perioden. Enhetsregistreringen lyckas om tjänsten returnerar en 200-statuskod.

## <a name="connecting-over-websocket"></a>Ansluta via Websocket
När du ansluter via Websocket anger du `mqtt`underprotokol som . Följ [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Nästa steg

Mer information om MQTT-protokollet finns i [MQTT-dokumentationen](https://mqtt.org/documentation).

Mer information om hur DU kan utforska dps funktioner ytterligare finns i:

* [Om IoT DPS](about-iot-dps.md)
