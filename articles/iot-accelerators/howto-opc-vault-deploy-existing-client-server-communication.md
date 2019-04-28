---
title: Skydda OPC UA-klienten och OPC UA-serverprogram med hjälp av OPC-valv – Azure | Microsoft Docs
description: Skydda OPC UA-klient och OPC UA serverprogram med ett nytt nyckelpar och certifikat med hjälp av OPC-valvet.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450672"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Skydda OPC UA-klienten och OPC UA-serverprogram 
OPC-valv är en mikrotjänst som kan konfigurera, registrera och hantera livscykeln för användarcertifikatet för OPC UA-servern och klientprogram i molnet. Den här artikeln visar hur du skyddar en OPC UA-klient och en OPC UA serverprogram med ett nytt nyckelpar och certifikat med hjälp av OPC-valvet.

I följande inställningar OPC-klienten testar anslutningen till OPC-PLC. Som standard är anslutningen inte möjligt eftersom båda komponenterna ännu inte har tillhandahållits med rätt certifikat. I det här arbetsflödet, vi inte använder självsignerade certifikat för OPC UA-komponenter och signerar dem via OPC-valvet. Se föregående [Testbädd för](howto-opc-vault-deploy-existing-client-plc-communication.md). I stället etablerar den här Testbädd för komponenter med ett nytt certifikat samt med en ny privat nyckel som genereras både av OPC-valvet. Vissa grundläggande information om OPC UA-säkerhet finns i den här [White Paper](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). Fullständig information finns i OPC UA-specifikationen.

Testbädd för: Följande miljö konfigureras för att testa.

OPC Vault skript:
- Skydda OPC UA-klient och OPC UA serverprogram med ett nytt nyckelpar och certifikat med hjälp av OPC-valvet.

