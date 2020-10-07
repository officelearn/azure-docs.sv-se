---
title: Uppspelning av flera dagars inspelningar – Azure
description: 'I den här självstudien får du lära dig hur du använder Azure Media Service-API: er för att spela upp en kontinuerlig video inspelning på flera dagar.'
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 7641114dbae5a118937e7f4973092d5f0eadbccc
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776382"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>Självstudie: uppspelning av flera dagars inspelningar  

Den här självstudien bygger på CVR-självstudien ( [kontinuerlig video inspelning](continuous-video-recording-concept.md) ). I den här självstudien får du lära dig hur du använder Azure Media Service-API: er för att spela upp en löpande video inspelning på flera dagar från molnet. 

Detta är användbart för scenarier som allmän säkerhet, där det finns behov av att underhålla en inspelning av tagningen från en kamera i flera dagar (eller veckor) och det finns ett tillfälligt behov av att se vissa delar av denna tagning.

Självstudien visar hur du:

> [!div class="checklist"]
> * Kör exempel appen som visar hur du kan bläddra i innehållet i en multi-dagars inspelning
> * Visa valda delar av den inspelningen

## <a name="suggested-pre-reading"></a>Föreslagen för läsning  

Vi rekommenderar att du läser igenom följande dokumentations sidor:

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)
* [Media diagram-koncept](media-graph-concept.md)
* [Kontinuerlig videoinspelning](continuous-video-recording-concept.md) 
* [Instruktions guide: uppspelning av inspelningar](playback-recordings-how-to.md)
* [Självstudie: kontinuerlig videoinspelning](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>Krav

* Slutför [själv studie kursen om CVR](continuous-video-recording-tutorial.md). Den här själv studie kursen och relevanta API: er som diskuteras i [Självstudier: kontinuerlig videoinspelning](continuous-video-recording-tutorial.md) gäller för inspelningar som är 5 minuter eller längre, men vi rekommenderar att du spelar in 5 timmars video, om inte mer. De API: er som används för att bläddra i inspelningar visas bäst med långa inspelningar.
* Vi rekommenderar att du kör den här självstudien medan [självstudien: kontinuerlig](continuous-video-recording-tutorial.md) inspelning av video fortfarande körs – det vill säga du spelar fortfarande in video till molnet.

## <a name="run-the-sample"></a>Kör exemplet 

Som en del av [självstudien om CVR](continuous-video-recording-tutorial.md)skulle du ha skapat ett media service-konto. I den här självstudien måste du ha fullständig API-åtkomst till det kontot. Du kan använda stegen i [Hämta autentiseringsuppgifter för att komma åt Media Services API](../latest/access-api-howto.md?tabs=portal) för att skapa ett huvud namn för tjänsten. Du bör kunna hämta ett JSON-block från Azure Portal som ser ut så här:

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

Gå sedan till Visual Studio Code och öppna src/AMS-Asset-Player. Den här mappen innehåller de filer som krävs för den här självstudien. Öppna filen appsettings.jsoch kopiera innehållet till en ny fil appsettings.development.jspå. Gör följande ändringar i den senare filen:

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

1. Öppna fliken **tillägg** i Visual Studio Code (eller tryck på Ctrl + Shift + X) och Sök efter Azure-IoT Hub.
1. Högerklicka och välj **Inställningar för tillägg**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Tilläggs inställningar":::
1. Sök och aktivera "Visa utförligt meddelande".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Tilläggs inställningar" är markerad.

![Dataström](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
På höger sida av spelaren kan du se kontrollerna för att bläddra i arkivet. Åren, månaderna och datumen i den här kontrollen fylls med hjälp av availableMedia-API: t som dokumenteras i [instruktions boken: uppspelning av inspelningar](playback-recordings-how-to.md).
Om du har till gång till CVR-självstudierna i flera timmar när du utökar dagen visas ett resultat som detta:

![Bläddra i Arkiv](./media/playback-multi-day-recordings-tutorial/results.png)

Källan för video flödet i självstudien är en MKV-fil. När RSTP Simulator (se [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) når slutet av filen, avslutar den strömmen. Noden RTSP-källa i medie diagrammet identifierar detta och upprättar anslutningen och videon återupptas. I mellan varje sådant filslut och återanslut, finns det ett mellanrum i det inspelade arkivet, som visas som en ny post i availableMedia resultat.

![Resultat](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
När du klickar på en post i listan skapar programmet en strömmande URL med lämpligt filter, till exempel https://{hostname}/{locatorId}/Content. ISM/manifest (format = mpd-Time-CSF, StartTime = ÅÅÅÅ-MM-DDTHH: MM: SS). Spelaren läser in URL: en och uppspelningen börjar vid önskad StartTime.

Du kan också använda särskilda start-och slut tider via kontrollerna längst ned på sidan. Du kan använda resultatet från availableMedia-anropet, som i listan till höger, som en vägledning för de tillåtna värdena för start-och slut tider. De detaljerade begränsningarna för StartTime-och slut tid filtren dokumenteras i [instruktions guiden: uppspelning av inspelningar](playback-recordings-how-to.md). När du har valt tids värden, när du klickar på Skicka laddar programmet upp spelaren med en strömmande URL som: https://{hostname}/{locatorId}/Content. ISM/manifest (format = mpd-Time-CSF, StartTime = ÅÅÅÅ-MM-DDTHH: MM: SS, slut tid = ÅÅÅÅ-MM-DDTHH: MM: SS) uppspelningen påbörjas vid önskad StartTime.

## <a name="next-steps"></a>Nästa steg

[Event-baserad videoinspelning till molnet och uppspelningen från molnet](event-based-video-recording-tutorial.md)
