---
title: Distribuera och hantera Notification Hubs med PowerShell
description: Skapa och hantera Notification Hubs med PowerShell för Automation
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 4dbbaeea736dd46478ad9992201ea28bd7bfc2ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855511"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Distribuera och hantera notification hubs med PowerShell

## <a name="overview"></a>Översikt

Den här artikeln visar hur du använder skapa och hantera Azure Notification Hubs med hjälp av PowerShell. Följande vanliga automatiseringsaktiviteter visas i den här artikeln.

- Skapa en meddelandehubb
- Ange autentiseringsuppgifter

Om du måste också skapa en ny service bus-namnområde för notification Hub, se [hantera Service Bus med PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Hantera Meddelandehubbar stöds inte direkt av de cmdletar som ingår med Azure PowerShell. Det bästa sättet från PowerShell är att referera till Microsoft.Azure.NotificationHubs.dll-sammansättningen. Sammansättningen distribueras med den [Microsoft Azure Notification Hubs NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Azure är en prenumeration-baserad plattform. Läs mer om hur du skaffar en prenumeration, [köpalternativ], [Medlemserbjudanden], eller [kostnadsfri utvärderingsversion].
- En dator med Azure PowerShell. Anvisningar finns i [installera och konfigurera Azure PowerShell].
- En allmän förståelse för PowerShell-skript, NuGet-paket och .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>En referens till .NET-sammansättning för Service Bus

Hantera Azure Notification Hubs ingår inte ännu i PowerShell-cmdletar i Azure PowerShell. För att etablera meddelandehubbar, du kan använda .NET-klient som anges i den [Microsoft Azure Notification Hubs NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Kontrollera först att skriptet kan hitta den **Microsoft.Azure.NotificationHubs.dll** paket som är installerat som NuGet-paket i ett Visual Studio-projekt. För att vara flexibel utför skriptet de här stegen:

1. Anger sökvägen där den anropades.
2. Passerar sökvägen tills den hittar en mapp med namnet `packages`. Den här mappen skapas när du installerar NuGet-paket för Visual Studio-projekt.
3. Rekursivt söker den `packages` mapp för en sammansättning med namnet `Microsoft.Azure.NotificationHubs.dll`.
4. Refererar till sammansättningen så att typerna som är tillgängliga för senare användning.

Här är hur de här stegen implementeras i ett PowerShell-skript:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Skapa den `NamespaceManager` klass

För att etablera Meddelandehubbar, skapa en instans av den [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) klass från SDK.

Du kan använda den [Get-AzureSBAuthorizationRule] cmdlet som ingår i Azure PowerShell för att hämta en auktoriseringsregel som används för att ange en anslutningssträng. En referens till den `NamespaceManager` instans lagras i den `$NamespaceManager` variabeln. `$NamespaceManager` används för att etablera en notification hub.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Etablera en ny Meddelandehubb

För att etablera en ny meddelandehubb, använda den [.NET API för Notification Hub].

I den här delen av skriptet kan ställa du in fyra lokala variabler.

1. `$Namespace`: Ange namnet på namnområdet där du vill skapa en meddelandehubb.
2. `$Path`: Ange den här sökvägen till namnet på den nya meddelandehubben.  Till exempel ”MyHub”.
3. `$WnsPackageSid`: Ställ in på paket-SID för din Windows-App från den [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Ställ in på den hemliga nyckeln för din Windows-App från den [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Dessa variabler används för att ansluta till ditt namnområde och skapa en ny Meddelandehubb som konfigurerats för att hantera Windows Notification Services (WNS)-meddelanden med WNS autentiseringsuppgifter för en Windows-App. Information om hur du skaffar paketet SID och hemliga nyckel finns i den [komma igång med Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) självstudien.

- Skript-kodfragment används den `NamespaceManager` objekt för att kontrollera om Meddelandehubben som identifieras av `$Path` finns.
- Om det inte finns skriptet skapar `NotificationHubDescription` med WNS autentiseringsuppgifter och skickar det till den `NamespaceManager` klass `CreateNotificationHub` metod.

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>Ytterligare resurser

- [Hantera Service Bus med PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Så här skapar du Service Bus-köer, ämnen och prenumerationer med hjälp av ett PowerShell-skript](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Så här skapar du en Service Bus Namespace och en Händelsehubb med ett PowerShell-skript](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Vissa färdiga skript är också tillgängliga för nedladdning:

- [Service Bus PowerShell-skript](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Köpalternativ]: https://azure.microsoft.com/pricing/purchase-options/
[Medlemserbjudanden]: https://azure.microsoft.com/pricing/member-offers/
[Kostnadsfri utvärderingsversion]: https://azure.microsoft.com/pricing/free-trial/
[Installera och konfigurera Azure PowerShell]: /powershell/azureps-cmdlets-docs
[.NET API för Notification hub]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
