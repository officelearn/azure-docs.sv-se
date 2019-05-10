---
title: Token cache serialisering i Microsoft Authentication Library för .NET | Azure
description: Läs mer om serialisering och kunden serialisering av tokens cacheminne med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f1e9a48b114d328e0405a2f03764df4ce29b166
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407064"
---
# <a name="token-cache-serialization-in-msalnet"></a>Token-cache-serialisering i MSAL.NET
När du har en [token förvärvas](msal-acquire-cache-tokens.md), cachelagras av Microsoft Authentication Library (MSAL).  Programkod bör försöka att hämta en token från cacheminnet innan du hämtar en token med en annan metod.  Den här artikeln beskrivs standard och anpassade serialisering av token i MSAL.NET cacheminnet.

Den här artikeln är MSAL.NET 3.x. Om du är intresserad av MSAL.NET 2.x kan se [Token cache serialisering i MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Standard-serialisering för mobila plattformar

I MSAL.NET som en token minnescache standard. Serialisering är som standard för plattformar där säker lagring är tillgänglig för en användare som en del av plattformen. Detta är fallet för Universal Windows Platform (UWP), Xamarin.iOS och Xamarin.Android.

> [!Note]
> När du migrerar en Xamarin.Android-projekt från MSAL.NET 1.x till MSAL.NET 3.x, kanske du vill lägga till `android:allowBackup="false"` till projektet för att undvika gamla cachelagrade token från kommer tillbaka när Visual Studio-distributioner utlöser en återställning av lokal lagring. Se [utfärda #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Anpassad serialisering för Windows-skrivbordsappar och web apps/webb-API: er

Kom ihåg att anpassad serialisering är inte tillgängligt på mobila plattformar (UWP Xamarin.iOS och Xamarin.Android). MSAL definierar redan en säker och högpresterande serialiseringsmekanism för dessa plattformar. .NET-skrivbord och .NET Core program, men har olika arkitekturer och MSAL inte kan implementera en serialiseringsmekanism för allmänna. Webbplatser kan exempelvis välja att lagra token i en Redis-cache eller skrivbordsappar store token i en krypterad fil. Serialisering tillhandahålls inte så out-of the box. Om du vill att ett program för beständiga token-cache i .NET desktop eller .NET Core, måste du anpassa serialisering.

Följande klasser och gränssnitt som används i token-cache serialisering:

- `ITokenCache`, som definierar händelser att prenumerera på token serialisering cachebegäranden samt metoder för att serialisera eller deserialisera cache i olika format (ADAL v3.0, MSAL 2.x och MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback` skickas en motringning på händelser så att du kan hantera serialisering. De kommer att anropas med argument av typen `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` endast innehåller de `ClientId` på programmet och en referens till användaren som token är tillgänglig.

  ![Klassdiagram](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET skapar token cacheminnen för dig och förser dig med den `IToken` cachelagra när du anropar ett programs `GetUserTokenCache` och `GetAppTokenCache` metoder. Du är inte avsedd för att implementera gränssnittet själv. Det är ditt ansvar, när du implementerar en anpassad tokencache-serialisering att:
> - Reagera på `BeforeAccess` och `AfterAccess` ”händelser”. Den `BeforeAccess` ombudet är ansvarig för att deserialisera cache, medan den `AfterAccess` en ansvarar för serialisering av cachen.
> - En del av händelserna lagra eller läsa in blobbar som skickas via argumentet händelse till det lagringsutrymme som du vill.

Strategierna är olika beroende på om du skriver en token-cache-serialisering för en [offentliga klientprogram](msal-client-applications.md) (skrivbord), eller en [konfidentiell klientprogram](msal-client-applications.md)) (webbapp / webb-API, daemon-app ).

### <a name="token-cache-for-a-public-client"></a>Token-cache för en offentlig klient 

Sedan MSAL.NET v2.x har du flera alternativ för serialisering av en offentlig klient token-cache. Du kan serialisera cachen bara till formatet MSAL.NET (enhetligt format-cachen är gemensamt för MSAL och plattformarna).  Du kan också använda den [äldre](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) token cache serialisering av ADAL V3.

Anpassa token-cache-serialisering för att dela det enda inloggning tillståndet mellan ADAL.NET 3.x ADAL.NET 5.x och MSAL.NET förklaras delvis i följande exempel: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> MSAL.NET 1.1.4-preview tokencache format stöds inte längre i MSAL 2.x. Om du har program med hjälp av MSAL.NET 1.x, användarna behöver återexport-registrerings-modulen. Alternativt kan stöds migreringen från ADAL 4.x (och 3.x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Enkel tokencache serialisering (MSAL)

Nedan visas ett exempel på en naïve implementering av anpassade serialisering av en token cache för datorprogram. Här kan är användaren token-cache en fil i samma mapp som programmet.

När du har skapat programmet kan du aktivera serialisering genom att anropa den `TokenCacheHelper.EnableSerialization()` metoden och skicka programmet `UserTokenCache`.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Den `TokenCacheHelper` hjälparklass definieras som:

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

En förhandsversion av en produkt kvalitet tokencache filbaserad serialisering för offentliga klientprogram (för program som körs på Windows, Mac och Linux) är tillgänglig från den [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) bibliotek med öppen källkod. Du kan inkludera den i dina program från följande nuget-paket: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Dubbel tokencache serialisering (MSAL unified cache och ADAL v3)

Om du vill implementera tokencache serialisering båda med enhetlig cache-format (vanligt att ADAL.NET 4.x, MSAL.NET 2.x och andra MSALs av samma generation eller äldre, på samma plattform), ta en titt på följande kod:

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

Den här gången hjälparklass som:

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
  /// Get the user token cache
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
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Token-cache för en webbapp (konfidentiell klient-program)

Cachen i webbappar eller webb-API: er kan nu använda sessionen, en Redis-cache eller en databas.

En viktig sak att komma ihåg är att för webbappar och webb-API: er, det ska vara en token-cache per användare (per konto). Du behöver att serialisera tokens cacheminne för varje konto.

Exempel på hur du använder token cacheminnen för webbappar och webb-API: er är tillgängliga i den [ASP.NET Core självstudier om webbappen](https://ms-identity-aspnetcore-webapp-tutorial) i fasen [2-2-Tokencache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Om du vill implementeringar har en titt på följande mapp [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) i den [microsoft-autentisering-tillägg-för-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) biblioteket (i den [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) mapp. 

## <a name="next-steps"></a>Nästa steg
Följande exempel illustrerar tokencache serialisering.

| Exempel | Plattform | Beskrivning|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Desktop (WPF) | Windows Desktop .NET (WPF) program anropa Microsoft Graph API. ![Topologi](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Fjärrskrivbord (konsol) | Visual Studio-lösningar som illustrerar migreringen av Azure AD v1.0-program (med ADAL.NET) till Azure AD v2.0-program som också kallas konvergerade program (med hjälp av MSAL.NET), i synnerhet [Token Cache-migrering](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|