---
title: Översikt över Azure Media Player
description: Läs mer om Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726524"
---
# <a name="azure-media-player-overview"></a>Översikt över Azure Media Player #

Azure Media Player är en webb Videos pelare som skapats för att spela upp medie innehåll från [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) på en rad olika webbläsare och enheter. Azure Media Player använder bransch standarder, till exempel HTML5, media source Extensions (MSE) och EME (Encrypted Media Extensions) för att ge en omfattande anpassningsbar strömmande upplevelse.  När dessa standarder inte är tillgängliga på en enhet eller i en webbläsare, använder Azure Media Player Flash och Silverlight som reserv teknik. Oavsett vilken uppspelnings teknik som används har utvecklare ett enhetligt JavaScript-gränssnitt för att få åtkomst till API: er.  Detta gör att innehåll som hanteras av Azure Media Services kan spelas upp i en mängd olika enheter och webbläsare utan extra ansträngning.

Microsoft Azure Media Services gör att innehåll kan hanteras med bindestreck, Smooth Streaming och HLS strömnings format för att spela upp innehåll. Azure Media Player tar hänsyn till dessa olika format och spelar automatiskt upp den bästa länken utifrån plattforms-och webb läsar funktionerna. Microsoft Azure Media Services också tillåta dynamisk kryptering av till gångar med common Encryption (PlayReady eller Widevine) eller AES-128 bitars kryptering av kuvert. Azure Media Player tillåter dekryptering av PlayReady-och AES-128-bitars krypterat innehåll när det är lämpligt konfigurerat.  Mer information om hur du konfigurerar detta finns i avsnittet [skyddat innehåll](azure-media-player-protected-content.md) .

För att begära nya funktioner, ge idéer eller feedback, skicka till [UserVoice för Azure Media Player](https://aka.ms/ampuservoice). Om du har och specifika problem, frågor eller hittar några buggar, släpp vi oss på ampinfo@microsoft.com.

[Registrera dig](https://aka.ms/ampsignup) för att aldrig sakna en lansering och håll dig uppdaterad med den senaste versionen av Azure Media Player har att erbjuda.

> [!NOTE]
> Observera att Azure Media Player endast stöder medie strömmar från Azure Media Services.

## <a name="license"></a>Licens ##

Azure Media Player licensieras och omfattas av villkoren som beskrivs i licens villkoren för program vara från Microsoft för Azure Media Player. Se [licens filen](azure-media-player-license.md) för fullständiga villkor. Mer information finns i [Sekretess policyn](https://www.microsoft.com/en-us/privacystatement/default.aspx) .

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)