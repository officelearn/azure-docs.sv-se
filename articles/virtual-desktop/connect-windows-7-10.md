---
title: Ansluta till Windows Virtual Desktop Windows 10 eller 7 – Azure
description: Hur du ansluter till virtuella Windows-datorer med hjälp av Windows Skriv bords klienten.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 73811aa58b09e394468596f42e0ff221b2bb240b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262218"
---
# <a name="connect-with-the-windows-desktop-client"></a>Ansluta med Windows-skrivbordsklienten

> Gäller för: Windows 7, Windows 10 och Windows 10 IoT Enterprise

>[!IMPORTANT]
>Det här innehållet gäller för våren 2020-uppdateringen med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder den virtuella Windows-datorn med version 2019 utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
>
> Den virtuella Windows-skrivbordets våren 2020-uppdateringen är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan komma åt Windows-resurser för virtuella skriv bord på enheter med Windows 7, Windows 10 och Windows 10 IoT Enterprise med Windows Skriv bords klienten.

>[!NOTE]
>Windows-klienten har automatiskt standard versionen av Windows virtuella skriv bord 2019. Men om klienten upptäcker att användaren också har Azure Resource Manager resurser lägger den automatiskt till resurserna eller meddelar användaren om att de är tillgängliga.

> [!IMPORTANT]
> Windows Virtual Desktop stöder inte RADC-klienten (RemoteApp-och Desktop Connections) eller Anslutning till fjärrskrivbord-klienten (MSTSC).

> [!IMPORTANT]
> Windows Virtual Desktop stöder för närvarande inte fjärr skrivbords klienten från Windows Store.

## <a name="install-the-windows-desktop-client"></a>Installera Windows Desktop-klienten

Välj den klient som matchar din version av Windows:

- [Windows 64-bitars](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bitars](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows-ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Du kan installera klienten för den aktuella användaren, som inte kräver administratörs rättigheter, eller så kan administratören installera och konfigurera klienten så att alla användare på enheten kan komma åt den.

När du har installerat kan klienten startas från Start-menyn genom att söka efter **fjärr skrivbord**.

## <a name="subscribe-to-a-workspace"></a>Prenumerera på en arbets yta

Du kan prenumerera på en arbets yta på två sätt. Klienten kan försöka identifiera de resurser som är tillgängliga från ditt arbets-eller skol konto eller så kan du ange webb adressen direkt där dina resurser finns i de fall där klienten inte kan hitta dem. När du prenumererar på en arbets yta kan du starta resurser med någon av följande metoder:

- Gå till anslutnings Center och dubbelklicka på en resurs för att starta den.
- Du kan också gå till Start-menyn och leta efter en mapp med namnet på arbets ytan eller ange resurs namnet i Sök fältet.

### <a name="subscribe-with-a-user-account"></a>Prenumerera med ett användar konto

1. Välj **Prenumerera**på klientens huvud sida.
2. Logga in med ditt användar konto när du uppmanas till det.
3. Resurserna kommer att visas i anslutnings Center och grupperas efter arbets yta.

### <a name="subscribe-with-a-url"></a>Prenumerera med en URL

1. Från klientens huvud sida väljer du **Prenumerera med URL**.
2. Ange arbets ytans URL eller din e-post adress:
   - Om du använder **URL: en för arbets ytan**använder du den som din administratör fick. Om du får åtkomst till resurser från virtuella Windows-datorer kan du använda någon av följande URL: er:
     - Windows Virtual Desktop sjunker 2019:`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Windows Virtual Desktop fjäder 2020:`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - Om du använder **e-** postfältet i stället anger du din e-postadress. Detta instruerar klienten att söka efter en URL som är kopplad till din e-postadress om din administratör har konfigurerat [identifiering av e-post](/windows-server/remote/remote-desktop-services/rds-email-discovery).
3. Välj **Nästa**.
4. Logga in med ditt användar konto när du uppmanas till det.
5. Resurserna bör visas i anslutnings centret, grupperade efter arbets yta.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder Windows-skrivbordet kan du läsa [komma igång med Windows Skriv bords klienten](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
