---
title: Tal-loggning för SDK - Speech Services
titleSuffix: Azure Cognitive Services
description: 'Aktivera loggning i SDK: N för tal.'
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 75eaea22c4809eda78e54514961d13113b4a5f3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60696859"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Aktivera loggning i SDK: N för tal

Loggning till fil är en valfri funktion för tal-SDK. Loggning ger ytterligare information och diagnostik från Speeck SDK-kärnkomponenter under utvecklingen. Den kan aktiveras genom att ange egenskapen `Speech_LogFilename` på ett tal konfigurationsobjekt till platsen och namnet på loggfilen. Loggning aktiveras globalt när en identifierare som har skapats från den konfigurationen och kan inte inaktiveras efteråt. Du kan inte ändra namnet på en loggfil under en löpande loggning session.

> [!NOTE]
> Loggning är tillgänglig i alla stöds tal SDK programmeringsspråk, med undantag för JavaScript.

## <a name="sample"></a>Exempel

Loggfilens namn har angetts på ett konfigurationsobjekt. Tar de `SpeechConfig` som ett exempel och förutsatt att du har skapat en instans kallas `config`:

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

Du kan skapa en identifierare från config-objektet. Detta aktiverar loggning för alla identifierare.

> [!NOTE]
> Om du skapar en `SpeechSynthesizer` från konfigurationsobjekt, kommer den inte aktivera loggning. Om loggning är aktiverad men du får även diagnostik från den `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Skapa en loggfil på olika plattformar

Loggfilen kan vara i valfri sökväg som användaren har skrivbehörighet för för Windows eller Linux. Skrivbehörigheter till system sökvägar i andra operativsystem kan vara begränsad eller begränsade som standard.

### <a name="universal-windows-platform-uwp"></a>Universal Windows Platform (UWP)

Program för UWP måste vara platser loggfiler i någon av programmet data platser (lokal, centrala eller tillfällig). En loggfil kan skapas i mappen lokalt program:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Mer om filåtkomst behörigheten för UWP-program är tillgängliga [här](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Du kan spara en loggfil till intern lagring, extern lagring eller cache-katalogen. Filer som skapades i den interna lagringen eller cachekatalogen är privata för programmet. Det är bättre att skapa en loggfil i extern lagring.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Koden ovan sparar en loggfil till extern lagring i roten på en programspecifik katalog. En användare har åtkomst till filen med hanteraren (vanligtvis i `Android/data/ApplicationName/logfile.txt`). Filen tas bort när programmet har avinstallerats.

Du måste också begära `WRITE_EXTERNAL_STORAGE` behörighet i manifestfilen:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Mer om data- och lagring för Android-program är tillgängligt [här](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Endast kataloger i sandlådan program är tillgängliga. Filer kan skapas i dokument, bibliotek och temporära kataloger. Filer i katalogen dokument kan göras tillgängliga till en användare. Följande kodavsnitt visar skapandet av en loggfil i dokumentet programkatalogen:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Du kommer åt en skapad fil genom att lägga till den nedan egenskaper så att den `Info.plist` egenskapslistan av programmet:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Mer om iOS-filsystemet är tillgängliga [här](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)

