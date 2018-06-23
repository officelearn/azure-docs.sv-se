---
title: Kom igång med API länka entiteten | Microsoft Docs
description: Analysera text och länka med namnet entiteter till relevanta poster i en kunskapsbas med entiteten länka API i kognition Services.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: article
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: 54c4a3bbb3637c248bd7705ed291633368b542c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351771"
---
# <a name="get-started-with-entity-linking-api-in-c35"></a>Kom igång med entiteten länka API i C&#35;

Länkning av Microsofts entiteten är ett verktyg för behandling av naturligt språk att analysera text och länka med namnet entiteter till relevanta poster i en knowledge base. 

Den här självstudiekursen utforskar entitet länkar med entiteten länka klientbiblioteket som ett NuGet paket. 

### <a name="Prerequisites">Förutsättningar</a>

- Visual Studio 2015
- En kognitiva Microsoft Services API-nyckel
- Installera klienten för bibliotek och exempel
- Microsoft-enhet länka NuGet-paketet

Du kan hämta entiteten länka Intelligence Service API klientbiblioteket via [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). Hämtade zip-filen måste extraheras till en valfri mapp, många användare väljer du mappen för Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Steg 1: Prenumerera entitet länka Intelligence-tjänsten och hämta din nyckel</a>
Innan du använder tjänsten för entiteten Intelligence koppling, måste du registrera dig för en API-nyckel. Se [prenumerationer](https://www.microsoft.com/cognitive-services/en-us/sign-up). Både den primära och sekundära nyckeln kan användas i den här självstudiekursen.

### <a name="step-2-create-a-new-project-in-visual-studio"> Steg 2: Skapa ett nytt projekt i Visual Studio</a>

Börja med att skapa ett nytt projekt i Visual Studio. Starta först, Visual Studio 2015 från Start-menyn. Skapa sedan ett nytt projekt genom att välja **installerat → mallar → Visual C# → Windows Universal → tom App** för projektmallen för:

 ![Skapa en universell app](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Steg 3: Lägg till entiteten länka NuGet-paket i projektet</a>

Entiteten länkning av kognitiva Services släpps som ett NuGet.org paket och måste installeras innan du kan använda den.
Om du vill lägga till den i projektet, gå till den **Solution Explorer** , högerklicka på projektet och välj **hantera Nuget-paket**.

Först i den **NuGet Package Manager** fönstret Välj NuGet.org som din **Paketkällan** i det övre högra hörnet. Välj **Bläddra** i det övre vänstra hörnet och i sökrutan skriver du ”ProjectOxford.EntityLinking”. Välj den **Microsoft.ProjectOxford.EntityLinking** NuGet-paketet och klickar på **installera**.

Sök sedan efter Newtonsoft.Json och installera. Om du uppmanas att granska ändringar, klickar du på **OK**. Om du ges EntityLinking licensvillkoren klickar du på **jag accepterar**.

Entiteten länka installeras som en del av ditt program. Du kan kontrollera detta genom att kontrollera som den ** Microsoft.ProjectOxford.EntityLinking** referens finns som en del av projektet i Solution Explorer.

 ![Med nuget-biblioteket i projektet](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Steg 4: Lägga till indata och utdata textblock till din app XAML</a>
Navigera till ** MainPage.xaml ** i **Solution Explorer**, dubbelklicka sedan på filen som ska öppnas i ett nytt fönster. Av praktiska skäl, du kan dubbelklicka på den **XAML** knappen i den **Designer** fliken döljs i **visuell Designer** och reservera allt utrymme för kodvyn.

 ![Med nuget-biblioteket i projektet](./Images/UWPMainPage.png)
 
Som en tjänst, är det bästa sättet att visualisera funktionen att skapa indata och ett textblock för utdata. Det gör du genom att lägga till följande XAML i den **rutnätet**. Den här koden lägger till tre komponenter, en inkommande textruta, ett utdata textblock och en start-knappen.
 
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
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Steg 5: Fortsätta att lägga till entiteten länka Intelligence Service</a>
 
Användargränssnittet har skapats. Innan du använder tjänsten entitet länkar, som vi behöver lägger du till hanteraren klickar på knappen. Öppna **MainPage.xaml** från **Solution Explorer**. Lägga till en hanterare för button_Click i slutet av knappen.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
En hanterare för klickar på knappen måste implementeras i koden. Öppna **MainPage.xaml.cs** från **Solution Explorer** att implementera klickar på knappen. EntityLinkingServiceClient är en Omslutning för att hämta entiteten länka svar. Argumentet konstruktorn för EntityLinkingServiceClient är nyckeln kognitiva tjänster prenumeration. Klistra in nyckeln prenumeration som du fick i **steg 1** att anropa tjänsten entitet länkning. 

Nedan visas exempelkod som lägger till ”wikipediaId” i svaret med hjälp av tjänsten för entiteten koppling. 
 
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
 
Nu är du redo att köra ditt första naturligt språk entitet länka App bearbetas. Tryck på den **F5-tangenten** att kompilera och starta programmet. Klistra in text kodavsnitt eller stycken i inmatningsrutan. Tryck på knappen ”Hämta resultatet” och notera identifierade enheter i utdatablock.
 
 ![Resultat för UWP-exempel](./Images/DemoCodeResult.png)
 
### <a name="summary">Sammanfattning</a>
 
Du har lärt dig hur du skapar ett program för att utnyttja entitet länka Intelligence Service klientbiblioteket med bara några få rader C# och XAML-kod i den här självstudiekursen. 

