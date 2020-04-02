---
title: Skriv createOptions för moduler - Azure IoT Edge | Microsoft-dokument
description: Så här använder du createOptions i distributionsmanifestet för att konfigurera moduler vid körning
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550349"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Konfigurera alternativ för att skapa behållare för IoT Edge-moduler

Parametern **createOptions** i distributionsmanifestet gör att du kan konfigurera modulbehållarna vid körning. Den här parametern utökar kontrollen över modulerna och möjliggör uppgifter som att tillåta eller begränsa modulens åtkomst till värdenhetens resurser eller konfigurera nätverk.

IoT Edge-moduler implementeras som Docker-kompatibla behållare på din IoT Edge-enhet. Docker erbjuder många alternativ för att skapa behållare, och dessa alternativ gäller även för IoT Edge-moduler. Mer information finns i [Skapa alternativ för Docker-behållare](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Alternativ för formatskapande

IoT Edge-distributionsmanipuleringsmanipuleringsmanipuleringsmanipuleringsmanipuleringsalternativen är formaterade som JSON. Ta till exempel de skapa alternativ som automatiskt ingår för varje edgeHub-modul:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

I det här edgeHub-exemplet används parametern **HostConfig.PortBindings** för att mappa exponerade portar på behållaren till en port på värdenheten.

Om du använder Azure IoT Tools-tilläggen för Visual Studio eller Visual Studio-kod kan du skriva skapa-alternativen i JSON-format i filen **deployment.template.json.** När du sedan använder tillägget för att skapa IoT Edge-lösningen eller genererar distributionsmanifestet, kommer det att stringifiera JSON för dig i det format som IoT Edge-körningen förväntar sig. Ett exempel:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Ett tips för att skriva `docker inspect` skapa alternativ är att använda kommandot. Som en del av din utvecklingsprocess `docker run <container name>`kör du modulen lokalt med . När du har modulen fungerar som `docker inspect <container name>`du vill ha den, kör . Det här kommandot matar ut modulinformationen i JSON-format. Leta reda på de parametrar som du har konfigurerat och kopiera JSON. Ett exempel:

[![Resultat av docker](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) inspektera edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Vanliga scenarier

Alternativ för att skapa behållare möjliggör många scenarier, men här är några som oftast visas när du skapar IoT Edge-lösningar:

* [Ge moduler åtkomst till värdlagring](how-to-access-host-storage-from-module.md)
* [Mappa värdport till modulport](#map-host-port-to-module-port)
* [Begränsa modulminnes- och CPU-användning](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Mappa värdport till modulport

Om din modul behöver kommunicera med en tjänst utanför IoT Edge-lösningen och inte använder meddelanderoutning för att göra det, måste du mappa en värdport till en modulport.

>[!TIP]
>Den här portmappningen krävs inte för modul-till-modul-kommunikation på samma enhet. Om modul A behöver fråga ett API som finns på modul B kan den göra det utan portmappning. Modul B måste exponera en port i sin `EXPOSE 8080`dockerfile, till exempel: . Sedan kan modul A fråga API:et med `http://ModuleB:8080/api`hjälp av modul B:s namn, till exempel: .

Kontrollera först att en port inuti modulen är exponerad för att lyssna efter anslutningar. Du kan göra detta med hjälp av en [EXPOSE-instruktion](https://docs.docker.com/engine/reference/builder/#expose) i dockerfilen. Till exempel `EXPOSE 8080`. Exponera instruktionen standard till TCP-protokollet om det inte anges, eller så kan du ange UDP.

Använd sedan inställningen **PortBindings** i gruppen **HostConfig** i [Docker-behållarens alternativ](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) för att mappa den exponerade porten i modulen till en port på värdenheten. Om du till exempel exponerade port 8080 inuti modulen och vill mappa den till port 80 av värdenheten, skulle skapa-alternativen i filen template.json se ut som följande exempel:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

När strängen har stringensats för distributionsmanifestet skulle samma konfiguration se ut som följande exempel:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Begränsa modulminnes- och CPU-användning

Du kan deklarera hur mycket av värdresurserna en modul kan använda. Den här kontrollen är användbar för att säkerställa att en modul inte kan använda för mycket minne eller CPU-användning och förhindra att andra processer körs på enheten. Du kan hantera dessa inställningar med [Docker-behållaren skapa alternativ](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) i **HostConfig** gruppen, inklusive:

* **Minne**: Minnesgräns i byte. Till exempel 268435456 byte = 256 MB.
* **MemorySwap**: Total minnesgräns (minne + swap). Till exempel 536870912 byte = 512 MB
* **CpuPeriod**: Längden på en CPU-period i mikroseconds. Standardvärdet är 100000, så till exempel begränsar värdet 25000 en behållare till 25 % av PROCESSOR-resurserna.

I formatet template.json skulle dessa värden se ut som följande exempel:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

När de här värdena har stringensats för det slutliga distributionsmanifestet ser de ut som följande exempel:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Nästa steg

Fler exempel på hur du skapar alternativ finns i följande IoT Edge-exempel:

* [Anpassad vision och Azure IoT Edge på en Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT Edge blob storage sample](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
