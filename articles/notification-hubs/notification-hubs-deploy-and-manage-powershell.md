---
title: Distribuera och hantera Notification Hubs med PowerShell
description: Skapa och hantera Notification Hubs med hjälp av PowerShell för Automation
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
ms.openlocfilehash: 2cff6ef25d0fe812cc6df26bf946daff29d37f84
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102844"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Distribuera och hantera Notification Hub med PowerShell

## <a name="overview"></a>Översikt

Den här artikeln visar hur du använder skapa och hantera Azure Notification Hubs med hjälp av PowerShell. Följande vanliga automatiserings aktiviteter visas i den här artikeln.

- Skapa en meddelandehubb
- Ange autentiseringsuppgifter

Om du också behöver skapa ett nytt Service Bus-namnområde för dina Notification Hub, se [hantera Service Bus med PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Hantering av Notification Hub stöds inte direkt av de cmdletar som ingår i Azure PowerShell. Den bästa metoden från PowerShell är att referera till sammansättningen Microsoft. Azure. NotificationHubs. dll. Sammansättningen distribueras med [Microsoft Azure Notification Hubs NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Azure är en prenumeration-baserad plattform. Läs mer om hur du skaffar en prenumeration, [Köp alternativ], [Medlems erbjudanden], eller [Kostnads fri utvärdering].
- En dator med Azure PowerShell. Anvisningar finns i [Installera och konfigurera Azure PowerShell].
- En allmän förståelse för PowerShell-skript, NuGet-paket och .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Inklusive en referens till .NET-sammansättningen för Service Bus

Hantering av Azure-Notification Hubs ingår ännu inte i PowerShell-cmdletar i Azure PowerShell. Om du vill etablera aviserings hubbar kan du använda .NET-klienten som finns i [Microsoft Azure Notification Hubs NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Kontrol lera först att skriptet kan hitta **Microsoft. Azure. NotificationHubs. dll** -sammansättningen som installeras som ett NuGet-paket i ett Visual Studio-projekt. Skriptet utför följande steg för att vara flexibelt:

1. Anger sökvägen då den anropades.
2. Bläddrar i sökvägen tills den hittar en mapp med `packages`namnet. Den här mappen skapas när du installerar NuGet-paket för Visual Studio-projekt.
3. Söker rekursivt i `packages` mappen efter en sammansättning med namnet `Microsoft.Azure.NotificationHubs.dll`.
4. Refererar till sammansättningen så att typerna kan användas senare.

Så här implementerar du de här stegen i ett PowerShell-skript:

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

## <a name="create-the-namespacemanager-class"></a>`NamespaceManager` Skapa klassen

Om du vill etablera Notification Hubs skapar du en instans av klassen [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) från SDK: n.

Du kan använda cmdleten [Get-AzureSBAuthorizationRule] som ingår i Azure PowerShell för att hämta en auktoriseringsregel som används för att ange en anslutnings sträng. En referens till `NamespaceManager` instansen lagras `$NamespaceManager` i variabeln. `$NamespaceManager`används för att etablera en Notification Hub.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Etablering av en ny Notification Hub

Om du vill etablera en ny Notification Hub använder du [.NET API för Notification Hubs].

I den här delen av skriptet ställer du in fyra lokala variabler.

1. `$Namespace`: Ange namnet på det namn område där du vill skapa en Notification Hub.
2. `$Path`: Ange den här sökvägen till namnet på den nya Notification Hub.  Till exempel "MyHub".
3. `$WnsPackageSid`: Ange detta till paket-SID för Windows-appen från [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Ange den hemliga nyckeln för Windows-appen från [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Dessa variabler används för att ansluta till ditt namn område och skapa ett nytt meddelande nav som kon figurer ATS för att hantera Windows Notification Services-meddelanden (WNS) med WNS-autentiseringsuppgifter för en Windows-app. Information om hur du hämtar paket-SID och hemlig nyckel finns i själv studie kursen [komma igång med Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .

- Skript utdraget använder `NamespaceManager` objektet för att kontrol lera om Notification Hub identifieras av `$Path` finns.
- Om den inte finns skapar `NotificationHubDescription` skriptet med WNS-autentiseringsuppgifter och skickar det `NamespaceManager` till klass `CreateNotificationHub` metoden.

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
- [Så här skapar du Service Bus köer, ämnen och prenumerationer med hjälp av ett PowerShell-skript](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Så här skapar du ett Service Bus-namnområde och en Event Hub med ett PowerShell-skript](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Vissa färdiga skript är också tillgängliga för nedladdning:

- [Service Bus PowerShell-skript](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Köp alternativ]: https://azure.microsoft.com/pricing/purchase-options/
[Medlems erbjudanden]: https://azure.microsoft.com/pricing/member-offers/
[Kostnads fri utvärdering]: https://azure.microsoft.com/pricing/free-trial/
[Installera och konfigurera Azure PowerShell]: /powershell/azureps-cmdlets-docs
[.NET API för Notification Hubs]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
