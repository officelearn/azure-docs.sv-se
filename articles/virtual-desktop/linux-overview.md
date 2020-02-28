---
title: Windows Virtual Desktop Linux Support – Azure
description: En kort översikt av Linux-stöd för virtuella Windows-datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
ms.openlocfilehash: af5ab05cb772909cd36e2e6bfe1c100a5b83841c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660044"
---
# <a name="linux-support"></a>Linux-support

Du kan använda Linux SDK för Windows Virtual Desktop för att bygga en fristående Windows-klient för virtuella skriv bord. Du kan också använda den för att aktivera stöd för virtuella Windows-datorer i klient programmet. Den här snabb guiden förklarar vad Linux SDK är och hur du börjar använda det.

## <a name="what-is-the-linux-sdk"></a>Vad är Linux SDK?

Du kan använda SDK-API: er för att hämta resurs flöden, ansluta till Skriv bords-eller fjärranslutna programsessioner och använda många av de omdirigeringar som våra klienter från den första parten har stöd för.

> [!NOTE]
> SDK är för närvarande under utveckling. Vi kommer att uppdatera det här dokumentet med instruktioner för att få åtkomst till SDK när det är tillgängligt.

### <a name="supported-linux-distributions"></a>Linux-distributioner som stöds

SDK är kompatibelt med de flesta operativ system baserade på Ubuntu 18,04 eller senare. Om du har en annan Linux-distribution kan vi samar beta med dig för att ta reda på hur du bäst stöder dina behov.

### <a name="feature-support"></a>Funktioner som stöds

SDK stöder flera anslutningar till Skriv bords-och fjärranslutna programsessioner. Följande omdirigeringar stöds:

| Omdirigering       | Stöds |
| :---------------- | :-------: |
| PS/2-tangentbordet          | &#10004;  |
| Klickning             | &#10004;  |
| Ljud i          | &#10004;  |
| Ljud ut         | &#10004;  |
| Urklipp (text)  | &#10004;  |
| Urklipp (bild) | &#10004;  |
| Urklipp (fil)  | &#10004;  |
| Smartkortsinloggning         | &#10004;  |
| Enhet/mapp      | &#10004;  |

SDK har även stöd för flera bildskärms konfigurationer, så länge de övervakare du väljer för din session är sammanhängande.

Vi kommer att uppdatera det här dokumentet när vi lägger till stöd för nya funktioner och omdirigeringar. Om du vill föreslå nya funktioner och andra förbättringar kan du besöka vår [UserVoice-sida](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Kom igång med Linux SDK

Innan du kan utveckla en Linux-klient för Windows Virtual Desktop måste du göra följande:

1. Bygg och distribuera en Windows Virtual Desktop-miljö för testning eller användning av produktion.
2. Testa de tillgängliga första parts-klienterna för att bekanta dig med användar upplevelsen av Windows virtuella skriv bord.

## <a name="next-steps"></a>Nästa steg

Kolla in vår dokumentation för följande klienter:

- [Windows Desktop-klient](connect-windows-7-and-10.md)
- [Webb klient](connect-web.md)
- [Android-klient](connect-android.md)
- [macOS-klient](connect-macos.md)
- [iOS-klient](connect-ios.md)
