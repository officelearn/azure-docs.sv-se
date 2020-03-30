---
title: Serialisering av tokencache (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om serialisering och kundserierisering av tokencachen med Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1bd348ad27d892d0421b13c16ce81bc4f5dfb021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262807"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serialisering av tokencache i MSAL.NET
När en [token har hämtats](msal-acquire-cache-tokens.md)cachelagras den av Microsoft Authentication Library (MSAL).  Programkod bör försöka hämta en token från cacheminnet innan du hämtar en token med en annan metod.  I den här artikeln beskrivs standard- och anpassnings serialisering av tokencachen i MSAL.NET.

Denna artikel är för MSAL.NET 3.x. Om du är intresserad av MSAL.NET 2.x läser du [Serialisering av tokencache i MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Standardserierisering för mobila plattformar

I MSAL.NET tillhandahålls en tokencache i minnet som standard. Serialisering tillhandahålls som standard för plattformar där säker lagring är tillgänglig för en användare som en del av plattformen. Detta är fallet för Universal Windows Platform (UWP), Xamarin.iOS och Xamarin.Android.

> [!Note]
> När du migrerar ett Xamarin.Android-projekt från MSAL.NET 1.x till MSAL.NET 3.x `android:allowBackup="false"` kanske du vill lägga till i projektet för att undvika att gamla cachelagrade token kommer tillbaka när Visual Studio-distributioner utlöser en återställning av lokal lagring. Se [Problem #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Anpassad serialisering för Windows-skrivbordsappar och webbappar/webb-API:er

Kom ihåg att anpassad serialisering inte är tillgänglig på mobila plattformar (UWP, Xamarin.iOS och Xamarin.Android). MSAL definierar redan en säker och högpresterande serialiseringsmekanism för dessa plattformar. .NET-skrivbords- och .NET Core-program har dock olika arkitekturer och MSAL kan inte implementera en generell serialiseringsmekanism. Webbplatser kan till exempel välja att lagra token i en Redis-cache eller att skrivbordsappar lagrar token i en krypterad fil. Så serialisering tillhandahålls inte out-of-the-box. Om du vill ha ett beständigt tokencacheprogram i .NET-skrivbordet eller .NET Core måste du anpassa serialiseringen.

Följande klasser och gränssnitt används i serialisering av tokencache:

- `ITokenCache`, som definierar händelser för att prenumerera på serialiseringsbegäranden för tokencache samt metoder för att serialisera eller av serialisera cachen i olika format (ADAL v3.0, MSAL 2.x och MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback`är en motringning som skickas till händelserna så att du kan hantera serialiseringen. De kommer att anropas `TokenCacheNotificationArgs`med argument av typen .
- `TokenCacheNotificationArgs`tillhandahåller endast `ClientId` av programmet och en referens till den användare som token är tillgänglig för.

  ![Klassdiagram](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET skapar tokencachar åt dig och ger `IToken` dig cacheminnet när `UserTokenCache` du `AppTokenCache` anropar ett programs och egenskaper. Du ska inte implementera gränssnittet själv. Ditt ansvar, när du implementerar en anpassad tokencache serialisering, är att:
> - Reagera `BeforeAccess` på `AfterAccess` och "händelser" (eller deras Async smaker). Ombudet `BeforeAccess` är ansvarigt för att deserialisera cacheminnet, medan den `AfterAccess` som ansvarar för serialisering av cachen.
> - En del av dessa händelser lagrar eller läser in blobbar, som skickas genom händelseargumentet till vilken lagring du vill.

Strategierna är olika beroende på om du skriver en tokencache serialisering för ett [offentligt klientprogram](msal-client-applications.md) (skrivbord), eller ett [konfidentiellt klientprogram)](msal-client-applications.md)(webbapp / webb-API, daemon app).

### <a name="token-cache-for-a-public-client"></a>Tokencache för en offentlig klient 

Sedan MSAL.NET v2.x har du flera alternativ för serialisering av tokencachen för en offentlig klient. Du kan serialisera cachen endast till MSAL.NET format (den enhetliga formatcachen är vanlig mellan MSAL och plattformarna).  Du kan också stödja den [äldre](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) tokencachen serialisering av ADAL V3.

Anpassa serialiseringen av tokencachen för att dela det enskilda inloggningstillståndet mellan ADAL.NET 3.x, ADAL.NET 5.x och MSAL.NET förklaras i en del av följande exempel: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Det MSAL.NET tokencacheformatet för 1.1.4-förhandsgranskning stöds inte längre i MSAL 2.x. Om du har program som utnyttjar MSAL.NET 1.x måste användarna logga in igen. Alternativt stöds migreringen från ADAL 4.x (och 3.x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Enkel tokencache serialisering (endast MSAL)

Nedan är ett exempel på en naiv implementering av anpassad serialisering av en tokencache för skrivbordsprogram. Här är användartokencachen en fil i samma mapp som programmet.

När du har byggt programmet aktiverar du `TokenCacheHelper.EnableSerialization()` serialiseringen genom `UserTokenCache`att anropa metoden och skicka programmet .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Hjälpklassen `TokenCacheHelper` definieras som:

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

En förhandsgranskning av en tokenfil för produktkvalitet för offentliga klientprogram (för skrivbordsprogram som körs på Windows, Mac och Linux) finns på [biblioteket microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) open source. Du kan inkludera det i dina program från följande nugetpaket: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serialisering av dual token cache (MSAL unified cache och ADAL v3)

Om du vill implementera serialisering av tokencache både med det enhetliga cacheformatet (gemensamt för ADAL.NET 4.x, MSAL.NET 2.x och andra MSALs av samma generation eller äldre, på samma plattform), ta en titt på följande kod:

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

Den här gången hjälperklassen enligt definitionen:

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

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Tokencache för en webbapp (konfidentiellt klientprogram)

I webbappar eller webb-API:er kan cachen utnyttja sessionen, en Redis-cache eller en databas.

I webbappar eller webb-API:er behåller du en tokencache per konto.  För webbappar ska tokencachen knappas in av konto-ID:t.  För webb-API:er ska kontot knappas in av hash-värdet för den token som används för att anropa API:et. MSAL.NET tillhandahåller anpassad tokencacheseriering i .NET Framework och .NET Core-underplattformar. Händelser utlöses när cachen används, appar kan välja om du vill serialisera eller avserialisera cachen. På konfidentiella klientprogram som hanterar användare (webbappar som loggar in användare och anropar webb-API:er och webb-API:er som anropar underordnade webb-API:er) kan det finnas många användare och användarna bearbetas parallellt. Av säkerhets- och prestandaskäl är vår rekommendation att serialisera en cache per användare. Serialiseringshändelser beräknar en cachenyckel baserat på identiteten hos den bearbetade användaren och serialiserar/deserialie en tokencache för den användaren.

Exempel på hur du använder tokencachar för webbappar och webb-API:er finns i [självstudiekursen för ASP.NET Core-webbappar](https://ms-identity-aspnetcore-webapp-tutorial) i fas [2-2 Token Cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). För implementeringar kan du titta på mappen [TokenCacheProviders](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/Microsoft.Identity.Web/TokenCacheProviders) i [microsoft-authentication-extensions-for-dotnet-biblioteket](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (i mappen [Microsoft.Identity.Client.Extensions.Web.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) 

## <a name="next-steps"></a>Nästa steg
Följande exempel illustrerar serialisering av tokencache.

| Exempel | Plattform | Beskrivning|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Stationär dator (WPF) | Wpf-programmet (Windows Desktop .NET) anropar Microsoft Graph API. ![Topologi](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Stationär dator (konsol) | Uppsättning Visual Studio-lösningar som illustrerar migreringen av Azure AD v1.0-program (med ADAL.NET) till Azure AD v2.0-program, även namngivna konvergerade program (med MSAL.NET), särskilt [migrering av tokencache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