> [!NOTE]
> Mer information finns i GitHub [databasen](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-new-certificate-and-private-key"></a>Generera ett nytt certifikat och privat nyckel 
**Förberedelse**
- Kontrollera att miljövariablerna `$env:_PLC_OPT` och `$env:_CLIENT_OPT` är odefinierad. Till exempel `$env:_PLC_OPT=""` i din PowerShell
- Ange miljövariabeln `$env:_OPCVAULTID` till en sträng som hjälper dig att hitta dina data igen i OPC-valvet. Vi rekommenderar att ställa in den 6 siffror. I vårt exempel användes ”123456” som värde för variabeln.
- Se till att ingen docker-volym `opcclient` eller `opcplc`. Kontrollera med `docker volume ls` och ta bort dem med `docker volume rm <volumename>`. Du kan behöva ta bort också behållare med `docker rm <containerid>` om volymerna som fortfarande används av en behållare.

**Snabbstart**
1. Gå till den [OPC Vault webbplats](https://opcvault.azurewebsites.net/)

1. Välj `Register New`

1. Ange PLC OPC-information som den som visas i testbädden tidigare loggutdata `CreateSigningRequest information` området i inmatningsfält i den `Register New OPC UA Application` väljer `Server` som ApplicationType.

1. Välj `Register`

1. På nästa sida `Request New Certificate for OPC UA Application` Välj `Request new KeyPair and Certificate`

1. På nästa sida `Generate a new Certificate with a Signing Request` klistra in den `CSR (base64 encoded)` sträng från loggutdata till den `CreateRequest` indatafält. Se till att du kopierar fullständig sträng.

1. På nästa sida `Request New Certificate for OPC UA Application` Välj `Request new Certificate with Signing Request`

1. På nästa sida `Generate a new KeyPair and for an OPC UA Application` ange `CN=OpcPlc` som SubjectName, `opcplc-<_OPCVAULTID>` (Ersätt `<_OPCVAULTID>` med dina) som domännamn, Välj `PEM` som PrivateKeyFormat och ange ett lösenord (vi senare refererar till den som `<certpassword-string>`)

1. Välj `Generate New KeyPair`

1. Du nu flyttar framåt till `View Certificate Request Details`. På den här sidan kan du ladda ned all nödvändig information för att etablera certifikatarkiv för `opc-plc`.

1. På den här sidan:  
    - Välj `Certificate` i `Download as Base64` och kopiera textsträngen som presenteras i den `EncodedBase64` fältet och lagra den för senare användning. Vi refererar till den som `<applicationcertbase64-string>` vid ett senare tillfälle. Välj `Back`.
    - Välj `PrivateKey` i `Download as Base64` och kopiera textsträngen som presenteras i den `EncodedBase64` fältet och lagra den för senare användning. Vi refererar till den som `<privatekeybase64-string>` vid ett senare tillfälle. Välj `Back`.
    - Välj `Issuer` i `Download as Base64` och kopiera textsträngen som presenteras i den `EncodedBase64` fältet och lagra den för senare användning. Vi refererar till den som `<addissuercertbase64-string>` vid ett senare tillfälle. Välj `Back`.
    - Välj `Crl` i `Download as Base64` och kopiera textsträngen som presenteras i den `EncodedBase64` fältet och lagra den för senare användning. Vi refererar till den som `<updatecrlbase64-string>` vid ett senare tillfälle. Välj `Back`.

1. Ange nu i din PowerShell en variabel med namnet `$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Ersätt strängarna som skickats in som alternativ värden Base64 strängar som du hämtade från webbplatsen.  

1. Upprepa fullständig processen från och med `Register New` för OPC-klienten. Det finns endast följande skillnader som du behöver känna till:
    - Använd loggutdata från den `opcclient`.
    - Välj `Client` som ApplicationType under registreringen.
    - Använd `$env:_CLIENT_OPT` som namnet på PowerShell-variabel.

    > [!NOTE] 
    > När du arbetar med det här scenariot kan du kanske har erkänt som det `<addissuercertbase64-string>` och `<updatecrlbase64-string>` värden är identiska för `opcplc` och `opcclient`. Detta gäller för våra användningsfall och kan spara tid när du gör stegen.

**Snabbstart**

Kör följande PowerShell-kommando i roten för lagringsplatsen:

```
docker-compose -f connecttest.yml up
```

**Verifiering**

Kontrollera att de båda komponenterna inte har ett befintligt certifikat för programmet. Kontrollera loggutdata. Nedan visas resultatet av OPC PLC och OPC-klienten har en liknande loggutdata.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
Om det finns ett programcertifikat, ta bort volymerna som docker enligt beskrivningen i steg för förberedelse.

Kontrollera att OPC Vault CA-certifikatet har installerats i certifikatarkivet utfärdare samt som i certifikatarkivet Betrodda peer i loggen. Nedan visas loggutdata för OPC PLC och OPC-klienten har en liknande loggutdata. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
OPC-PLC nu lita på alla OPC UA-klienter med certifikat som signerats av OPC-valvet.

Kontrollera i loggen att format för privat nyckel har utsetts till PEM och att det nya certifikatet för programmet är installerat. Nedan visas loggutdata för OPC PLC och OPC-klienten har en liknande loggutdata. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

Programcertifikatet och den privata nyckeln är nu installerat i certifikatarkivet för program och används av OPC UA-programmet.

Kontrollera att anslutningen mellan OPC-klienten och OPC PLC kan upprättas har och OPC-klienten kan läsa data från OPC PLC. Du bör se följande utdata i loggutdata för OPC-klienten:
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
Om du ser dessa utdata kan sedan OPC-PLC nu betrodda OPC-klienten och tvärtom, eftersom båda har nu de certifikat som signerats av en Certifikatutfärdare och båda ha förtroende för certifikat som har signerats av Certifikatutfärdaren.

### <a name="a-testbed-for-opc-publisher"></a>En Testbädd för för OPC Publisher ###

**Snabbstart**

Kör följande PowerShell-kommando i roten för lagringsplatsen:
```
docker-compose -f testbed.yml up
```

**Verifiering**
- Kontrollera att data skickas till IOT Hub som du har konfigurerat genom att ange `_HUB_CS` med [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) eller [iothub-explorer](https://github.com/Azure/iothub-explorer).
- OPC testklienten kommer att använda IoTHub direkt metodanrop och OPC-metodanrop för att konfigurera OPC Publisher för att publicera/ta bort noder från testservern OPC.
- Titta på resultatet för felmeddelanden.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC-valv till ett befintligt projekt, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Distribuera OPC-Twin till ett befintligt projekt](howto-opc-twin-deploy-existing.md)