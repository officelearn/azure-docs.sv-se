---
title: Talenheter SDK Roobo Smart Audio Dev Kit v2 - Taltjänst
titleSuffix: Azure Cognitive Services
description: Förutsättningar och instruktioner för att komma igång med Speech Devices SDK, Roobo Smart Audio Dev Kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371585"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Enhet: Roobo Smart Audio Dev Kit v2

Den här artikeln innehåller enhetsspecifik information för Roobo Smart Audio Dev Kit2.

## <a name="set-up-the-development-kit"></a>Ställ in utvecklingspaketet

1. Utvecklingssatsen har två mikro-USB-kontakter. Den vänstra kontakten är att driva utvecklingssatsen och markeras som Power i bilden nedan. Den rätta är att kontrollera den, och är märkt Debug i bilden. 
    ![Ansluta utvecklingspaketet](media/speech-devices-sdk/roobo-v2-connections.png)
1. Ström av utvecklingssatsen genom att använda en mikro-USB-kabel för att ansluta strömporten till en dator eller strömadapter. En grön strömindikator tänds under den övre brädan.
1. Om du vill styra utvecklingspaketet ansluter du felsökningsporten till en dator med hjälp av en andra mikro-USB-kabel. Det är viktigt att använda en högkvalitativ kabel för att säkerställa tillförlitlig kommunikation.
1. Orientera utvecklingspaketet Cirkulärt - Upprätt, med mikrofoner mot taket som visas ovan


## <a name="development-information"></a>Utvecklingsinformation

Mer utvecklingsinformation finns i [Roobos utvecklingsguide](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Ljudinspelning/uppspelning

DDK2-ljudåtgärder kan utföras på följande sätt:
* Använd ALSA Open-source-bibliotek och deras program.
* Använd appmainprog-gränssnittet för att göra programutveckling. DDK2 audio - relaterade programramverk använder standard ALSA ram, kan du använda libasound. Så för att utveckla programvara direkt. Så du kan använda ALSA:s ärspelade och aplay direkt för att spela in och spela upp ljud.
