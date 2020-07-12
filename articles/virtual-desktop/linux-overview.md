---
title: Windows Virtual Desktop Linux Support – Azure
description: En kort översikt av Linux-stöd för virtuella Windows-datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3dc9d62141a63574a6796982542cb1491108eedc
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259864"
---
# <a name="linux-support"></a>Linux-support

Partner kan använda Linux SDK för Windows Virtual Desktop för att bygga en fristående Windows-klient för virtuella skriv bord. Du kan också använda den för att aktivera stöd för virtuella Windows-datorer i klient programmet. Den här snabb guiden förklarar vad Linux SDK är och hur du börjar använda det.

## <a name="connect-with-your-linux-device"></a>Anslut till din Linux-enhet

Följande partners har godkänt Windows virtuella Skriv bords klienter för Linux-enheter.

|Partner|Partner dokumentation|Support för partner|
|:------|:--------------------|:--------------|
|![IGEL-logotyp](./media/partners/igel.png)|[Dokumentation om IGEL-klient](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL-stöd](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Vad är Linux SDK?

Du kan använda SDK-API: er för att hämta resurs flöden, ansluta till Skriv bords-eller fjärranslutna programsessioner och använda många av de omdirigeringar som våra klienter från den första parten har stöd för.

> [!NOTE]
> SDK är för närvarande under utveckling. Vi kommer att uppdatera det här dokumentet med instruktioner för att få åtkomst till SDK: n när det är allmänt tillgängligt.

### <a name="supported-linux-distributions"></a>Linux-distributioner som stöds

SDK är kompatibelt med de flesta operativ system baserade på Ubuntu 18,04 eller senare. Om du har en annan Linux-distribution kan vi samar beta med dig för att ta reda på hur du bäst stöder dina behov.

### <a name="feature-support"></a>Funktions stöd

SDK stöder flera anslutningar till Skriv bords-och fjärranslutna programsessioner. Följande omdirigeringar stöds:

| Omdirigering       | Stöds |
| :---------------- | :-------: |
| Tangentbord          | &#10004;  |
| Mus             | &#10004;  |
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

- [Klient för Windows-skrivbordet](connect-windows-7-10.md)
- [Webbklient](connect-web.md)
- [Android-klient](connect-android.md)
- [macOS-klient](connect-macos.md)
- [iOS-klient](connect-ios.md)
