---
title: Tal SDK-loggning - Taltjänst
titleSuffix: Azure Cognitive Services
description: Läs mer om hur du aktiverar loggning i Tal-SDK (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805798"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Aktivera loggning i Tal-SDK

Att logga till fil är en valfri funktion för Tal-SDK. Under utvecklingsloggningen finns ytterligare information och diagnostik från Tal-SDK:s kärnkomponenter. Den kan aktiveras genom `Speech_LogFilename` att ange egenskapen på ett talkonfigurationsobjekt till loggfilens plats och namn. Loggning aktiveras globalt när en identifierare har skapats från den konfigurationen och inte kan inaktiveras efteråt. Du kan inte ändra namnet på en loggfil under en loggningssession som körs.

> [!NOTE]
> Loggning är tillgänglig sedan Speech SDK version 1.4.0 i alla tal SDK-programmeringsspråk som stöds, med undantag för JavaScript.

## <a name="sample"></a>Exempel

Loggfilsnamnet anges på ett konfigurationsobjekt. Tar `SpeechConfig` som ett exempel och förutsatt att du `config`har skapat en instans som heter:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Du kan skapa en identifierare från config-objektet. Detta gör det möjligt att logga för alla identifierare.

> [!NOTE]
> Om du `SpeechSynthesizer` skapar en från config-objektet aktiveras inte loggning. Om loggning är aktiverat får du dock även `SpeechSynthesizer`diagnostik från .

## <a name="create-a-log-file-on-different-platforms"></a>Skapa en loggfil på olika plattformar

För Windows eller Linux kan loggfilen vara i vilken väg som helst som användaren har skrivbehörighet för. Skrivbehörigheter till filsystemplatser i andra operativsystem kan vara begränsade eller begränsade som standard.

### <a name="universal-windows-platform-uwp"></a>Universal Windows Platform (UWP)

UWP-program måste placeras i loggfiler på en av programdataplatserna (lokal, roaming eller tillfällig). En loggfil kan skapas i den lokala programmappen:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Mer information om filåtkomstbehörighet för UWP-program finns [här](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Du kan spara en loggfil i antingen intern lagring, extern lagring eller cachekatalogen. Filer som skapas i den interna lagringen eller cachekatalogen är privata för programmet. Det är att föredra att skapa en loggfil i extern lagring.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Koden ovan sparar en loggfil i den externa lagringen i roten till en programspecifik katalog. En användare kan komma åt filen med `Android/data/ApplicationName/logfile.txt`filhanteraren (vanligtvis i ). Filen tas bort när programmet avinstalleras.

Du måste också `WRITE_EXTERNAL_STORAGE` begära behörighet i manifestfilen:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Mer om data och fillagring för Android-program finns [här](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Endast kataloger i programsandlådan är tillgängliga. Filer kan skapas i dokument-, biblioteks- och tempkatalogerna. Filer i dokumentkatalogen kan göras tillgängliga för en användare. Följande kodavsnitt visar skapandet av en loggfil i programdokumentkatalogen:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Om du vill komma åt en `Info.plist` skapad fil lägger du till egenskaperna nedan i programmets egenskapslista:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Mer om iOS File System finns [här](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)
