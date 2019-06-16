---
title: Hantera beräkning nätverk på Azure Data Box Edge åtkomst modulerna | Microsoft Docs
description: Beskriver hur du utökar beräkning nätverket på din Data Box Edge till moduler via en extern IP-adress.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65917242"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Aktivera beräkning nätverket på din Azure Data Box Edge

Den här artikeln beskriver hur de moduler som körs på Azure Data Box-Edge har åtkomst till compute-nätverk som är aktiverat på enheten.

För att konfigurera nätverket, ska du utföra följande steg:

- Aktivera ett nätverksgränssnitt på din Data Box Edge-enhet för beräkning
- Lägga till en modul att få åtkomst till beräkning på din Data Box-Edge
- Kontrollera att modulen kan komma åt det aktiverade nätverksgränssnittet

I de här självstudierna ska du använda en webbserver app-modul för att demonstrera scenariot.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar behöver du:

- En Data Box Edge-enhet med device installationen slutförts.
- Du har slutfört **konfigurera beräkna** steg enligt den [självstudien: Transformera data med Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) på din enhet. Enheten bör ha en associerad IoT Hub-resurs, en IoT-enhet och en IoT Edge-enhet.

## <a name="enable-network-interface-for-compute"></a>Aktivera nätverksgränssnitt för beräkning

För att komma åt de moduler som körs på din enhet via ett externt nätverk, måste du tilldela en IP-adress till ett nätverksgränssnitt på din enhet. Du kan hantera dessa compute inställningar från din lokala webbgränssnittet.

Gör följande i din lokala webbgränssnitt att konfigurera inställningarna för beräkning.

1. I det lokala webbgränssnittet går du till **Configuration > Compute inställningar**.  

2. **Aktivera** nätverksgränssnittet som du vill använda för att ansluta till en beräkning-modul som ska köras på enheten.

    - Om du använder statiska IP-adresser anger du en IP-adress för nätverksgränssnittet.
    - Om du använder DHCP, tilldelas IP-adresser automatiskt. Det här exemplet använder DHCP.

    ![Aktivera inställningarna för beräkning 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Välj **tillämpa** att tillämpa inställningarna. Anteckna den IP-adress till nätverksgränssnittet om du använder DHCP.

    ![Aktivera inställningarna för beräkning](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Lägg till app-modul för webbserver

Vidta följande åtgärder för att lägga till en modul för webbserver-appen på din Data Box Edge-enhet.

1. Gå till IoT Hub-resurs som är associerade med din Data Box Edge-enhet och välj sedan **IoT Edge-enhet**.
2. Välj IoT Edge-enhet som är associerade med din Data Box Edge-enhet. På den **enhetsinformation**väljer **ange moduler**. På **Lägg till moduler**väljer **+ Lägg till** och välj sedan **IoT Edge-modul**.
3. I den **anpassade IoT Edge-moduler** bladet:

    1. Ange en **namn** för din webbserver app-modul som du vill distribuera.
    2. Ange en **URI för avbildning** för din avbildning av modulen. En modul som matchar det angivna namnet och taggar hämtas. I det här fallet `nginx:stable` hämtar en stabil nginx-avbildning (märkta som stabil) från offentligt [Docker-lagringsplats](https://hub.docker.com/_/nginx/).
    3. I den **behållare skapa alternativ**, klistra in följande exempelkod:  

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

        Den här konfigurationen kan du komma åt modulen med beräkning nätverks-IP över *http* på TCP-port 8080 (med standardporten för webbserver som är 80).

        ![Ange portinformation i bladet för IoT Edge-anpassad modul](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Välj **Spara**.

## <a name="verify-module-access"></a>Kontrollera åtkomst till modul

1. Kontrollera modulen har distribuerats och körs. På den **enhetsinformation** klickar du på den **moduler** fliken Körningsstatus för modulen ska vara **kör**.  
2. Ansluta till web server app-modulen. Öppna ett webbläsarfönster och skriv:

    `http://<compute-network-IP-address>:8080`

    Du bör se att webbserver-appen körs.

    ![Verifiera anslutningen till modulen via angivna porten](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar användare via Azure-portalen](data-box-edge-manage-users.md).
