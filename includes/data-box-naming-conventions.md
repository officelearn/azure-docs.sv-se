---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244670"
---
| Entitet                                       | Konventioner   |
|----------------------------------------------|-----------------------------------------------------|
| Behållarnamn för blockblob och sidblob | Måste vara ett giltigt DNS-namn som är 3 till 63 tecken långt. <br>  Måste börja med en bokstav eller en siffra. <br> Kan bara innehålla gemener, siffror och bindestreck (-). <br> Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. <br> På varandra följande bindestreck är inte tillåtna i namn. |
| Dela namn för Azure-filer                  | Samma som ovan                                          |
| Katalog- och filnamn för Azure-filer     |<li> Fallbevarande, skiftlägesokänslig och får inte vara längre än 255 tecken. </li><li> Det går inte att sluta med det främre snedstrecket (/). </li><li>Om det tillhandahålls tas den bort automatiskt. </li><li> Följande tecken är inte tillåtna:<code>" \\ / : \| < > * ?</code></li><li> Reserverade URL-tecken måste undantas korrekt. </li><li> Ogiltiga URL-sökvägstecken är inte tillåtna. Kodpunkter \\som uE000 är inte giltiga Unicode-tecken. Vissa ASCII- eller Unicode-tecken, som kontrolltecken (0x00 till 0x1F, \\u0081, etc.), är inte heller tillåtna. Regler som styr Unicode-strängar i HTTP/1.1 finns i RFC 2616, avsnitt 2.2: Grundläggande regler och RFC 3987. </li><li> Följande filnamn är inte tillåtna: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, dot character (.).</li>|
| Blobnamn för blockblobar och sidblobar      | </li><li>Blobnamn är skiftlägeskänsliga och kan innehålla valfri kombination av tecken. </li><li>Ett blobnamn måste vara mellan 1 och 1 024 tecken långt. </li><li>Reserverade URL-tecken måste undantas korrekt. </li><li>Antalet sökvägssegment som blobnamnet består av får inte överskrida 254. Ett segment är strängen mellan avgränsningstecken (till exempel snedstreck ”/”) som motsvarar namnet på en virtuell katalog.</li> |
