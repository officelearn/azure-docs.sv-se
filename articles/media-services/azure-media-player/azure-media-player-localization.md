---
title: Azure Media Player lokalisering
description: Stöd för flera språk för användare av andra språk än engelska.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ca4dc888af414ede270118eff72652f098d3306c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779050"
---
# <a name="localization"></a>Lokalisering #

Stöd för flera språk gör att användare av andra språk än engelska kan interagera med spelaren internt. Azure Media Player instansieras med en global ord lista med språk, vilket kommer att lokalisera fel meddelandena baserat på sid språket. En utvecklare kan också skapa en Player-instans med ett tvingat set-språk. Standard språket är engelska (en).

> [!NOTE]
> Den här funktionen kommer fortfarande att gå igenom vissa tester och kan vara beroende av buggar.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player stöder för närvarande följande språk koder:

| Språk            | Kod | Språk                | Kod   | Språk                | Kod         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Engelska {standard}   | en   | Kroatiska                | tim     | Rumänska                | ro           |
| Arabiska              | bakre   | Ungerska               | HU     | Slovakiska                  | sk           |
| Bulgariska           | Bulgarien   | Indonesiska              | id     | Slovene                 | SL           |
| Katalanska             | onlinecertifikatutfärdare   | Isländska               | är     | Serbiska-kyrilliska      | sr-cyrl-CS   |
| Tjeckiska               | östasiatisk   | Italienska                 | it     | Serbiska-Latin         | sr-latn-rs   |
| Danska              | da   | Japanska                | ja     | Ryska                 | ru           |
| Tyska              | de   | Kazakiska                  | kk     | Svenska                 | sv           |
| Grekiska               | El   | Koreansk                  | Ko     | Thailändska                    | i:te           |
| Spanska             | ES   | Litauiska              | långsiktiga     | Tagalog                 | TL           |
| Estniska            | ge   | Lettiska                 | LV     | Turkiska                 | TR           |
| Baskiska              | gemenskaps   | Malaysia               | millisekund     | Ukrainska               | Storbritannien           |
| Farsi               | bokförda   | Norska-BokmÃ ¥ l     | Anm     | Urdu                    | vilken           |
| Finska             | fi   | Nederländska                   | nl     | Vietnamesiska              | Vi           |
| Franska              | fr   | Norska-Nynorsk     | nn     | Kinesiska – Förenklad    | zh-hans      |
| Galiciska            | huvud   | Polska                  | pl     | Kinesiska (traditionell)   | zh-Hant      |
| Hebreiska              | producenten   | Portugisiska – Brasilien     | pt-br  |                         |              |
| Hindi               | Hej   | Portugisiska – Portugal   | pt-pt  |                         |              |


> [!NOTE]
> Om du inte vill att någon lokalisering ska ske måste du tvinga språket till engelska

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)
