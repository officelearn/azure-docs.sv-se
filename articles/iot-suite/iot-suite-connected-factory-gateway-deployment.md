---
title: Distribuera dina anslutna factory gateway - Azure | Microsoft Docs
description: "Så här distribuerar du en gateway på Windows eller Linux för att ansluta till till anslutna fabriken förkonfigurerade lösningen."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.openlocfilehash: 9f11d93be49f7d88012b1274ad61495e38a2e9a9
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>Distribuera en gateway på Windows- eller Linux för anslutna factory förkonfigurerade lösningen

Programvara som krävs för att distribuera en gateway för anslutna factory förkonfigurerade lösningen har två komponenter:

* Den *OPC Proxy* upprättar en anslutning till IoT-hubb. Den *OPC Proxy* väntar sedan på kommando- och meddelanden från integrerade OPC webbläsaren som körs i den anslutna factory lösning portalen.
* Den *OPC Publisher* ansluter till befintliga lokala OPC UA servrar och vidarebefordrar telemetri från dem till IoT-hubben.

Båda komponenterna är öppen källkod och är tillgängliga som källa på GitHub och Docker-behållare:

| GitHub | DockerHub |
| ------ | --------- |
| [OPC Publisher][lnk-publisher-github] | [OPC Publisher][lnk-publisher-docker] |
| [OPC-Proxy][lnk-proxy-github] | [OPC-Proxy][lnk-proxy-docker] |

Ingen offentlig IP-adress eller hål i brandväggen gateway krävs för någon komponent. OPC-Proxy och OPC utgivare använder du endast utgående portarna 443, 5671 och 8883.

