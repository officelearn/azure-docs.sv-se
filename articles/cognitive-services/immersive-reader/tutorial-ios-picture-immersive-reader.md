---
title: 'Självstudiekurs: Skapa en iOS-app som tar ett foto och startar den i Immersive Reader (Swift)'
titleSuffix: Azure Cognitive Services
description: I den här självstudien ska du skapa en iOS-app från grunden och lägga till funktionen Bild i Immersive Reader.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 69ff58d6cdabe49000b00afecfc6b4ad1a3f2daa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841854"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Självstudiekurs: Skapa en iOS-app som startar Immersive Reader med innehåll från ett foto (Swift)

[Den uppslukande läsaren](https://www.onenote.com/learningtools) är ett inkluderande utformat verktyg som implementerar beprövade tekniker för att förbättra läsförståelsen.

[Läs-API:et för datorseende cognitive services](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) identifierar textinnehåll i en bild med hjälp av Microsofts senaste igenkänningsmodeller och konverterar den identifierade texten till en maskinläsbar teckenström.

I den här självstudien kommer du att bygga en iOS-app från grunden och integrera Läs-API: et och Immersive Reader med hjälp av Immersive Reader SDK. Ett fullständigt fungerande urval av den här självstudien finns [här](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* En Immersive Reader-resurs som konfigurerats för Azure Active Directory-autentisering. Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att komma igång. Du behöver några av de värden som skapas här när du konfigurerar exempelprojektegenskaperna. Spara utdata från sessionen i en textfil för framtida referens.
* Användning av det här exemplet kräver en Azure-prenumeration på Cognitive Service för datorseende. [Skapa en cognitivetjänstresurs för datorseende i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Skapa ett nytt projekt i Xcode.

![Nytt projekt](./media/ios/xcode-create-project.png)

Välj **App med enkel vy**.

![Ny app med en vy](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Skaffa SDK CocoaPod
Det enklaste sättet att använda Immersive Reader SDK är via CocoaPods. Så här installerar du via Cocoapods:
1. [Installera CocoaPods](http://guides.cocoapods.org/using/getting-started.html) - Följ komma igång guide för att installera Cocoapods.
2. Skapa en Podfile `pod init` genom att köras i Xcode-projektets rotkatalog.
3.  Lägg till CocoaPod till din `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`Podfile genom att lägga till . Podfilen ska se ut så här, med ditt måls namn som ersätter bild-till-uppslukande läsare-swift:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. I terminalen, i katalogen för ditt Xcode-projekt, kör du kommandot `pod install` för att installera Immersive Reader SDK-podden.
5. Lägg `import immersive_reader_sdk` till alla filer som behöver referera till SDK.
6. Se till att öppna `.xcworkspace` projektet genom `.xcodeproj` att öppna filen och inte filen.

## <a name="acquire-an-azure-ad-authentication-token"></a>Hämta en Azure AD-autentiseringstoken

Du behöver några värden från azure AD-autentiseringskonfigurationen förutsättningen steg ovan för den här delen. Gå tillbaka till textfilen som du sparade av sessionen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Skapa en Swift-klassfil som heter Constants.swift i huvudprojektmappen, som innehåller filen ViewController.swift. Ersätt klassen med följande kod och lägg till i dina värden där så är tillämpligt. Behåll den här filen som en lokal fil som bara finns på din dator och se till att inte arkivera den här filen i källkontrollen, eftersom den innehåller hemligheter som inte bör offentliggöras. Vi rekommenderar att du inte har hemligheter i appen. I stället rekommenderar vi att du använder en backend-tjänst för att hämta token, där hemligheterna kan hållas utanför appen och bort från enheten. Serverdels-API-slutpunkten bör skyddas bakom någon form av autentisering (till exempel [OAuth)](https://oauth.net/2/)för att förhindra att obehöriga användare får token att använda mot din Immersive Reader-tjänst och fakturering. att arbetet är utanför ramen för den här självstudien.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Konfigurera appen så att den körs utan storyboard

Öppna AppDelegate.swift och ersätt filen med följande kod.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Lägga till funktioner för att ta och ladda upp foton

Byt namn på ViewController.swift till PictureLaunchViewController.swift och ersätt filen med följande kod.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Ställ in arkivschemat i Xcode genom att välja en simulator eller enhetsmål.
![Arkivschema](./media/ios/xcode-archive-scheme.png)<br/>
![Välja mål](./media/ios/xcode-select-target.png)

I Xcode trycker du på Ctrl + R eller klickar på uppspelningsknappen för att köra projektet och appen ska starta på den angivna simulatorn eller enheten.

I din app bör du se:

![Exempelapp](./media/ios/picture-to-immersive-reader-ipad-app.png)

Inuti appen, ta eller ladda upp ett foto av text genom att trycka på "Ta foto" knappen eller "Välj bild från biblioteket" knappen och Immersive Reader kommer sedan att starta visar texten från bilden.

![Avancerad läsare](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Nästa steg

* Utforska [den uppslukande läsar-SDK-referensen](./reference.md)
