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
ms.openlocfilehash: f91e130bfa83c6a9b116c05d7293aa70945e2dc2
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903552"
---
# <a name="linux-support"></a>Linux-support

Du kan komma åt Windows virtuella Skriv bords resurser från dina Linux-enheter med följande klienter som stöds, som tillhandahålls av våra Linux-tunna klient partner. Vi arbetar med ett antal partner för att aktivera Windows virtuella Skriv bords klienter som stöds på fler Linux-baserade operativ system och enheter. Om du vill ha stöd för virtuella Windows-datorer på en Linux-plattform som inte visas här kan du berätta för oss på vår [UserVoice-sida](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux).

## <a name="connect-with-your-linux-device"></a>Anslut till din Linux-enhet

Följande partners har godkänt Windows virtuella Skriv bords klienter för Linux-enheter.

|Partner|Partner dokumentation|Support för partner|
|:------|:--------------------|:--------------|
|![IGEL-logotyp](./media/partners/igel.png)|[Dokumentation om IGEL-klient](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL-stöd](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Vad är Linux SDK?

Linux-tunna klient partner kan använda Windows Virtual Desktop Linux SDK-API: er för att hämta resurs flöden, ansluta till Skriv bords-eller fjärranslutna programsessioner och använda många av de omdirigeringar som våra klienter från den första parten har stöd för. SDK är kompatibelt med de flesta operativ system baserade på Ubuntu 18,04 eller senare.

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

## <a name="next-steps"></a>Nästa steg

Kolla in vår dokumentation för följande klienter:

- [Klient för Windows-skrivbordet](connect-windows-7-10.md)
- [Webbklient](connect-web.md)
- [Android-klient](connect-android.md)
- [macOS-klient](connect-macos.md)
- [iOS-klient](connect-ios.md)
