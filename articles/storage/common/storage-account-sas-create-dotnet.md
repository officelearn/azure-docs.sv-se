---
title: Skapa ett konto säkerhets associationer med .NET
titleSuffix: Azure Storage
description: Lär dig hur du skapar en signatur för delad åtkomst för kontot (SAS) med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 75f4a3c835a843e824c2ac0b437843cd91824bfa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512716"
---
# <a name="create-an-account-sas-with-net"></a>Skapa ett konto säkerhets associationer med .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder lagrings konto nyckeln för att skapa en konto säkerhets Association med [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="create-an-account-sas"></a>Skapa en konto-SAS

Om du vill skapa en konto säkerhets Association för en behållare anropar du metoden [CloudStorageAccount. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) .

I följande kod exempel skapas en konto säkerhets Association som är giltig för blob-och fil tjänsterna och ger klienten behörigheterna läsa, skriva och lista för att få åtkomst till API: er på tjänst nivå. Kontots SAS begränsar protokollet till HTTPS, så begäran måste göras med HTTPS. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

## <a name="use-an-account-sas-from-a-client"></a>Använda en konto säkerhets Association från en klient

Om du vill använda kontots SAS för att få åtkomst till API: er för den Blob Service skapar du ett Blob Service klient objekt med SAS-och Blob Storage-slutpunkten för ditt lagrings konto. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

## <a name="next-steps"></a>Nästa steg

- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](storage-sas-overview.md)
- [Skapa en konto-SAS](/rest/api/storageservices/create-account-sas)
