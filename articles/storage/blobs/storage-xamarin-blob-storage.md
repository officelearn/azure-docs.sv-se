---
title: Så här använder du objektlagring (Blob) från Xamarin | Microsoft-dokument
description: Azure Storage-klientbiblioteket för Xamarin gör det möjligt för utvecklare att skapa iOS-, Android- och Windows Store-appar med sina inbyggda användargränssnitt. Den här självstudien visar hur du använder Xamarin för att skapa ett program som använder Azure Blob-lagring.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8a1c91c8a8a59af26386e70e68e7c4fd93f5eaa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726345"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Så här använder du Blob Storage från Xamarin

Xamarin gör det möjligt för utvecklare att använda en delad C#-kodbas för att skapa iOS-, Android- och Windows Store-appar med sina inbyggda användargränssnitt. Den här självstudien visar hur du använder Azure Blob-lagring med ett Xamarin-program. Om du vill veta mer om Azure Storage läser du [Introduktion till Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)innan du dyker in i koden .

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Skapa ett nytt Xamarin-program

För den här självstudien skapar vi en app som riktar sig till Android, iOS och Windows. Denna app kommer helt enkelt att skapa en behållare och ladda upp en blob i denna behållare. Vi kommer att använda Visual Studio i Windows, men samma lärdomar kan användas när du skapar en app med Xamarin Studio på macOS.

Så här skapar du programmet:

1. Om du inte redan har gjort det kan du hämta och installera [Xamarin för Visual Studio](https://www.xamarin.com/download).
2. Öppna Visual Studio och skapa en tom app (Inbyggd bärbar): **Fil > Nytt > Projekt > plattformsoberoende > Tom App(Native Portable).**
3. Högerklicka på lösningen i fönstret Lösningsutforskaren och välj **Hantera NuGet-paket för lösning**. Sök efter **WindowsAzure.Storage** och installera den senaste stabila versionen för alla projekt i din lösning.
4. Bygg och kör ditt projekt.

Du bör nu ha ett program som låter dig klicka på en knapp som ökar en räknare.

## <a name="create-container-and-upload-blob"></a>Skapa behållare och ladda upp blob

Under projektet `(Portable)` lägger du sedan till `MyClass.cs`lite kod i . Den här koden skapar en behållare och överför en blob till den här behållaren. `MyClass.cs`bör se ut så här:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Se till att ersätta "your_account_name_here" och "your_account_key_here" med ditt faktiska kontonamn och kontonyckel.

Dina iOS-, Android- och Windows Phone-projekt har alla referenser till ditt bärbara projekt , vilket innebär att du kan skriva all din delade kod på ett ställe och använda den i alla dina projekt. Du kan nu lägga till följande kodrad i varje projekt för att börja dra nytta:`MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Köra appen

Du kan nu köra det här programmet i en Android- eller Windows Phone-emulator. Du kan också köra det här programmet i en iOS-emulator, men det kräver en Mac. För specifika instruktioner om hur du gör detta, läs dokumentationen för [att ansluta Visual Studio till en Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

När du har kört appen skapas behållaren `mycontainer` i ditt lagringskonto. Den ska innehålla blobben `myblob`, som `Hello, world!`har texten. Du kan verifiera detta med hjälp av [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du skapar ett plattformsoberoende program i Xamarin som använder Azure Storage, med fokus på ett scenario i Blob Storage. Du kan dock göra mycket mer med inte bara Blob Storage, men också med Tabell, Fil och Kölagring. Läs följande artiklar om du vill veta mer:

* [Komma igång med Azure Blob Storage med hjälp av .NET](storage-dotnet-how-to-use-blobs.md)
* [Introduktion till Azure Files](../files/storage-files-introduction.md)
* [Utveckla för Azure Files med .NET](../files/storage-dotnet-how-to-use-files.md)
* [Komma igång med Azure Table Storage med hjälp av .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Komma igång med Azure Queue storage med .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
