---
title: 'Självstudier: Skapa en Entity Linking-app – C#'
titlesuffix: Azure Cognitive Services
description: Analysera text och länka namngivna entiteter till relevanta poster i en kunskapsbas med hjälp av API för Entity Linking.
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: tutorial
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: fc1bdd5c6ad4829e22af9922c6749e60f842abaf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816598"
---
# <a name="tutorial-build-an-entity-linking-app-with-c"></a>Självstudier: Skapa en Entity Linking-app med C#

Microsofts Entity Linking är ett verktyg för bearbetning av naturligt språk som används för att analysera text och länka namngivna entiteter till relevanta poster i en kunskapsdatabas. 

Den här guiden utforskar entitetslänkning med hjälp av Entity Linking-klientbiblioteket som ett NuGet-paket. 

### <a name="Prerequisites">Förutsättningar</a>

- Visual Studio 2015
- En API-nyckel för Microsoft Cognitive Services
- Hämta klientbiblioteket och exempel
- Microsoft Entity Linking NuGet-paket

Du kan ladda ned Entity Linking Intelligence Service API Client Library via [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). Den nedladdade zip-filen måste extraheras till en mapp som du väljer. Många användare väljer mappen för Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Steg 1: Prenumerera på Entity Linking Intelligence Service och få din nyckel</a>
Innan du börjar använda Entity Linking Intelligence Service måste du registrera dig för en API-nyckel. Se [prenumerationer](https://www.microsoft.com/cognitive-services/en-us/sign-up). Både den primära och sekundära nyckeln kan användas i den här självstudien.

### <a name="step-2-create-a-new-project-in-visual-studio"> Steg 2: Skapa ett nytt projekt i Visual Studio</a>

Låt oss börja med att skapa ett nytt projekt i Visual Studio. Starta först Visual Studio 2015 från Start-menyn. Skapa sedan ett nytt projekt genom att välja **Installerat → Mallar → Visual C# → Windows Universal → Tom app** för din projektmall:

 ![Skapa en universell app](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Steg 3: Lägg till NuGet-paketet för Entity Linking till ditt projekt</a>

Entity Linking för Cognitive Services släpps som ett NuGet.org-paket och måste installeras innan du kan använda det.
Om du vill lägga till det i projektet går du till fliken **Solution Explorer**, högerklickar på projektet och väljer **Hantera Nuget-paket**.

Välj först NuGet.org som din **Paketkälla** i det övre högra hörnet i fönstret **NuGet-pakethanteraren**. Välj **Bläddra** i det övre vänstra hörnet och i skriv ”ProjectOxford.EntityLinking” i sökrutan. Välj NuGet-paketet **Microsoft.ProjectOxford.EntityLinking** och klicka på **Installera**.

Sök sedan efter Newtonsoft.Json och installera. Klicka på **OK** om du uppmanas att granska ändringarna. Om visas licensvillkoren för EntityLinking visas klickar du på **Jag accepterar**.

Entity Linking installeras som en del av ditt program. Du kan bekräfta detta genom att kontrollera att referensen ** Microsoft.ProjectOxford.EntityLinking** finns som en del av ditt projekt i Solution Explorer.

 ![Inkluderade nuget-biblioteket i projektet](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Steg 4: Lägg till in- och utdatatextblock i appens XAML</a>
Gå till **MainPage.xaml** i **Solution Explorer** och dubbelklicka på filen. Då öppnas den i ett nytt fönster. För enkelhetens skull kan du dubbelklicka på **XAML**-knappen på fliken **Designer**. Då döljs **Visuell designer** och reservera allt utrymme för kodvyn.

 ![Inkluderade nuget-biblioteket i projektet](./Images/UWPMainPage.png)
 
Det bästa sättet att visualisera funktionen som en tjänst är att skapa in- och utdatatextblock. Gör detta genom att lägga till följande XAML i **rutnätet**. Den här koden lägger till tre komponenter, en textruta för textinmatning, ett utdatatextblock och en startknapp.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Steg 5: Fortsätt med att lägga till Entity Linking Intelligence Service</a>
 
Användargränssnittet skapas nu. Innan du använder Entity Linking Service måste du lägga till knapptryckningshanteraren. Öppna **MainPage.xaml** från **Solution Explorer**. Lägg till en knapptryckningshanterare vid slutet av knappen.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
En knapptryckningshanterare måste implementeras i koden. Implementera knapptryckningen genom att öppna **MainPage.xaml.cs** från **Solution Explorer**. EntityLinkingServiceClient är en adapter för hämtning av Entity Linking-svar. Konstruktorargumentetet för EntityLinkingServiceClient är Cognitive Services-prenumerationsnyckeln. Klistra in prenumerationsnyckeln som du fick i **steg 1** för att anropa Entity Linking-tjänsten. 

Nedan visas exempelkod som lägger till ”wikipediaId” i svaret med hjälp av tjänsten Entity Linking. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
Nu är du redo att köra din första entitetslänkningsapp för bearbetning på naturligt språk. Kompilera och starta programmet genom att trycka på **F5**. Klistra in textkodfragment eller stycken i inmatningsrutan. Tryck på knappen Hämta resultat och notera de identifierade enheterna i utdatablocket.
 
 ![UWP-exempelresultat](./Images/DemoCodeResult.png)
 
### <a name="summary">Sammanfattning</a>
 
I den här självstudien har du lärt dig hur du skapar ett program för att utnyttja Entity Linking Intelligence Service Client Library med bara några rader av C#- och XAML-kod. 

