---
author: WenJason
ms.service: databox
ms.topic: include
origin.date: 12/07/2018
ms.date: 02/25/2019
ms.author: v-jay
ms.openlocfilehash: 0a9aaa86d44e71e429f2bfff13a56ddcb1ee2071
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60728407"
---
1. Logga in på Data Box-enheten. Se till att den är upplåst.

    ![Instrumentpanel för Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Gå till **Ställ in nätverksgränssnitt**. Anteckna enhetens IP-adress för nätverksgränssnittet som används för att ansluta till klienten.

    ![Instrumentpanel för Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Gå till **Anslut och kopiera** och klicka på **Rest (förhandsversion)**.

    ![Instrumentpanel för Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Från dialogrutan **Öppna lagringskonto och ladda upp data** kopierar du **Blob Service-slutpunkt**.

    ![Instrumentpanel för Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Starta **Anteckningar** som administratör och öppna sedan **värdens** fil på `C:\Windows\System32\Drivers\etc`.
6. Lägg till följande post för **värdens** fil: `<device IP address> <Blob service endpoint>`
7. Använd följande bild som referens. Spara **värdens** fil.

    ![Instrumentpanel för Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
