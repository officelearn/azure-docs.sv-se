---
title: Hantera beräkningsnätverk på Azure Data Box Edge för att komma åt moduler| Microsoft-dokument
description: Beskriver hur du utökar beräkningsnätverket på databoxens edge för att komma åt moduler via en extern IP.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65917242"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Aktivera beräkningsnätverk på azure databox edge

I den här artikeln beskrivs hur modulerna som körs på din Azure Data Box Edge kan komma åt det beräkningsnätverk som är aktiverat på enheten.

Så här konfigurerar du nätverket:

- Aktivera ett nätverksgränssnitt på Data Box Edge-enheten för beräkning
- Lägga till en modul för att komma åt beräkningsnätverket på databoxkanten
- Kontrollera att modulen kan komma åt det aktiverade nätverksgränssnittet

I den här självstudien använder du en webbserverappmodul för att demonstrera scenariot.

## <a name="prerequisites"></a>Krav

Innan du börjar behöver du:

- En Data Box Edge-enhet med enhetskonfigurationen klar.
- Du har slutfört **konfigurera beräkningssteg** enligt [självstudiekursen: Omvandla data med Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) på din enhet. Enheten ska ha en associerad IoT Hub-resurs, en IoT-enhet och en IoT Edge-enhet.

## <a name="enable-network-interface-for-compute"></a>Aktivera nätverksgränssnitt för beräkning

Om du vill komma åt modulerna som körs på enheten via ett externt nätverk måste du tilldela en IP-adress till ett nätverksgränssnitt på enheten. Du kan hantera dessa beräkningsinställningar från det lokala webbgränssnittet.

Gör så här i det lokala webbgränssnittet för att konfigurera beräkningsinställningar.

1. Gå till **konfigurations-> beräkningsinställningar i**det lokala webbgränssnittet .  

2. **Aktivera** det nätverksgränssnitt som du vill använda för att ansluta till en beräkningsmodul som du ska köra på enheten.

    - Om du använder statiska IP-adresser anger du en IP-adress för nätverksgränssnittet.
    - Om du använder DHCP tilldelas IP-adresserna automatiskt. I det här exemplet används DHCP.

    ![Aktivera beräkningsinställningar 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Välj **Använd** om du vill använda inställningarna. Anteckna den IP-adress som tilldelats nätverksgränssnittet om du använder DHCP.

    ![Aktivera beräkningsinställningar](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Lägg till appmodul för webbserver

Gör så här för att lägga till en webbserverappmodul på databoxens edge-enhet.

1. Gå till IoT Hub-resursen som är associerad med databoxens edge-enhet och välj sedan **IoT Edge-enhet**.
2. Välj den IoT Edge-enhet som är associerad med databoxens edge-enhet. Välj **Ange moduler** **på enhetsinformationen**. På **Lägg till moduler**väljer du + Lägg **till** och väljer sedan **IoT Edge Module**.
3. I **bladet IoT Edge anpassade moduler:**

    1. Ange ett **namn** för webbserverappmodulen som du vill distribuera.
    2. Ange en **bild-URI** för din modulavbildning. En modul som matchar det angivna namnet och taggarna hämtas. I det `nginx:stable` här fallet kommer att dra en stabil nginx bild (taggad som stabil) från den offentliga [Docker-databasen](https://hub.docker.com/_/nginx/).
    3. Klistra in följande exempelkod i **alternativen För behållar skapa:**  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Med den här konfigurationen kan du komma åt modulen med hjälp av beräkningsnätverkets IP över *http* på TCP-port 8080 (med standardwebbserverporten 80).

        ![Ange portinformation i anpassade modulblad för IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Välj **Spara**.

## <a name="verify-module-access"></a>Verifiera modulåtkomst

1. Kontrollera att modulen har distribuerats och körs. På sidan **Enhetsinformation,** på fliken Moduler, ska **körningsstatusen** för modulen **köras**.  
2. Anslut till webbserverappmodulen. Öppna ett webbläsarfönster och skriv:

    `http://<compute-network-IP-address>:8080`

    Du bör se att webbserverappen körs.

    ![Verifiera anslutning till modul över angiven port](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar användare via Azure-portalen](data-box-edge-manage-users.md).
