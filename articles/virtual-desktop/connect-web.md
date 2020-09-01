---
title: Ansluta till virtuella Windows-datorer med webb klienten – Azure
description: Ansluta till virtuella Windows-datorer med hjälp av webb klienten.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ca5a591d168e18181a29cf1c00a7d1fead54595
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226054"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Ansluta till virtuella Windows-datorer med webb klienten

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/connect-web-2019.md).

Med webb klienten kan du komma åt dina Windows-resurser för virtuella skriv bord från en webbläsare utan den långa installations processen.

>[!NOTE]
>Webb klienten har för närvarande inte stöd för mobila operativ system.

## <a name="supported-operating-systems-and-browsers"></a>Operativsystem och webbläsare som stöds

Även om en HTML5-kompatibel webbläsare ska fungera, har vi officiellt stöd för följande operativ system och webbläsare.

| Webbläsare           | Operativ system som stöds                     | Anteckningar               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Version 11 eller senare |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Version 55 eller senare |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Åtkomst till fjär resurser-feed

I en webbläsare går du till den Azure Resource Manager-integrerade versionen av webb klienten för virtuella Windows-datorer på <https://rdweb.wvd.microsoft.com/arm/webclient> och loggar in med ditt användar konto.

>[!NOTE]
>Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager-integrering ansluter du till dina resurser i <https://rdweb.wvd.microsoft.com/webclient> stället.
>
> Om du använder US Gov portalen använder du <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Om du redan har loggat in med ett annat Azure Active Directory-konto än det som du vill använda för Windows Virtual Desktop, bör du antingen logga ut eller använda ett privat webbläsarfönster.

När du har loggat in bör du nu se en lista över resurser. Du kan starta resurser genom att välja dem som en normal app på fliken **alla resurser** .

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder webb klienten kan du läsa [komma igång med webb klienten](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
