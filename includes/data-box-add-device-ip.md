---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67188008"
---
1. Logga in på Data Box-enheten. Se till att den är upplåst.

    ![Data Box-instrumentpanel](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Gå till **Ställ in nätverksgränssnitt**. Anteckna enhetens IP-adress för nätverksgränssnittet som används för att ansluta till klienten.

    ![Data Box-instrumentpanel](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Gå till **Anslut och kopiera** och klicka på **Vila**.

    ![Data Box-instrumentpanel](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Från dialogrutan **Öppna lagringskonto och ladda upp data** kopierar du **Blob Service-slutpunkt**.

    ![Data Box-instrumentpanel](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Starta **Anteckningar** som administratör och öppna sedan **värdens** fil på `C:\Windows\System32\Drivers\etc`.
6. Lägg till följande post för **värdens** fil: `<device IP address> <Blob service endpoint>`
7. Använd följande bild som referens. Spara **värdens** fil.

    ![Data Box-instrumentpanel](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
