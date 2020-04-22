---
title: Lokalisering av Azure Media Player
description: Flera språkstöd för användare av icke-engelska språk.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727182"
---
# <a name="localization"></a>Lokalisering #

Stöd för flera språk gör det möjligt för användare av icke-engelska språk att interagera med spelaren. Azure Media Player instansierar med en global ordlista med språk, vilket lokaliserar felmeddelandena baserat på sidspråket. En utvecklare kan också skapa en spelarinstans med ett påtvingat inställt språk. Standardspråket är engelska (sv).

> [!NOTE]
> Den här funktionen genomgår fortfarande vissa tester och är därför föremål för buggar.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player stöder för närvarande följande språk med motsvarande språkkoder:

| Språk            | Kod | Språk                | Kod   | Språk                | Kod         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Engelska {standard}   | en   | Kroatiska                | tim     | Rumänska                | Ro           |
| Arabiska              | Ar   | Ungerska               | Hu     | Slovakiska                  | Sk           |
| Bulgariska           | Bg   | Indonesiska              | id     | Slovenska                 | Sl           |
| Katalanska             | ca ( ca )   | Isländska               | är     | Serbiska - kyrilliska      | sr-cyrl-cs   |
| Tjeckiska               | Cs   | Italienska                 | it     | Serbiska - latin         | sr-latn-rs   |
| Danska              | da   | Japanska                | ja     | Ryska                 | ru           |
| Tyska              | de   | Kazakiska                  | Kk     | Svenska                 | sv           |
| Grekiska               | El   | Koreansk                  | Ko     | Thailändska                    | Th           |
| Spanska             | ES   | Litauiska              | Lt     | Tagalog                 | Tl           |
| Estniska            | Et   | Lettiska                 | Lv     | Turkiska                 | Tr           |
| Baskiska              | Eu   | Malaysiska               | Ms     | Ukrainska               | Storbritannien           |
| Persiska               | Fa   | Norska - BokmÃ¥l     | Nb     | Urdu                    | Din           |
| Finska             | fi   | Nederländska                   | nl     | Vietnamesiska              | vi           |
| Franska              | fr   | Norska - Nynorsk     | nn     | Kinesiska - förenklad    | zh-hans (olikartade)      |
| Galiciska            | Gl   | Polska                  | Pl     | Kinesiska - traditionella   | zh-hant (olika)      |
| Hebreiska              | Han   | Portugisiska – Brasilien     | pt-br  |                         |              |
| Hindi               | Hu   | Portugisiska – Portugal   | pt-pt  |                         |              |


> [!NOTE]
> Om du inte vill att någon lokalisering ska ske måste du tvinga språket till engelska

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)