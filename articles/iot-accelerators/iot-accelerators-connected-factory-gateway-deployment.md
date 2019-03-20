---
title: Distribuera din gateway för ansluten fabrik – Azure | Microsoft Docs
description: Så här distribuerar du en gateway på Windows eller Linux för att ansluta till till lösningsaccelerator ansluten fabrik.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 99953b486fbd1daa9800aa850684447465eead9e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182106"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>Distribuera en edge-gateway för ansluten fabrik lösningsaccelerator på Windows eller Linux

Du behöver två programvarukomponenter som du distribuerar en edge-gateway för den *ansluten fabrik* lösningsaccelerator:

- Den *OPC Proxy* upprättar en anslutning till ansluten fabrik. OPC Proxy väntar sedan kommandon och kontrollerar meddelanden från integrerad OPC Browser som körs i portalen för lösningen ansluten fabrik.

- Den *OPC Publisher* ansluter till befintliga lokala OPC UA-servrar och vidarebefordrar telemetrimeddelanden från dem till ansluten fabrik. Du kan ansluta en OPC klassiska enheter med hjälp av den [klassiska OPC-adapter för OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Båda komponenterna är öppen källkod och är tillgängliga som källkod på GitHub och Docker-behållare på DockerHub:

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | [OPC Publisher](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Du behöver inte en offentlig IP-adress eller öppna ingående portar i brandväggen för gateway för antingen komponent. OPC Proxy och OPC Publisher-komponenter kan bara använda utgående port 443.

Stegen i den här artikeln visar hur du distribuerar en edge-gateway med Docker i Windows eller Linux. Gatewayen kan anslutningen till lösningsaccelerator ansluten fabrik. Du kan också använda komponenterna utan ansluten fabrik.

> [!NOTE]
> Båda komponenterna kan användas som moduler i [Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Välj en gateway-enhet

Om du ännu inte har en gateway-enhet, rekommenderar Microsoft att du köper en kommersiella gatewayer från någon av deras partners. En lista över alla gatewayenheter som är kompatibla med lösningen ansluten fabrik finns i [Azure IoT-enhetskatalog](https://catalog.azureiotsolutions.com/?q=opc). Följ instruktionerna som medföljer enheten för att konfigurera gatewayen.

Använd följande instruktioner om du vill konfigurera en befintlig gatewayenhet manuellt.

## <a name="install-and-configure-docker"></a>Installera och konfigurera Docker

Installera [Docker för Windows](https://www.docker.com/docker-windows) på din Windows-baserade gateway-enhet eller använder en pakethanterare för att installera docker på din Linux-baserade gatewayenhet.

Välj en enhet på värddatorn att dela med Docker under Docker för Windows-installationen. Följande skärmbild visar dela den **D** enhet på Windows-system. Dela en enhet ger åtkomst till värdenheten från inuti en docker-behållare:

![Installera Docker för Windows](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Du kan också utföra det här steget när du har installerat docker från den **inställningar** dialogrutan. Högerklicka på den **Docker** ikonen i systemfältet Windows och välj **inställningar**. Om stora Windows-uppdateringar har placerats i systemet, t.ex. Windows Fall Creators update kan sluta dela enheterna och dela dem igen för att uppdatera åtkomstbehörigheter.

Om du använder Linux, krävs ingen ytterligare konfiguration för att ge åtkomst till filsystemet.

På Windows, skapa en mapp på den enhet som du har delat med Docker, skapa en mapp under rotfilsystem i Linux. Den här genomgången refererar till den här mappen som `<SharedFolder>`.

När du refererar till den `<SharedFolder>` i en Docker-kommandot, måste du använda rätt syntax för ditt operativsystem. Här följer två exempel, en för Windows och en för Linux:

- Om du använder mappen `D:\shared` på Windows som din `<SharedFolder>`, kommandosyntaxen Docker är `d:/shared`.

- Om du använder mappen `/shared` på Linux som din `<SharedFolder>`, kommandosyntaxen Docker är `/shared`.

Mer information finns i den [använda volymer](https://docs.docker.com/engine/admin/volumes/volumes/) docker-motorn referens.

## <a name="configure-the-opc-components"></a>Konfigurera OPC-komponenter

Utför följande steg för att förbereda din miljö innan du installerar OPC-komponenter:

1. Du behöver för att slutföra gateway-distribution, den **iothubowner** anslutningssträngen för IoT-hubben i distributionen ansluten fabrik. I den [Azure-portalen](https://portal.azure.com/), navigera till din IoT-hubb i resursgruppen som skapades när du distribuerade lösningen för ansluten fabrik. Klicka på **principer för delad åtkomst** åtkomst till den **iothubowner** anslutningssträng:

    ![Hitta IoT Hub-anslutningssträngen](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

    Kopiera den **anslutningssträng – primär nyckel** värde.

1. Om du vill tillåta kommunikation mellan docker-behållare, måste en användardefinierad nätverksbrygga. Om du vill skapa en nätverksbrygga för dina behållare, kör du följande kommandon i Kommandotolken:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Verifiera den **iot_edge** nätverksbrygga har skapats genom att köra följande kommando:

    ```cmd/sh
    docker network ls
    ```

    Din **iot_edge** Nätverksbrygga ingår i listan över nätverk.

Om du vill köra OPC Publisher, kör du följande kommando i Kommandotolken:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.4 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- Den [OPC Publisher GitHub](https://github.com/Azure/iot-edge-opc-publisher) och [docker kör referens](https://docs.docker.com/engine/reference/run/) ger mer information om:

  - Docker-kommandoradsalternativ som angavs innan behållarens namn (`microsoft/iot-edge-opc-publisher:2.1.4`).
  - Betydelsen av OPC Publisher-kommandoradsparametrar som angetts efter behållarens namn (`microsoft/iot-edge-opc-publisher:2.1.4`).

- Den `<IoTHubOwnerConnectionString>` är den **iothubowner** delad åtkomst princip anslutningssträngen från Azure-portalen. Du har kopierat den här anslutningssträngen i föregående steg. Du behöver bara den här anslutningssträngen för den första körningen av OPC Publisher. På efterföljande körningar, bör du utelämnar den eftersom det utgör en säkerhetsrisk.

- Den `<SharedFolder>` du använder och dess syntax beskrivs i avsnittet [installera och konfigurera Docker](#install-and-configure-docker). OPC Publisher använder den `<SharedFolder>` till:

    - Läsa och skriva till OPC Publisher-konfigurationsfilen.
    - Skriva till loggfilen.
    - Göra båda filerna tillgängliga utanför behållaren.

- OPC Publisher läser konfigurationen från den **publishednodes.json** -fil som är läses in och skrivs till den `<SharedFolder>/docker` mapp. Den här konfigurationsfilen definierar vilka data för OPC UA-noden på en viss OPC UA-server OPC Publisher ska prenumerera på. Den fullständiga syntaxen för den **publishednodes.json** filen beskrivs i den [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) sidan på GitHub. När du lägger till en gateway, placerar du en tom **publishednodes.json** till mappen:

    ```json
    [
    ]
    ```

- När OPC UA-servern meddelar OPC Publisher av en dataändring, skickas det nya värdet till IoT Hub. Beroende på vilka batchbearbetningen inställningar ackumuleras OPC-utgivaren först data innan data skickas till IoT Hub i en batch.

- Docker stöder inte NetBIOS-namnmatchning, endast DNS-namnmatchningen. Om du inte har en DNS-server i nätverket kan använda du den lösning som visas i föregående kommandoradsexempel. Föregående kommandoradsexempel använder den `--add-host` parametern för att lägga till en post i värdfilen behållare. Den här posten gör det möjligt för hostname-sökning för den angivna `<OpcServerHostname>`, lösa till angiven IP-adress `<IpAddressOfOpcServerHostname>`.

- OPC UA använder X.509-certifikat för autentisering och kryptering. Placera OPC Publisher-certifikatet på OPC UA-servern som du ansluter till, så att den litar på OPC Publisher. OPC Publisher-certifikatarkivet finns i den `<SharedFolder>/CertificateStores` mapp. Du kan hitta OPC Publisher-certifikatet i den `trusted/certs` mapp i den `CertificateStores` mapp.

  Hur du konfigurerar OPC UA-servern beror på den enhet som du använder. de här stegen finns vanligtvis dokumenterade i användarhandboken för OPC UA-servern.

Om du vill installera OPC Proxy, kör du följande kommando i Kommandotolken:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Du behöver bara att köra installationen en gång på ett system.

Använd följande kommando för att köra OPC Proxy:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -D /mapped/cs.db
```

OPC Proxy sparar anslutningssträngen under installationen. På efterföljande körningar utelämna anslutningssträngen eftersom det utgör en säkerhetsrisk.

## <a name="enable-your-gateway"></a>Aktivera din gateway

Utför följande steg om du vill aktivera din gateway i ansluten fabrik lösningsaccelerator:

1. När båda komponenterna körs, bläddra till den **ansluta dina egna OPC UA-servern** sidan i portalen för lösningen ansluten fabrik. Den här sidan är endast tillgänglig för administratörer i lösningen. Ange utgivaren slutpunkts-URL (opc.tcp://publisher: 62222) och klicka på **Connect**.

1. Upprätta en förtroenderelation mellan ansluten fabrik-portalen och OPC Publisher. När du ser en certifikatvarning, klickar du på **Fortsätt**. Nu visas ett fel att OPC-utgivaren inte har förtroende för UA webbklienten. Lös felet genom att kopiera den **UA webbklienten** certifikat från den `<SharedFolder>/CertificateStores/rejected/certs` mappen till den `<SharedFolder>/CertificateStores/trusted/certs` mapp på gatewayen. Du behöver inte starta om gatewayen.

Du kan nu ansluta till gatewayen från molnet och du är redo att lägga till OPC UA-servrar i lösningen.

## <a name="add-your-own-opc-ua-servers"></a>Lägg till dina egna OPC UA-servrar

Lägga till dina egna OPC UA-servrar i lösningsaccelerator ansluten fabrik:

1. Bläddra till den **ansluta dina egna OPC UA-servern** sidan i portalen för lösningen ansluten fabrik.

    1. Starta OPC UA-servern som du vill ansluta till. Kontrollera att OPC UA-servern kan nås från OPC Publisher och OPC Proxy som körs i behållaren. Se tidigare kommentarer för namnmatchning.
    1. Ange slutpunkts-URL för OPC UA-servern (`opc.tcp://<host>:<port>`) och klicka på **Connect**.
    1. Installationsprocessen anslutning upprättar en förtroenderelation mellan ansluten fabrik-portal (OPC UA-klient) och OPC UA-servern som du försöker ansluta. Instrumentpanelen för ansluten fabrik som du får en **går inte att verifiera certifikatet för servern som du vill ansluta** varning. När du ser en certifikatvarning, klickar du på **Fortsätt**.
    1. Svårare att installationen är certifikatkonfigureringen av OPC UA-servern som du försöker ansluta till. För PC-baserade OPC UA-servrar, kan du bara få ett varningsmeddelande i instrumentpanelen som du kan godkänna. Läs dokumentationen om OPC UA-servern att leta upp hur den här uppgiften utförs för inbäddade OPC UA-serversystem. Du kan behöva certifikatet i portalen för ansluten fabrik OPC UA-klient för att slutföra den här uppgiften. En administratör kan ladda ned det här certifikatet på den **ansluta dina egna OPC UA-servern** sidan:

        ![Lösningsportal](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. Bläddra träd för OPC UA-noder med OPC UA-servern, högerklicka på OPC-noder som du vill skicka värden till ansluten fabrik och välj **publicera**.

1. Telemetri flödar nu från gateway-enheten. Du kan visa telemetri i den **Fabriksplatser** vy över ansluten fabrik-portalen under **ny fabrik**.

## <a name="next-steps"></a>Nästa steg

Mer information om arkitekturen för ansluten fabrik lösningsaccelerator finns [ansluten fabrik solution accelerator genomgången](iot-accelerators-connected-factory-sample-walkthrough.md).

Lär dig mer om den [referensimplementering för OPC Publisher](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).