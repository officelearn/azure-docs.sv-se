---
title: Anslut Windows Virtual Desktop webb klient – Azure
description: Ansluta till virtuella Windows-datorer med hjälp av webb klienten.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e95498498fd15d7a6e73630e218f9bdd1b892f09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204803"
---
# <a name="connect-with-the-web-client"></a>Ansluta med webbklienten

>[!IMPORTANT]
>Det här innehållet gäller för hösten 2019-versionen som inte stöder Azure Resource Manager virtuella Windows Desktop-objekt. Om du försöker hantera Azure Resource Manager virtuella Windows-skrivbordet som introduceras i våren 2020-uppdateringen, se [den här artikeln](../connect-web.md).

Med webb klienten kan du komma åt dina Windows-resurser för virtuella skriv bord från en webbläsare utan den långa installations processen.

>[!NOTE]
>Webb klienten har för närvarande inte stöd för mobila operativ system.

## <a name="supported-operating-systems-and-browsers"></a>Operativsystem och webbläsare som stöds

Även om en HTML5-kompatibel webbläsare ska fungera, har vi officiellt stöd för följande operativ system och webbläsare.

| Webbläsare           | Operativ system som stöds                     | Obs!               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Version 55 eller senare |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Åtkomst till fjär resurser-feed

I en webbläsare, navigerar du till Windows Virtual Desktop-webbklienten på <https://rdweb.wvd.microsoft.com/webclient> och loggar in med ditt användar konto.

>[!NOTE]
>Om du använder Windows Virtual Desktop fjäder 2020-versionen med Azure Resource Manager-integrering ansluter du till dina resurser i <https://rdweb.wvd.microsoft.com/arm/webclient> stället.

>[!NOTE]
>Om du redan har loggat in med ett annat Azure Active Directory-konto än det som du vill använda för Windows Virtual Desktop, bör du antingen logga ut eller använda ett privat webbläsarfönster.

När du har loggat in bör du nu se en lista över resurser. Du kan starta resurser genom att välja dem som en normal app på fliken **alla resurser** .

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder webb klienten kan du läsa [komma igång med webb klienten](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
