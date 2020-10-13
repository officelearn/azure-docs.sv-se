---
title: Efter installation och etablering kontrollerar du lyckade och felsöker
description: inkludera fil
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979725"
---
## <a name="verify-successful-setup"></a>Verifiera installationen

Kontrollera status för IoT Edge-tjänsten. Den ska visas som körs.  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

Undersök tjänst loggar.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

Om du precis har installerat IoT Edge runtime kan du se en lista över fel från tiden mellan körningen av **Deploy-IoTEdge** och **Initialize-IoTEdge**. De här felen förväntas när tjänsten försöker starta innan den konfigureras.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

Kör [fel söknings verktyget](../articles/iot-edge/troubleshoot.md#run-the-check-command) för att kontrol lera de vanligaste konfigurations-och nätverks felen.

```powershell
iotedge check
```

Innan du distribuerar din första modul till IoT Edge på enheten, distribueras inte **$edgeHub** system-modulen till enheten. Det innebär att den automatiska kontrollen returnerar ett fel för `Edge Hub can bind to ports on host` anslutnings kontrollen. Felet kan ignoreras om det inte inträffar när du har distribuerat en modul till enheten.

Slutligen visar du en lista med moduler som körs:

```powershell
iotedge list
```

När en ny installation har slutförts är den enda modul som du bör se **edgeAgent**.

## <a name="tips-and-troubleshooting"></a>Tips och felsökning

På resurs begränsade enheter rekommenderar vi starkt att du ställer in miljövariabeln *OptimizeForPerformance* på *falskt* enligt anvisningarna i [fel söknings guiden](../articles/iot-edge/troubleshoot.md).

Om enheten inte kan ansluta till IoT Hub och nätverket har en proxyserver följer du stegen i [konfigurera IoT Edge enheten för att kommunicera via en proxyserver](../articles/iot-edge/how-to-configure-proxy-support.md).

# <a name="linux"></a>[Linux](#tab/linux)

På Linux-enheter måste du ha förhöjd behörighet för att köra `iotedge` kommandon. När du har installerat körningen loggar du ut från datorn och loggar in igen för att uppdatera dina behörigheter automatiskt. Fram till dess använder `sudo` du för att köra kommandon med utökade privilegier.

# <a name="windows"></a>[Windows](#tab/windows)

På Windows-enheter som kör Windows-behållare installerades Moby container Engine som en del av IoT Edge. Moby-motorn har utformats för att köras parallellt med Docker Desktop. Du kan använda `docker` kommandon om du vill interagera direkt med behållarna på enheten. Du måste dock särskilt rikta in Moby-motorn i den Docker-skrivbordet som också är installerad på enheten.

Om du till exempel vill visa alla Docker-avbildningar använder du följande kommando:

```powershell
docker images
```

Om du vill visa alla Moby-avbildningar ändrar du samma kommando med en pekare till Moby-motorn:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Motorns URI visas i utdata från installations skriptet, eller så hittar du den i avsnittet container runtime-inställningar för filen config. yaml.

![moby_runtime-URI i config. yaml](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
