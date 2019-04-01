---
title: Säker kommunikation för OPC-klienten och OPC PLC med hjälp av Azure IoT OPC UA-certifikathantering | Microsoft Docs
description: 'Skydda kommunikation för OPC-klienten och OPC PLC genom att registrera sina certifikat med hjälp av OPC Vault CA: N.'
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c437f6db21956d1be5e4f6d3512f325f37ca7308
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759670"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>Säker kommunikation för OPC-klienten och OPC PLC

Azure IoT OPC UA certifikathantering, även kallade OPC-valv är en micro tjänst som kan konfigurera registreras, och hantera certifikatlivscykeln för OPC UA-servern och klienten program i molnet. Den här artikeln visar hur du skyddar kommunikation för OPC-klienten och OPC PLC genom att registrera sina certifikat med hjälp av OPC Vault CA.

I följande inställningar OPC-klienten för att testa anslutningen till OPC-PLC. Som standard är anslutningen inte möjligt eftersom båda komponenterna inte har etablerats med rätt certifikat. Om en OPC UA-komponenten inte har etablerats med ett certifikat, skulle den generera ett självsignerat certifikat vid start. Certifikatet kan dock signerade av en certifikatutfärdare (CA) och installeras i OPC UA-komponenten. När det är klart för OPC-klienten och OPC PLC aktiveras anslutningen. Arbetsflödet nedan beskriver processen. Vissa grundläggande information om OPC UA-säkerhet finns i [det här dokumentet](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf) white paper om. Fullständig information finns i OPC UA-specifikationen.

Testbädd för: Följande miljö konfigureras för att testa.

OPC Vault skript:
- Säker kommunikation för OPC-klienten och OPC PLC genom att registrera sina certifikat med hjälp av OPC Vault CA: N.

> [!NOTE]
> Mer information finns i GitHub [databasen](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-self-signed-certificate-on-startup"></a>Generera ett självsignerat certifikat vid start

**Förberedelse**

- Kontrollera att miljövariablerna `$env:_PLC_OPT` och `$env:_CLIENT_OPT` är odefinierade, till exempel `$env:_PLC_OPT=""` i din PowerShell.

- Ange miljövariabeln `$env:_OPCVAULTID` till en sträng som hjälper dig att hitta dina data igen i OPC-valvet. Endast alfanumeriska tecken tillåts. I vårt exempel användes ”123456” som värde för den här variabeln.

- Se till att det finns inga volymer docker `opcclient` eller `opcplc`. Kontrollera med `docker volume ls` och ta bort dem med `docker volume rm <volumename>`. Du kan behöva ta bort också behållare med `docker rm <containerid>` om volymerna som fortfarande används av en behållare.

**Snabbstart**

Kör följande PowerShell-kommando i roten för lagringsplatsen:

```
docker-compose -f connecttest.yml up
```

**Verifiering**

Kontrollera att det inte finns några certifikat installeras vid första start i loggen. Här loggutdata för OPC PLC (liknande visas för OPC-klienten):...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
Om du ser certifikat som har rapporterats, följer du förberedelsestegen av ovan och ta bort volymerna som docker.

Kontrollera att anslutningen till OPC-PLC misslyckades. Du bör se följande utdata i OPC-klienten logga utdata:

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
Orsaken till felet är att certifikatet inte är betrodd. Detta innebär att `opc-client` försökte ansluta till `opc-plc` och får svar tillbaka, vilket betyder att `opc-plc` inte har förtroende `opc-client`, ett resultat av `opc-plc` det gick inte att verifiera certifikatet som `opc-client` har angetts. Det här är ett självsignerat certifikat utan någon ytterligare Certifikatkonfiguration på `opc-plc`, och därmed anslutningen misslyckades.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>Registrera och installera certifikat i OPC UA-komponenter

**Förberedelse**
1. Titta på loggutdata i steg 1 och hämta ”CreateSigningRequest information” för OPC-PLC och OPC-klienten. Här visas bara utdata för OPC PLC:

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. Gå till den [OPC Vault webbplats](https://opcvault.azurewebsites.net/).

1. Välj `Register New`

1. Ange OPC PLC information från utdata log `CreateSigningRequest information` området i inmatningsfält i den `Register New OPC UA Application` väljer `Server` som ApplicationType.

1. Välj `Register`.

1. På nästa sida `Request New Certificate for OPC UA Application` Välj `Request new Certificate with Signing Request`.

1. På nästa sida `Generate a new Certificate with a Signing Request` klistra in den `CSR (base64 encoded)` sträng från loggutdata till den `CreateRequest` indatafält. Se till att du kopierar fullständig sträng.

1. Välj `Generate New Certificate`.

1. Du nu flyttar framåt till `View Certificate Request Details`. På den här sidan kan du ladda ned all nödvändig information för att etablera certifikatarkiv för `opc-plc`.

1. På den här sidan:  
    - Välj `Certificate` i `Download as Base64` och kopiera textsträngen som presenteras i den `EncodedBase64` fältet och lagra den för senare användning. Vi refererar till den som `<applicationcertbase64-string>` vid ett senare tillfälle. Välj `Back`.

    - Välj `Issuer` i `Download as Base64` och kopiera textsträngen som presenteras i den `EncodedBase64` fältet och lagra den för senare användning. Vi refererar till den som `<addissuercertbase64-string>` vid ett senare tillfälle. Välj `Back`.

    - Välj `Crl` i `Download as Base64` och kopiera textsträngen som presenteras i den `EncodedBase64` fältet och lagra den för senare användning. Vi refererar till den som `<updatecrlbase64-string>` vid ett senare tillfälle. Välj `Back`.

1. Ange nu i din PowerShell en variabel med namnet `$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Ersätt strängarna som skickats in som alternativ värden Base64 strängar som du hämtade från webbplatsen.

Upprepa fullständig processen från och med `Register New` (steg 3 ovan) för OPC-klienten. Det finns endast följande skillnader som du behöver känna till:

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

**Verifiering** kontrollerar du att de båda komponenterna har nu registrerat programcertifikat. Kontrollera loggutdata för följande utdata:

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
Programcertifikatet finns det och signerad av en Certifikatutfärdare.

Kontrollera att det inte finns nu certifikat installerade i loggen. Nedan visas loggutdata för OPC PLC och OPC-klienten har en liknande utdata.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
Program-certifikatets utfärdare är CA: N `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` och OPC-PLC litar också alla certifikat som signerats av Certifikatutfärdaren.


Kontrollera att anslutningen till OPC-PLC har skapats och OPC-klienten kan läsa data från OPC PLC. Du bör se följande utdata i OPC-klienten logga utdata:
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
Om dessa utdata visas sedan OPC-PLC är nu betrodda OPC-klienten och vice versa, eftersom båda har nu certifikat som signerats av en Certifikatutfärdare och båda betrodda certifikat som var signerad av denna Certifikatutfärdare.

> [!NOTE] 
> Även om vi visade första två verifieringssteg endast för OPC PLC, måste de också verifieras för OPC-klienten.


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar OPC-valv till ett befintligt projekt, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Distribuera OPC-Twin till ett befintligt projekt](howto-opc-twin-deploy-existing.md)