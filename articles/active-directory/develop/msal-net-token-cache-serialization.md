---
title: Cachelagring av token i Microsoft Authentication Library för .NET
titleSuffix: Microsoft identity platform
description: Lär dig mer om serialisering och kundens serialisering av token-cachen med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cae1a755b9f79ce2fd5084653f7b3c177f29832
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802726"
---
# <a name="token-cache-serialization-in-msalnet"></a>Cachelagring av token i MSAL.NET
När en [token har hämtats](msal-acquire-cache-tokens.md)cachelagras den av Microsoft Authentication Library (MSAL).  Program koden bör försöka hämta en token från cachen innan du hämtar en token med en annan metod.  I den här artikeln beskrivs standard och anpassad serialisering för token cache i MSAL.NET.

Den här artikeln är för MSAL.NET 3. x. Om du är intresse rad av MSAL.NET 2. x läser du [serialisera token cache i MSAL.NET 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Standard serialisering för mobila plattformar

I MSAL.NET anges en token i minnet som standard. Serialisering tillhandahålls som standard för plattformar där säkert lagrings utrymme är tillgängligt för en användare som en del av plattformen. Detta är fallet för Universell Windows-plattform (UWP), Xamarin. iOS och Xamarin. Android.

> [!Note]
> När du migrerar ett Xamarin. Android-projekt från MSAL.NET 1. x till MSAL.NET 3. x kanske du vill lägga till `android:allowBackup="false"` i projektet för att undvika att gamla cachelagrade token kommer tillbaka när Visual Studio-distributioner utlöser en återställning av lokal lagring. Se [problem #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Anpassad serialisering för Windows-skrivbordsappar och webbappar/webb-API: er

Kom ihåg att anpassad serialisering inte är tillgänglig på mobila plattformar (UWP, Xamarin. iOS och Xamarin. Android). MSAL definierar redan en säker och utförd serialisering för dessa plattformar. .NET Desktop-och .NET Core-program, men har varierande arkitekturer och MSAL kan inte implementera en mekanism för generell användning av serialisering. Webbplatser kan till exempel välja att lagra tokens i en Redis cache, eller skrivbordsappar lagra tokens i en krypterad fil. Därför har serialisering inte tillhandahållits direkt. Om du vill ha ett program med beständig token cache i .NET Desktop eller .NET Core måste du anpassa serialiseringen.

Följande klasser och gränssnitt används i cache-serialisering för token:

- `ITokenCache`, som definierar händelser för att prenumerera på begär Anden om cachelagring av token och metoder för att serialisera eller deserialisera cachen i olika format (ADAL v 3.0, MSAL 2. x och MSAL 3. x = ADAL v 5.0).
- `TokenCacheCallback` är ett återanrop som skickas till händelserna så att du kan hantera serialiseringen. De anropas med argument av typen `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` tillhandahåller endast `ClientId` för programmet och en referens till den användare som token är tillgänglig för.

  ![Klass diagram](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET skapar token-cacheminnen för dig och ger dig `IToken` cache när du anropar ett programs `UserTokenCache`-och `AppTokenCache` egenskaper. Du ska inte implementera gränssnittet själv. Ditt ansvar när du implementerar en anpassad token cache-serialisering är att:
> - Reagera på `BeforeAccess` och `AfterAccess` händelser (eller deras asynkrona varianter). `BeforeAccess` ombudet ansvarar för deserialisering av cacheminnet, medan den `AfterAccess` som är ansvarig för serialisering av cachen.
> - En del av dessa händelser lagrar eller läser in blobbar som skickas genom händelse argumentet till vilken lagring du vill ha.

Strategierna skiljer sig beroende på om du skriver en cachelagring för token-cache för ett [offentligt klient program](msal-client-applications.md) (skriv bord) eller ett [konfidentiellt klient program](msal-client-applications.md)) (WEBBAPP/webb-API, daemon-app).

### <a name="token-cache-for-a-public-client"></a>Token-cache för en offentlig klient 

Eftersom MSAL.NET v2. x har du flera alternativ för att serialisera token-cachen för en offentlig klient. Du kan endast serialisera cachen till MSAL.NET-formatet (Unified format cache är gemensamt för MSAL och plattformarna).  Du kan också stödja den [äldre](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) token-cachelagring av ADAL v3.

Att anpassa token cache-serialiseringen för att dela läget för enkel inloggning mellan ADAL.NET 3. x, ADAL.NET 5. x och MSAL.NET förklaras i följande exempel: [Active Directory-dotNet-v1-till-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> MSAL.NET 1.1.4-Preview token cache stöds inte längre i MSAL 2. x. Om du har program som utnyttjar MSAL.NET 1. x måste användarna logga in igen. Alternativt kan migreringen från ADAL 4. x (och 3. x) användas.

#### <a name="simple-token-cache-serialization-msal-only"></a>Simple token cache-serialisering (endast MSAL)

Nedan visas ett exempel på en naïve-implementering av anpassad serialisering av ett token-cache för Skriv bords program. Här är användartoken cache en fil i samma mapp som programmet.

När du har skapat programmet aktiverar du serialiseringen genom att anropa metoden `TokenCacheHelper.EnableSerialization()` och skicka program `UserTokenCache`.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Klassen `TokenCacheHelper` Helper definieras som:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

En för hands version av en token cache-baserad med produkt kvalitet för offentliga klient program (för Skriv bords program som körs på Windows, Mac och Linux) finns i biblioteket [Microsoft. Identity. client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) med öppen källkod. Du kan ta med den i dina program från följande NuGet-paket: [Microsoft. Identity. client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Dubbla token cache-serialisering (MSAL Unified cache och ADAL v3)

Ta en titt på följande kod om du vill implementera serialisering för cachelagring både med det enhetliga cacheminnet (common to ADAL.NET 4. x, MSAL.NET 2. x och andra MSALs i samma plattform):

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Den här gången hjälper klassen som definieras som:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Token-cache för en webbapp (konfidentiellt klient program)

I webbappar eller webb-API: er kan cache utnyttja sessionen, en Redis-cache eller en databas.

I Web Apps eller webb-API: er, Behåll ett token-cache per konto.  För Web Apps bör token-cachen anges av konto-ID: t.  För webb-API: er ska kontot anges genom hashen för den token som används för att anropa API: et. MSAL.NET tillhandahåller anpassad cachelagring av token i .NET Framework och .NET Core-underplattformar. Händelser utlöses när cachen används, kan appar välja om de vill serialisera eller deserialisera cachen. På konfidentiella klient program som hanterar användare (webbappar som loggar in användare och anropar webb-API: er och webb-API: er som anropar underordnade webb-API: er), kan det finnas många användare och användarna bearbetas parallellt. Av säkerhets-och prestanda skäl är vår rekommendation att serialisera en cache per användare. Serialiserings händelser beräknar en cache-nyckel baserat på identiteten för den bearbetade användaren och serialiserar/deserialiserar en token-cache för den användaren.

Exempel på hur du kan använda token-cacheminnen för webbappar och webb-API: er finns i [självstudien ASP.net Core Web Apps](https://ms-identity-aspnetcore-webapp-tutorial) i fas [2-2-token cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). För implementeringar kan du titta på mappen [TokenCacheProviders](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/Microsoft.Identity.Web/TokenCacheProviders) i [Microsoft-Authentication-Extensions-for-dotNet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) -biblioteket (i mappen [Microsoft. identitet. client. Extensions. Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) . 

## <a name="next-steps"></a>Nästa steg
I följande exempel visas serialisering av cachelagring av token.

| Exempel | Plattform | Beskrivning|
| ------ | -------- | ----------- |
|[Active Directory – dotNet-Desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Desktop (WPF) | Windows Desktop .NET-program (WPF) anropar Microsoft Graph-API: et. ![Topologi](media/msal-net-token-cache-serialization/topology.png)|
|[Active Directory – dotNet-v1-till-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Skriv bord (konsol) | En uppsättning Visual Studio-lösningar som illustrerar migreringen av Azure AD v 1.0-program (med ADAL.NET) till Azure AD v 2.0-program, även kallat konvergerade program (med MSAL.NET), i synnerhet [token cache-migrering](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
