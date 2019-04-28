---
title: Hur du använder objektlagring (Blob) från Xamarin | Microsoft Docs
description: Azure Storage-klientbiblioteket för Xamarin kan utvecklare skapa iOS-, Android- och Windows Store-appar med sina interna användargränssnitt. Den här självstudien visar hur du använder Xamarin för att skapa ett program som använder Azure Blob storage.
services: storage
documentationcenter: xamarin
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: b9c707dcc1628f685661f88aaed29612465a5469
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098090"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Hur du använder Blob Storage från Xamarin

Xamarin kan utvecklare att använda en delad C# kodbas för att skapa iOS-, Android- och Windows Store-appar med sina interna användargränssnitt. Den här självstudien visar hur du använder Azure Blob storage med ett Xamarin-program. Om du vill veta mer om Azure Storage, innan du dyker in i koden i [introduktion till Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Skapa en ny Xamarin-App
Den här självstudien ska vi skapa en app som riktar sig till Android, iOS och Windows. Den här appen kommer bara att skapa en behållare och laddar upp en blob i den här behållaren. Vi ska använda Visual Studio på Windows, men samma erfarenheter kan användas när du skapar en app med Xamarin Studio i Mac OS.

Följ stegen nedan för att skapa ditt program:

1. Om du inte redan gjort det, ladda ned och installera [Xamarin för Visual Studio](https://www.xamarin.com/download).
2. Öppna Visual Studio och skapa en tom App (interna bärbar): **Fil > Nytt > Projekt > plattformsoberoende > tom App (interna Portable)**.
3. Högerklicka på lösningen i Solution Explorer-fönstret och välj **hantera NuGet-paket för lösningen**. Sök efter **WindowsAzure.Storage** och installera den senaste stabila versionen för alla projekt i din lösning.
4. Skapa och köra projektet.

Du bör nu ha ett program som gör att du kan klicka på en knapp som räknas en räknare.

## <a name="create-container-and-upload-blob"></a>Skapa behållaren och ladda upp blob
Sedan under din `(Portable)` -projektet lägger du till kod till `MyClass.cs`. Den här koden skapar en behållare och laddar upp en blob i den här behållaren. `MyClass.cs` bör se ut så här:

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

Se till att ersätta ”your_account_name_here” och ”your_account_key_here” med din faktiska kontonamnet och kontonyckeln. 

IOS-, Android- och Windows Phone-projekt som alla har referenser till din bärbara projekt – vilket innebär att du kan skriva all delad kod i något placera och använda det för alla dina projekt. Du kan nu lägga till följande kodrad till varje projektet för att börja utnyttja: `MyClass.performBlobOperation()`

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

## <a name="run-the-application"></a>Köra programmet
Du kan nu köra det här programmet i en Android eller Windows Phone-emulatorn. Du kan också köra det här programmet i en iOS-emulatorn, men detta kräver en Mac. För specifika anvisningar för hur du gör detta, Läs i dokumentationen för [ansluta Visual Studio till en Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

När du kör din app skapas behållaren `mycontainer` i ditt lagringskonto. Den ska innehålla blob, `myblob`, som har texten, `Hello, world!`. Du kan kontrollera detta genom att använda den [Microsoft Azure Lagringsutforskaren](https://storageexplorer.com/).

## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att skapa ett plattformsoberoende program i Xamarin som använder Azure Storage, särskilt fokuserad på ett scenario i Blob Storage. Men kan du göra mycket mer med inte bara Blob Storage, utan även med tabell-, fil- och Queue Storage. Kontrollera informationen i följande artiklar om du vill veta mer:

* [Komma igång med Azure Blob Storage med hjälp av .NET](storage-dotnet-how-to-use-blobs.md)
* [Introduktion till Azure Files](../files/storage-files-introduction.md)
* [Utveckla för Azure Files med .NET](../files/storage-dotnet-how-to-use-files.md)
* [Komma igång med Azure Table Storage med hjälp av .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Komma igång med Azure Queue Storage med hjälp av .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]