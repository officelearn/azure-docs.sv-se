---
title: Azure Mobile Engagement iOS SDK viktig information | Microsoft Docs
description: Senaste uppdateringarna och procedurer för iOS SDK för Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 0268d65d2c0ea3cefeb8f06793838bc263e443bd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Azure Mobile Engagement iOS SDK viktig information
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 


## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Fast Aktivitetsikoner avmarkerad i bakgrunden.
* Fast varningar på XCode 9 om API: er som inte anropas i huvudkön.
* Fast en minnesläcka i Reach-avsökningar.
* Bort stöd för iOS 6.X. Startar från den här versionen av distributionsmålet för ditt program måste vara minst iOS 7.

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* Bättre loggen leverans i bakgrunden.

## <a name="400-09122016"></a>4.0.0 (09/12/2016)
* Fast meddelande inte åtgärdade på iOS 10-enheter.
* Föråldrad XCode 7.

## <a name="324-06302016"></a>3.2.4 (06/30/2016)
* Fast aggregering mellan tekniska loggar och andra loggar.

## <a name="323-06072016"></a>3.2.3 (06/07/2016)
* Fast programfelet där leverans av feedback har inte rapporterats om appen i bakgrunden.
* Optimerad skickar tekniska loggar.

## <a name="322-04072016"></a>3.2.2 (04/07/2016)
* Fast programfel på http-begäran annullering, vilket ibland leder till krascher.

## <a name="321-12112015"></a>3.2.1 (12/11/2015)
* Fast fördröjningen när en ny instans av appen utlöses av ett meddelande med djuplänkar

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* Aktivera Bitcode i SDK, för att få det att fungera med **Xcode 7**.
* Fast buggar som är relaterade till meddelanden i appen.
* Göras i appen meddelanden mer tillförlitlig vid låg batterinivå och andra sådana scenarier.
* Ta bort extra konsolen loggar som genereras av 3 part-biblioteket.

## <a name="310-08262015"></a>3.1.0 (08/26/2015)
* Åtgärda fel för iOS 9-kompatibilitet med tredjeparts-biblioteket. Den orsakar krascher medan skickar avsöker resultat, information om programmet eller extra data.

## <a name="300-06192015"></a>3.0.0 (06/19/2015)
* Mobile Engagement använder tysta Push-meddelanden.
* Bort stöd för iOS 4.X. Startar från den här versionen av distributionsmålet för ditt program måste vara minst iOS 6.

## <a name="220-05212015"></a>2.2.0 (05/21/2015)
* Mobile Engagement-enhets-id för enheter < iOS 6 nu baserat på ett GUID som genereras vid tidpunkten för installationen.

## <a name="210-04242015"></a>2.1.0 (04/24/2015)
* Tillagda Swift kompatibilitet.
* När du klickar på ett meddelande, köra instruktionen URL: en är nu höger när programmet har öppnats.
* Tillagda saknas huvudfilen i SDK-paketet.
* Ett problem har åtgärdats när Mobile Engagement krasch rapport har inaktiverats.

## <a name="200-02172015"></a>2.0.0 (02/17/2015)
* Första versionen av Azure Mobile Engagement
* konfiguration av appId/sdkKey ersätts av en sträng anslutningskonfiguration.
* Ta bort API för att skicka och ta emot godtycklig XMPP meddelanden från valfri XMPP entiteter.
* Ta bort API för att skicka och ta emot meddelanden mellan enheter.
* Förbättringar av säkerhet.
* Spårning av SmartAd tas bort.
