---
title: Översikt över Azure Media Player
description: Läs mer om Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726524"
---
# <a name="azure-media-player-overview"></a>Azure Media Player-översikt #

Azure Media Player är en webbvideospelare som är byggd för att spela upp medieinnehåll från [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) på en mängd olika webbläsare och enheter. Azure Media Player använder branschstandarder, till exempel HTML5, MSE (Media Source Extensions) och EME (Encrypted Media Extensions) för att ge en berikad adaptiv direktuppspelningsupplevelse.  När dessa standarder inte är tillgängliga på en enhet eller i en webbläsare använder Azure Media Player Flash och Silverlight som reservteknik. Oavsett vilken uppspelningsteknik som används har utvecklare ett enhetligt JavaScript-gränssnitt för åtkomst till API:er.  Detta gör det möjligt att spela upp innehåll som betjänas av Azure Media Services på en mängd olika enheter och webbläsare utan extra ansträngning.

Med Microsoft Azure Media Services kan innehåll visas med dash-, Smooth Streaming- och HLS-streamingformat för att spela upp innehåll. Azure Media Player tar hänsyn till dessa olika format och spelar automatiskt den bästa länken baserat på plattforms-/webbläsarfunktionerna. Microsoft Azure Media Services möjliggör också dynamisk kryptering av tillgångar med gemensam kryptering (PlayReady eller Widevine) eller AES-128-bitars kuvertkryptering. Azure Media Player möjliggör dekryptering av PlayReady och AES-128-bitars krypterat innehåll när det är korrekt konfigurerat.  I avsnittet Skyddat innehåll finns i avsnittet [Skyddat innehåll](azure-media-player-protected-content.md) för hur du konfigurerar detta.

Om du vill begära nya funktioner, ge idéer eller feedback skickar du till [UserVoice för Azure Media Player](https://aka.ms/ampuservoice). Om du har och specifika frågor, frågor eller hitta ampinfo@microsoft.comnågra buggar, släpp oss en linje på .

[Registrera dig](https://aka.ms/ampsignup) för att aldrig missa en version och för att hålla dig uppdaterad med det senaste som Azure Media Player har att erbjuda.

> [!NOTE]
> Observera att Azure Media Player endast stöder medieströmmar från Azure Media Services.

## <a name="license"></a>Licens ##

Azure Media Player är licensierat och omfattas av de villkor som beskrivs i Licensvillkoren för programvara från Microsoft för Azure Media Player. Se [licensfilen](azure-media-player-license.md) för fullständiga villkor. Mer information finns i [sekretesspolicyn.](https://www.microsoft.com/en-us/privacystatement/default.aspx)

Upphovsrätt 2015 Microsoft Corporation.

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)