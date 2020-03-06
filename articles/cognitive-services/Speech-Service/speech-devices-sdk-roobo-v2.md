---
title: Tal enheter SDK Roobo Smart Audio dev kit v2 – tal service
titleSuffix: Azure Cognitive Services
description: Krav och anvisningar för att komma igång med tal enheter SDK, Roobo Smart Audio dev kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 5cf851bc9333004c0e14713cde44f470fb8c0c02
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304290"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Enhet: Roobo Smart Audio dev kit v2

Den här artikeln innehåller enhetsspecifika uppgifter för Roobo Smart Audio dev Kit2.

## <a name="set-up-the-development-kit"></a>Ställ in i development kit

1. Utvecklings paketet har två mikrousb-anslutningar. Den vänstra kopplingen är att sätta utvecklings paketet och är markerat som exponent i bilden nedan. Det högra är att kontrol lera den och har marker ATS som en fel sökning i avbildningen. 
    ![att ansluta till dev kit](media/speech-devices-sdk/roobo-v2-connections.png)
1. Utveckla utvecklings paketet genom att använda en mikrousb-kabel för att ansluta ström porten till en dator eller ett nätadapter. En grön energi indikator kommer att tändas under det översta brädet.
1. Om du vill styra utvecklings paketet ansluter du fel söknings porten till en dator genom att använda en andra Micro USB-kabel. Det är viktigt att använda en högkvalitativ kabel för att säkerställa tillförlitlig kommunikation.
1. Orientera ditt utvecklings paket cirkulärt upprättande med mikrofoner riktade mot taket som visas ovan


## <a name="development-information"></a>Utvecklings information

Mer utvecklings information finns i [utvecklings guiden för Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Ljud inspelning/uppspelning

DDK2 ljud åtgärder kan utföras på följande sätt:
* Använd ALSA bibliotek med öppen källkod och deras program.
* Använd appmainprog-gränssnittet för program utveckling. DDK2-programmet för ljudrelaterad program vara använder standard ramverket för ALSA. du kan använda libasound. Så att du kan utveckla program vara direkt. Så du kan använda ALSA arecord och APLAY direkt för att spela in och spela upp ljud.
