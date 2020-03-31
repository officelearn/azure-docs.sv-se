---
title: Felsöka – Azure Event Grid IoT Edge | Microsoft-dokument
description: Felsökning i händelserutnät på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100163"
---
# <a name="common-issues"></a>Vanliga problem

Om du får problem med att använda Azure Event Grid på IoT Edge i din miljö använder du den här artikeln som en guide för felsökning och lösning.

## <a name="view-event-grid-module-logs"></a>Visa modulloggar för händelserutnät

Om du vill felsöka kan du behöva komma åt moduleloggar för Event Grid. Det gör du på den virtuella datorn där modulen distribueras kör följande kommando:

I Windows

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

På Linux,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Det går inte att göra HTTPS-begäranden

* Kontrollera först att modulen Event Grid har **inkommande:serverAuth:tlsPolicy** inställd på **strikt** eller **aktiverad**.

* Om dess modul-till-modul-kommunikation, se till att du ringer samtalet på port **4438** och namnet på modulen matchar vad som distribueras. 

  Om modulen Event Grid har distribuerats med namn **eventgridmodule** ska webbadressen vara **https://eventgridmodule:4438**. Kontrollera att höljet och portnumret är korrekta.
    
* Om det kommer från icke-IoT-modul kontrollerar du att Event Grid-porten är mappad till värddatorn under distributionen, till exempel

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Det går inte att göra HTTP-begäranden

* Kontrollera först att modulen Event Grid har **inkommande:serverAuth:tlsPolicy** inställd på **aktiverad** eller **inaktiverad**.

* Om dess modul-till-modul-kommunikation, se till att du ringer samtalet på port **5888** och namnet på modulen matchar vad som distribueras. 

  Om modulen Event Grid har distribuerats med namn **eventgridmodule** ska webbadressen vara **http://eventgridmodule:5888**. Kontrollera att höljet och portnumret är korrekta.
    
* Om det kommer från icke-IoT-modul kontrollerar du att Event Grid-porten är mappad till värddatorn under distributionen, till exempel

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>Certifikatkedjan har utfärdats av en myndighet som inte är betrodd

Som standard är Event Grid-modulen konfigurerad för att autentisera klienter med certifikat utfärdat av säkerhetsdemonen IoT Edge. Kontrollera att klienten presenterar ett certifikat som är förankrat i den här kedjan.

**IoTSecurity-klass** [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) visar hur du hämtar certifikat från IoT Edge Security daemon och använder den för att konfigurera utgående samtal.

Om det inte är produktionsmiljö har du möjlighet att inaktivera klientautentisering. Mer information om hur du gör detta finns i [Säkerhet och autentisering.](security-authentication.md)

## <a name="debug-events-not-received-by-subscriber"></a>Felsökningshändelser som inte tas emot av prenumerant

Typiska orsaker till detta är:

* Händelsen har aldrig bokförts. En HTTP-statuskod på 200(OK) ska tas emot vid bokföring av en händelse i modulen Event Grid.

* Kontrollera händelseprenumerationen för att verifiera:
    * Url:en för slutpunkt är giltig
    * Alla filter i prenumerationen gör inte att händelsen "tas bort".

* Kontrollera om abonnentmodulen körs

* Logga in på den virtuella datorn där modulen Event Grid distribueras och visa dess loggar.

* Aktivera per leveransloggning genom att ange **broker:logDeliverySuccess=true** och distribuera om event grid-modulen och försöka igen begäran. Aktivera loggning per leverans kan påverka dataflöde och svarstid så när felsökning är klar är vår rekommendation att vända tillbaka detta till **broker:logDeliverySuccess=false** och omfördelning av event grid-modul.

* Aktivera mått genom att ange **mått:reportertype=console** och omdedela Event Grid-modulen. Alla åtgärder efter det kommer att resultera i att mått loggas på konsolen för Event Grid-modulen, som kan användas för att felsöka ytterligare. Vår rekommendation är att aktivera mått endast för felsökning och när det är klart för att stänga av den genom att ange **mått:reportertype=none** och omdeställa eventrutnätsmodulen.

## <a name="next-steps"></a>Nästa steg

Rapportera eventuella problem, förslag med att använda [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)Händelserutnät på IoT Edge på .