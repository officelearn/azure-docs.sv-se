---
title: Linuxsupport för Windows Virtual Desktop – Azure
description: En kort översikt Linux-stöd för Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422422"
---
# <a name="linux-support"></a>Linux-support

Partner kan använda Linux SDK för Windows Virtual Desktop för att skapa en fristående Windows Virtual Desktop-klient. Du kan också använda den för att aktivera Windows Virtual Desktop-stöd för klientprogrammet. Denna snabbguide kommer att förklara vad Linux SDK är och hur man börjar använda den.

## <a name="connect-with-your-linux-device"></a>Anslut med din Linux-enhet

Följande partner har godkända Windows Virtual Desktop-klienter för Linux-enheter.

|Partner|Dokumentation från partner|Partnersupport|
|:------|:--------------------|:--------------|
|![IGEL-logotyp](./media/partners/igel.png)|[IGEL-klientdokumentation](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Support för IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Vad är Linux SDK?

Du kan använda SDK-API:erna för att hämta resursflöden, ansluta till skrivbords- eller fjärrprogramsessioner och använda många av de omdirigeringar som våra förstapartsklienter stöder.

> [!NOTE]
> SDK är för närvarande under utveckling. Vi uppdaterar det här dokumentet med instruktioner för att komma åt SDK när det är allmänt tillgängligt.

### <a name="supported-linux-distributions"></a>Linuxdistributioner som stöds

SDK är kompatibel med de flesta operativsystem baserade på Ubuntu 18.04 eller senare. Om du har en annan Linux-distribution kan vi arbeta med dig för att ta reda på hur du bäst kan stödja dina behov.

### <a name="feature-support"></a>Stöd för funktioner

SDK stöder flera anslutningar till skrivbords- och fjärrprogramsessioner. Följande omdirigeringar stöds:

| Omdirigering       | Stöds |
| :---------------- | :-------: |
| Tangentbord          | &#10004;  |
| Mus             | &#10004;  |
| Ljud i          | &#10004;  |
| Ljud ut         | &#10004;  |
| Urklipp (text)  | &#10004;  |
| Urklipp (bild) | &#10004;  |
| Urklipp (fil)  | &#10004;  |
| Smartcard         | &#10004;  |
| Enhet/mapp      | &#10004;  |

SDK stöder också flera bildskärmskonfigurationer, så länge de bildskärmar du väljer för sessionen är sammanhängande.

Vi uppdaterar det här dokumentet när vi lägger till stöd för nya funktioner och omdirigeringar. Om du vill föreslå nya funktioner och andra förbättringar, besök vår [UserVoice sida](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Komma igång med Linux SDK

Innan du kan utveckla en Linux-klient för Windows Virtual Desktop måste du göra följande:

1. Skapa och distribuera en Windows Virtual Desktop-miljö för testning eller produktionsanvändning.
2. Testa de tillgängliga klienterna från första part för att bekanta dig med användarupplevelsen för Virtuellt skrivbord i Windows.

## <a name="next-steps"></a>Nästa steg

Kolla in vår dokumentation för följande kunder:

- [Windows Desktop-klient](connect-windows-7-and-10.md)
- [Webbklient](connect-web.md)
- [Android-klient](connect-android.md)
- [macOS-klient](connect-macos.md)
- [iOS-klient](connect-ios.md)
