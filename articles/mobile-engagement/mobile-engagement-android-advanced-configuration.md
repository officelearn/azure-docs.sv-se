---
title: "Avancerad konfiguration för Azure Mobile Engagement Android SDK"
description: Beskriver de avancerade konfigurationsalternativ inklusive Android Manifest med Azure Mobile Engagement Android SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 0301f71c76872714aa1bf727a6c21dd7a63db036
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Avancerad konfiguration för Azure Mobile Engagement Android SDK
> [!div class="op_single_selector"]
> * [Universell Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

Den här proceduren beskriver hur du konfigurerar olika konfigurationsalternativ för Azure Mobile Engagement Android-appar.

## <a name="prerequisites"></a>Krav
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Behörighet som krävs
Vissa alternativ kräver särskilda behörigheter, som anges här referens- och rad i den specifika funktionen. Lägg till följande behörigheter AndroidManifest.xml i ditt projekt omedelbart före eller efter den `<application>` tagg.

Behörighet-koden måste ska se ut som följande, där du fyller i behörighet från tabellen som följer.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Behörighet | När den används |
| --- | --- |
| INTERNET |Krävs. För grundläggande rapportering |
| ACCESS_NETWORK_STATE |Krävs. För grundläggande rapportering |
| RECEIVE_BOOT_COMPLETED |Krävs. Meddelanden center visas efter omstart av enheten |
| WAKE_LOCK |Rekommenderas. Gör det möjligt att samla in data när du använder Wi-Fi eller när skärmen är inaktiverat |
| VIBRERAR |Valfri. Aktiverar vibration när meddelanden tas emot |
| DOWNLOAD_WITHOUT_NOTIFICATION |Valfri. Aktiverar Android helheten meddelanden |
| WRITE_EXTERNAL_STORAGE |Valfri. Aktiverar Android helheten meddelanden |
| ACCESS_COARSE_LOCATION |Valfri. Aktiverar rapportering i realtid plats |
| ACCESS_FINE_LOCATION |Valfri. Aktiverar GPS-baserad plats reporting |

Från och med Android M [vissa behörigheter hanteras vid körning](mobile-engagement-android-location-reporting.md#android-m-permissions).

Om du redan använder ``ACCESS_FINE_LOCATION``, inte måste du också använda ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Android Manifest konfigurationsalternativ
### <a name="crash-report"></a>Kraschrapport
Om du vill inaktivera kraschrapporter, lägger du till den här koden mellan den `<application>` och `</application>` taggar:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Burst tröskelvärde
Som standard loggas Engagement service-rapporter i realtid. Om din rapport programloggarna ofta varierar, är det bättre att buffra loggarna och rapportera dem på en gång i en grundläggande reguljära tid (kallas ”burst läge”). Gör du genom att lägga till den här koden mellan den `<application>` och `</application>` taggar:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Burst läge något ökar för batterilivslängd men påverkar Engagement-Övervakare: alla sessioner och jobb varaktighet avrundas till burst tröskelvärdet (alltså sessioner och jobb som är kortare än tröskelvärdet burst inte kanske visas). Burst-tröskelvärde ska vara längre än 30000 (30s).

### <a name="session-timeout"></a>Tidsgräns för session
 Du kan avsluta en aktivitet genom att trycka på **Start** eller **tillbaka** nyckel genom att ange telefonnummer som inaktiv eller genom att hoppa över i ett annat program. Som standard avslutas session tio sekunder efter den senaste aktiviteten. Detta förhindrar en session delning varje gång användaren avslutar och återgår till programmet snabbt, vilket kan hända när användaren tar upp en bild, kontrollerar en avisering, osv. Du kanske vill ändra den här parametern. Gör du genom att lägga till den här koden mellan den `<application>` och `</application>` taggar:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Inaktivera rapportering av logg
### <a name="using-a-method-call"></a>Med hjälp av ett metodanrop
Om du vill Engagement slutar skicka loggar, kan du anropa:

    EngagementAgent.getInstance(context).setEnabled(false);

Det här anropet är beständiga: filen delade inställningar används.

Det kan ta en minut att stoppa tjänsten om Engagement är aktiv när du anropar den här funktionen. Men det inte kommer starta tjänsten alls nästa gång startar du programmet.

Du kan aktivera loggen reporting igen genom att anropa funktionen samma med `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integrering i din egen`PreferenceActivity`
I stället för att anropa den här funktionen kan du också integrera inställningen direkt i din befintliga `PreferenceActivity`.

Du kan konfigurera Engagement att använda inställningar-fil (med läge) i den `AndroidManifest.xml` filen med `application meta-data`:

* Den `engagement:agent:settings:name` nyckel används för att ange namnet på filen med delade inställningar.
* Den `engagement:agent:settings:mode` nyckel används för att definiera läget för filen delade inställningar. Använd samma läge som i din `PreferenceActivity`. Läget måste skickas som ett tal: Om du använder en kombination av konstant flaggor i koden, kontrollera det totala värdet.

Engagement alltid använder den `engagement:key` booleskt nyckel i filen med inställningar för att hantera den här inställningen.

Följande exempel visar `AndroidManifest.xml` visar standardvärden:

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

Du kan lägga till en `CheckBoxPreference` i layouten inställningar som den följande:

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />
