---
title: Distribuera och hantera meddelandehubbar med PowerShell
description: Så här skapar och hanterar du meddelandehubbar med PowerShell for Automation
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 863fdb445cce41f0fe4cbee63a3d6198c0a79339
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264652"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Distribuera och hantera meddelandehubbar med PowerShell

## <a name="overview"></a>Översikt

Den här artikeln visar hur du använder Skapa och hantera Azure Notification Hubs med PowerShell. Följande vanliga automatiseringsuppgifter visas i den här artikeln.

- Skapa en meddelandehubb
- Ange autentiseringsuppgifter

Om du också behöver skapa ett nytt tjänstbussnamnområde för dina meddelandehubbar läser du [Hantera servicebuss med PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Hantering av meddelandehubbar stöds inte direkt av cmdlets som ingår i Azure PowerShell. Den bästa metoden från PowerShell är att referera till sammansättningen Microsoft.Azure.NotificationHubs.dll. Sammansättningen distribueras med [Microsoft Azure Notification Hubs NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Azure är en prenumerationsbaserad plattform. Mer information om hur du skaffar en prenumeration finns i [Köpalternativ,] [Medlemserbjudanden]eller [Kostnadsfri utvärderingsversion].
- En dator med Azure PowerShell. Instruktioner finns i [Installera och konfigurera Azure PowerShell].
- En allmän förståelse av PowerShell-skript, NuGet-paket och .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Inklusive en referens till .NET-enheten för servicebuss

Hantering av Azure Notification Hubs ingår ännu inte i PowerShell-cmdlets i Azure PowerShell. Om du vill etablera meddelandehubbar kan du använda .NET-klienten som finns i [Microsoft Azure Notification Hubs NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Kontrollera först att skriptet kan hitta **sammansättningen Microsoft.Azure.NotificationHubs.dll,** som installeras som ett NuGet-paket i ett Visual Studio-projekt. För att vara flexibel utför skriptet följande steg:

1. Bestämmer sökvägen som den anropades på.
2. Korsar sökvägen tills den `packages`hittar en mapp med namnet . Den här mappen skapas när du installerar NuGet-paket för Visual Studio-projekt.
3. Söker rekursivt `packages` mappen efter en `Microsoft.Azure.NotificationHubs.dll`sammansättning med namnet .
4. Refererar till sammansättningen så att typerna är tillgängliga för senare användning.

Så här implementeras de här stegen i ett PowerShell-skript:

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

## <a name="create-the-namespacemanager-class"></a>Skapa `NamespaceManager` klassen

Om du vill etablera meddelandehubbar skapar du en instans av klassen [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) från SDK.

Du kan använda [cmdleten Get-AzureSBAuthorizationRule] som ingår i Azure PowerShell för att hämta en auktoriseringsregel som används för att tillhandahålla en anslutningssträng. En referens `NamespaceManager` till instansen `$NamespaceManager` lagras i variabeln. `$NamespaceManager`används för att etablera en meddelandehubb.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Etablera en ny meddelandehubb

Om du vill etablera en ny meddelandehubb använder du [.NET API för Meddelandehubbar].

I den här delen av skriptet ställer du in fyra lokala variabler.

1. `$Namespace`: Ställ in detta på namnet på namnområdet där du vill skapa en meddelandehubb.
2. `$Path`: Ange den här sökvägen till namnet på den nya meddelandehubben.  Till exempel "MyHub".
3. `$WnsPackageSid`: Ställ in detta på paketet SID för Din Windows-app från [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Ställ in detta på den hemliga nyckeln för din Windows-app från [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Dessa variabler används för att ansluta till ditt namnområde och skapa en ny meddelandehubb som konfigurerats för att hantera WNS-meddelanden (Windows Notification Services) med WNS-autentiseringsuppgifter för en Windows-app. Information om hur du hämtar paketet SID och hemlig nyckel finns i självstudien [Komma igång med meddelandehubbar.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)

- Skriptkodavsnittet använder `NamespaceManager` objektet för att kontrollera om meddelandehubben som identifieras av `$Path` finns.
- Om det inte finns, skriptet skapas `NotificationHubDescription` med WNS-autentiseringsuppgifter och skickar den till klassmetoden. `NamespaceManager` `CreateNotificationHub`

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
- [Skapa servicebussköer, ämnen och prenumerationer med ett PowerShell-skript](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Skapa ett servicebussnamnområde och en händelsehubb med ett PowerShell-skript](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Vissa färdiga skript finns också tillgängliga för nedladdning:

- [Service Bus PowerShell-skript](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Köpoptioner]: https://azure.microsoft.com/pricing/purchase-options/
[Medlemserbjudanden]: https://azure.microsoft.com/pricing/member-offers/
[Gratis provperiod]: https://azure.microsoft.com/pricing/free-trial/
[Installera och konfigurera Azure PowerShell]: /powershell/azureps-cmdlets-docs
[.NET API för meddelandehubbar]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
