---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244670"
---
| Entitet                                       | Konventioner   |
|----------------------------------------------|-----------------------------------------------------|
| Namn på behållare för blockblob och page blob | Måste vara ett giltigt DNS-namn som är 3 till 63 tecken långt. <br>  Måste börja med en bokstav eller en siffra. <br> Kan innehålla endast gemener, siffror och bindestreck (-). <br> Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. <br> Flera bindestreck i rad är inte tillåtet i namn. |
| Resursnamn för Azure files                  | Samma som ovan                                          |
| Katalog- och filnamn för Azure files     |<li> Bevara, skiftlägesokänslig och får inte överstiga 255 tecken långt. </li><li> Får inte sluta med snedstreck (/). </li><li>Om det tas automatiskt bort. </li><li> Följande tecken är inte tillåtna: <code>" \\ / : \| < > * ?</code></li><li> Reserverade URL-tecken måste undantas korrekt. </li><li> Ogiltiga tecken i URL-sökväg är inte tillåtna. Code punkter som \\uE000 är inte giltig Unicode-tecken. Vissa ASCII eller Unicode-tecken som kontrolltecken (0x00 0x1F, \\u0081, etc.), också är inte tillåtna. Regler för att styra Unicode strängar i HTTP/1.1 finns i RFC 2616 avsnittet 2.2: Grundläggande regler och RFC 3987. </li><li> Följande filnamn är inte tillåtna: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, punkt (.), och två punkter tecken (.).</li>|
| Blobnamn för blockblobar och sidblobar      | </li><li>Blobnamn är skiftlägeskänsliga och kan innehålla valfri kombination av tecken. </li><li>Ett blobnamn måste vara mellan 1 och 1 024 tecken långt. </li><li>Reserverade URL-tecken måste undantas korrekt. </li><li>Antalet sökvägssegment som blobnamnet består av får inte överskrida 254. Ett segment är strängen mellan avgränsningstecken (till exempel snedstreck ”/”) som motsvarar namnet på en virtuell katalog.</li> |
