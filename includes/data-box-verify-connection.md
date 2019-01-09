---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550495"
---
Anslut till lagringskontot och verifiera anslutningen på följande sätt.

1. Välj **Anslut till Azure Storage** i Storage Explorer. Välj **Använd lagringskontonamn och nyckel** i dialogrutan **Anslut till Azure Storage**.

    ![Data Box-instrumentpanel](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Klistra in ditt **kontonamn** och din **kontonyckel** (nyckel 1-värdet från sidan **Anslut och kopiera** i det lokala webbanvändargränssnittet). Välj **Annan (ange nedan)** som domän för lagringsslutpunkter och ange den blobtjänstslutpunkt som visas nedan. Markera alternativet **Använd HTTP** endast om du över via *http*. Lämna alternativet omarkerat om du använder *https*. Välj **Nästa**.

    ![Data Box-instrumentpanel](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Kontrollera informationen i dialogrutan **Anslutningssammanfattning**. Välj **Anslut**.

    ![Data Box-instrumentpanel](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Det konto som du har lagt till visas i den vänstra rutan i Storage Explorer med (extern, övrigt) i slutet av namnet. Visa behållaren genom att klicka på **Blobbehållare**.

    ![Data Box-instrumentpanel](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
