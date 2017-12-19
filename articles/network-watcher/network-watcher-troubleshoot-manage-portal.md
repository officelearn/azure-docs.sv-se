---
title: "Felsöka Azure virtuell nätverksgateway och anslutningar - PowerShell | Microsoft Docs"
description: "Den här sidan förklarar hur du använder Azure Nätverksbevakaren felsöka PowerShell-cmdlet"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: c3fa22bd599026b0838b134e26062d9837df703e
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Felsöka virtuella nätverksgateway och anslutningar med hjälp av Azure Network Watcher PowerShell

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST-API](network-watcher-troubleshoot-manage-rest.md)

Nätverksbevakaren innehåller många funktioner relateras till att förstå nätverksresurserna i Azure. En av dessa funktioner är resurs felsökning. Felsökning av resursen kan anropas via portalen, PowerShell, CLI eller REST API. När den anropas, Nätverksbevakaren kontrollerar hälsotillståndet för en virtuell nätverksgateway eller en anslutning och returnerar resultatet.

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren.

En lista över stöds gateway typer besök [stöd för Gateway-typer](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Översikt

Felsökning av resursen ger möjlighet felsöka problem som uppstår med virtuella Nätverksgatewayer och anslutningar. När en begäran skickas till resursen felsökning, loggar som efterfrågas och kontrolleras. Resultaten returneras när kontroll har slutförts. Resursen felsökning förfrågningar är tidskrävande begäranden, vilket kan ta flera minuter att returnera ett resultat. Loggar från felsökning lagras i en behållare för ett lagringskonto som har angetts.

## <a name="troubleshoot-a-gateway-or-connection"></a>Felsöka en gateway eller -anslutning

1. Navigera till den [Azure-portalen](https://portal.azure.com) och på **nätverk** > **Nätverksbevakaren** > **VPN-diagnostik**
2. Välj en **prenumeration**, **resursgruppen**, och **plats**.
3. Felsökning av resursen returnerar data om hälsotillståndet för resursen. Här sparas även relevanta loggar till ett lagringskonto som ska granskas. Klicka på **Lagringskonto** att välja ett lagringskonto.
4. På den **lagringskonton** bladet Välj ett befintligt lagringskonto eller klicka på **+ lagringskonto** att skapa en ny.
5. På den **behållare** bladet Välj en befintlig behållare eller klicka på **+ behållare** att skapa en ny behållare. När du är klar klickar du på **Välj**
6. Välj Gateway och anslutning till resurser för att felsöka och klicka på **Starta felsökning**

Om du har valt flera resurser körs felsökning samtidigt på gateway-resurser. Det kan inte köras på en anslutning och dess tillhörande gateway på samma gång. Det rekommenderas att felsöka gateways först anslutningen felsökning är en längre process. När VPN-diagnostik körs på en resurs i **felsökning STATUS** kolumnen visar statusen **körs**

När du är klar i statuskolumnen ändras till **felfri** eller **ohälsosam**.

![Felsöka klar][2]

## <a name="understanding-the-results"></a>Förstå resultaten

I den **information** i fönstret, den **Status** visar status för senaste felsökning kör på den valda resursen. Senaste diagnostiken skall visas xx minuter efter den senaste körningen.

|Egenskap  |Beskrivning  |
|---------|---------|
|Resurs     | En länk till resursen.        |
|Sökvägen till lagring     |  Sökvägen till lagringskontot och behållare som innehåller loggarna (om någon har producerats under kör). Den här inställningen sparas inte när du lämnar portalen.        |
|Sammanfattning     | Sammanfattning av hälsotillstånd för resursen.        |
|Detalj     | Detaljerad information om hälsotillståndet för resursen.        |
|Senaste körning     | Den tid som den senaste gången felsökning har körts.        |


Den **åtgärd** fliken innehåller allmänna råd om hur du löser problemet. Om en åtgärd kan vidtas för utfärdande, finns en länk med mer information. I fall där det finns inga ytterligare riktlinjer, svaret innehåller URL: en för att öppna ett supportärende.  Mer information om egenskaperna för svaret och vad som ingår finns [nätverk Watcher Felsökning: översikt](network-watcher-troubleshoot-overview.md)


## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats som stoppa VPN-anslutning, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/virtual-network-manage-nsg-arm-portal.md) att spåra de grupp och säkerhet Nätverkssäkerhetsregler som kan vara i fråga.


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png