Stegen i den här artikeln visar hur du distribuerar en gateway med Docker på antingen [Windows](#windows-deployment) eller [Linux](#linux-deployment). Gatewayen kan ansluta till den anslutna factory förkonfigurerade lösningen.

> [!NOTE]
> Gateway-programvaran som körs i dockerbehållare är [Azure IoT kant].

## <a name="windows-deployment"></a>Windows-distribution

> [!NOTE]
> Om du ännu inte har en gateway-enhet, rekommenderar Microsoft att du köper en kommersiell gateway från någon av våra partners. Finns det [Azure IoT-enhet katalogen] för en lista med gatewayenheter som är kompatibla med anslutna factory-lösning. Följ instruktionerna som medföljer enheten för att konfigurera gatewayen. Du kan också Använd följande instruktioner för att manuellt konfigurera en av dina befintliga gatewayer.

### <a name="install-docker"></a>Installera Docker

Installera [Docker för Windows] på din Windows-baserade gateway-enhet. Välj en enhet på värddatorn delar med Docker under Windows Docker-installationen. Följande skärmbild visar delning enhet D på Windows-systemet:

![Installera Docker][img-install-docker]

Skapa en mapp med namnet **docker** i roten av den delade enheten.
Du kan också utföra det här steget när du har installerat docker från den **inställningar** menyn.

### <a name="configure-the-gateway"></a>Konfigurera gatewayen

1. Du behöver den **iothubowner** anslutningssträngen för dina Azure IoT Suite anslutna factory distribution gateway-distributionen. I den [Azure-portalen], navigera till din IoT-hubb i resursgruppen som skapas när du har distribuerat anslutna factory-lösning. Klicka på **principer för delad åtkomst** att få åtkomst till den **iothubowner** anslutningssträngen:

    ![Hitta anslutningssträngen IoT-hubb][img-hub-connection]

    Kopiera den **anslutningssträngen--primärnyckel** värde.

1. Konfigurera gateway för din IoT-hubb genom att köra två gateway-moduler **när** från en kommandotolk med:

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;**  är namnet som ska ge OPC UA Publisher i formatet **utgivare.&lt; fullständigt kvalificerade domännamnet&gt;**. Om exempelvis factory nätverket kallas **myfactorynetwork.com**, **ApplicationName** värdet är **publisher.myfactorynetwork.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;**  är den **iothubowner** anslutningssträngen som du kopierade i föregående steg. Den här anslutningssträngen används endast i det här steget, behöver du inte den i följande steg:

    Du använder den mappade D:\\docker-mappen (den `-v` argumentet) senare för att bevara de två X.509-certifikat som använder gateway-moduler.

### <a name="run-the-gateway"></a>Kör gatewayen

1. Starta om gatewayen med följande kommandon:

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Av säkerhetsskäl måste de två X.509-certifikat som är kvar i D:\\docker mappen innehåller den privata nyckeln. Begränsa åtkomsten till den här mappen på autentiseringsuppgifterna (vanligtvis **administratörer**) används för att köra Docker-behållaren. Högerklicka på D:\\docker-mappen, Välj **egenskaper**, sedan **säkerhet**, och sedan **redigera**. Ge **administratörer** fullständig kontroll och ta bort alla andra:

    ![Bevilja behörighet till Docker-resurs][img-docker-share]

1. Kontrollera nätverksanslutningen. Ange kommandot från en kommandotolk `ping publisher.<your fully qualified domain name>` pinga din gateway. Lägg till IP-adressen och namnet på din gateway i värdfilen på din gateway om målet kan inte nås. Värdfilen finns i den **Windows\\System32\\drivrutiner\\etc** mapp.

1. Därefter försöka ansluta till utgivaren med hjälp av en lokal OPC UA-klient som körs på en gateway. OPC UA slutpunkten URL: en är `opc.tcp://publisher.<your fully qualified domain name>:62222`. Om du inte har en OPC UA-klient, hämtar och använder en [öppen källkod OPC UA klienten].

1. När du har slutfört dessa lokala tester, bläddra till den **ansluta din egen OPC UA Server** sidan på anslutna factory lösning portalen. Ange utgivare slutpunkts-URL (`tcp://publisher.<your fully qualified domain name>:62222`) och klicka på **Anslut**. Du får en certifikatvarning om och klicka sedan på **Fortsätt.** Nästa du får ett felmeddelande som webbklienten UA inte litar på utgivaren. Lös problemet genom att kopiera den **UA webbklienten** certifikat från den **D:\\docker\\avvisade certifikat\\certifikat** mappen till den **D:\\docker\\UA program\\certifikat** mapp på gateway. Du behöver inte starta om gatewayen. Upprepa det här steget. Du kan ansluta till gatewayen nu från molnet och du är redo att lägga till OPC UA servrar i lösningen.

### <a name="add-your-opc-ua-servers"></a>Lägga till OPC UA-servrar

1. Bläddra till den **ansluta din egen OPC UA Server** sidan på anslutna factory lösning portalen. Följ samma steg som i föregående avsnitt för att upprätta förtroende mellan anslutna factory-portalen och OPC UA-servern. Det här steget upprättar ett ömsesidigt förtroende av certifikat från anslutna factory-portalen och OPC UA-servern och skapar en anslutning.

1. Bläddra i OPC UA noder trädet serverns OPC UA, högerklicka på OPC-noder och välj **publicera**. För att publicera om du vill att fungera måste OPC UA-server och utgivaren vara i samma nätverk. Med andra ord, om det fullständigt kvalificerade domännamnet utgivarens **publisher.mydomain.com** fullständigt kvalificerade domännamnet för OPC UA-server måste vara, till exempel **myopcuaserver.mydomain.com**. Om din konfiguration skiljer sig, du kan manuellt lägga till noder publishesnodes.json-fil som finns i den **D:\\docker** mapp. Filen publishesnodes.json genereras automatiskt första lyckade publicera för en OPC-nod.

1. Telemetri nu flödar från gateway-enheten. Du kan visa telemetri i den **Factory platser** visa anslutna factory portalens under **nya Factory**.

## <a name="linux-deployment"></a>Linux-distribution

> [!NOTE]
> Om du ännu inte har en gateway-enhet, rekommenderar Microsoft att du köper en kommersiell gateway från någon av våra partners. Finns det [Azure IoT-enhet katalogen] för en lista med gatewayenheter som är kompatibla med anslutna factory-lösning. Följ instruktionerna som medföljer enheten för att konfigurera gatewayen. Du kan också Använd följande instruktioner för att manuellt konfigurera en av dina befintliga gatewayer.

[Installera Docker] på Linux-gateway-enheten.

### <a name="configure-the-gateway"></a>Konfigurera gatewayen

1. Du behöver den **iothubowner** anslutningssträngen för dina Azure IoT Suite anslutna factory distribution gateway-distributionen. I den [Azure-portalen], navigera till din IoT-hubb i resursgruppen som skapas när du har distribuerat anslutna factory-lösning. Klicka på **principer för delad åtkomst** att få åtkomst till den **iothubowner** anslutningssträngen:

    ![Hitta anslutningssträngen IoT-hubb][img-hub-connection]

    Kopiera den **anslutningssträngen--primärnyckel** värde.

1. Konfigurera gateway för din IoT-hubb genom att köra två gateway-moduler **när** från ett gränssnitt med:

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;**  är namnet på programmet OPC UA gatewayen skapar i formatet **utgivare.&lt; fullständigt kvalificerade domännamnet&gt;**. Till exempel **publisher.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;**  är den **iothubowner** anslutningssträngen som du kopierade i föregående steg. Den här anslutningssträngen används endast i det här steget, behöver du inte den i följande steg:

    Du använder den **/ delade** mappen (den `-v` argumentet) senare för att bevara de två X.509-certifikat som använder gateway-moduler.

### <a name="run-the-gateway"></a>Kör gatewayen

1. Starta om gatewayen med följande kommandon:

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 –-rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Av säkerhetsskäl måste de två X.509-certifikat som är kvar i den **/ delade** mappen innehåller den privata nyckeln. Begränsa åtkomsten till den här mappen på de autentiseringsuppgifter som används för att köra Docker-behållaren. Ange behörigheter för **rot** endast använda den `chmod` shell-kommandot på mappen.

1. Kontrollera nätverksanslutningen. Ange kommandot från en shell `ping publisher.<your fully qualified domain name>` pinga din gateway. Lägg till IP-adressen och namnet på din gateway till hosts-filen på din gateway om målet kan inte nås. Värdfilen finns i den **/etc** mapp.

1. Därefter försöka ansluta till utgivaren med hjälp av en lokal OPC UA-klient som körs på en gateway. OPC UA slutpunkten URL: en är `opc.tcp://publisher.<your fully qualified domain name>:62222`. Om du inte har en OPC UA-klient, hämtar och använder en [öppen källkod OPC UA klienten].

1. När du har slutfört dessa lokala tester, bläddra till den **ansluta din egen OPC UA Server** sidan på anslutna factory lösning portalen. Ange utgivare slutpunkts-URL (`tcp://publisher.<your fully qualified domain name>:62222`) och klicka på **Anslut**. Du får en certifikatvarning om och klicka sedan på **Fortsätt.** Nästa du får ett felmeddelande som webbklienten UA inte litar på utgivaren. Lös det här felet genom att kopiera den **UA webbklienten** certifikat från den **/delad/Avvisad certifikat/certifikat** mappen till den **/shared/UA program/certifikat** mapp på gateway. Du behöver inte starta om gatewayen. Upprepa det här steget. Du kan ansluta till gatewayen nu från molnet och du är redo att lägga till OPC UA servrar i lösningen.

### <a name="add-your-opc-ua-servers"></a>Lägga till OPC UA-servrar

1. Bläddra till den **ansluta din egen OPC UA Server** sidan på anslutna factory lösning portalen. Följ samma steg som i föregående avsnitt för att upprätta förtroende mellan anslutna factory-portalen och OPC UA-servern. Det här steget upprättar ett ömsesidigt förtroende av certifikat från anslutna factory-portalen och OPC UA-servern och skapar en anslutning.

1. Bläddra i OPC UA noder trädet serverns OPC UA, högerklicka på OPC-noder och välj **publicera**. För att publicera om du vill att fungera måste OPC UA-server och utgivaren vara i samma nätverk. Med andra ord, om det fullständigt kvalificerade domännamnet utgivarens **publisher.mydomain.com** fullständigt kvalificerade domännamnet för OPC UA-server måste vara, till exempel **myopcuaserver.mydomain.com**. Om din konfiguration skiljer sig, du kan manuellt lägga till noder publishesnodes.json-fil som finns i den **/ delade** mapp. Publishesnodes.json genereras automatiskt första lyckade publicera för en OPC-nod.

1. Telemetri nu flödar från gateway-enheten. Du kan visa telemetri i den **Factory platser** visa anslutna factory portalens under **nya Factory**.

## <a name="next-steps"></a>Nästa steg

Mer information om arkitekturen för den anslutna factory förkonfigurerade lösningen finns [anslutna factory förkonfigurerade lösningen genomgången][lnk-walkthrough].

Lär dig mer om den [OPC Publisher referensimplementering](iot-suite-connected-factory-publisher.md).

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker för Windows]: https://www.docker.com/docker-windows
[Azure IoT-enhet katalogen]: https://catalog.azureiotsuite.com/?q=opc
[Azure-portalen]: http://portal.azure.com/
[öppen källkod OPC UA klienten]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Installera Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Azure IoT kant]: https://github.com/Azure/iot-edge

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy