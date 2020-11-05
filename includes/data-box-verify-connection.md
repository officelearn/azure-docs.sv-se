---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 3e4edc55834d322dede36e9f429f5af7a4a79846
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375897"
---
Anslut till lagringskontot och verifiera anslutningen på följande sätt.

1. Välj **Anslut till Azure Storage** i Storage Explorer. Välj **Använd lagringskontonamn och nyckel** i dialogrutan **Anslut till Azure Storage**.

    ![Data Box-instrumentpanel](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Klistra in ditt **kontonamn** och din **kontonyckel** (nyckel 1-värdet från sidan **Anslut och kopiera** i det lokala webbanvändargränssnittet). Välj **Annan (ange nedan)** som domän för lagringsslutpunkter och ange den blobtjänstslutpunkt som visas nedan. Markera alternativet **Använd HTTP** endast om du över via *http*. Lämna alternativet omarkerat om du använder *https*. Välj **Nästa**.

    ![Data Box-enhet instrument panel 2](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Kontrollera informationen i dialogrutan **Anslutningssammanfattning**. Välj **Anslut**.

    ![Data Box-enhet instrument panel 3](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Det konto som du har lagt till visas i den vänstra rutan i Storage Explorer med (extern, övrigt) i slutet av namnet. Visa containern genom att klicka på **Blobcontainer**.

    ![Data Box-enhet instrument panel 4](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
