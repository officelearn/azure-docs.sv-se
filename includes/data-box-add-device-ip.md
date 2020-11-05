---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 6b716640bc8117ccc2d473b337ee3e555e8d9b0a
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376233"
---
1. Logga in på Data Box-enheten. Se till att den är upplåst.

    ![Data Box-instrumentpanel](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Gå till **Ställ in nätverksgränssnitt**. Anteckna enhetens IP-adress för nätverksgränssnittet som används för att ansluta till klienten.

    ![Data Box-enhet instrument panel 2](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Gå till **Anslut och kopiera** och klicka på **rest**.

    ![Data Box-enhet instrument panel 3](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Från dialogrutan **Öppna lagringskonto och ladda upp data** kopierar du **Blob Service-slutpunkt**.

    ![Data Box-enhet instrument panel 4](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Starta **Anteckningar** som administratör och öppna sedan **värdens** fil på `C:\Windows\System32\Drivers\etc`.
6. Lägg till följande post för **värdens** fil: `<device IP address> <Blob service endpoint>`
7. Använd följande bild som referens. Spara **värdens** fil.

    ![Data Box-enhet instrument panel 5](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
