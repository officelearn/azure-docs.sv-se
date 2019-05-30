---
title: Testa Microsoft Authentication Library (MSAL) program | Azure
description: Läs mer om testning av Microsoft Authentication Library (MSAL) program.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/28/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3495ad5f691ac57b69ab5d3efcd5436cc66e9a6e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307530"
---
# <a name="testing-msal-applications"></a>Testa MSAL program
Den här artikeln beskriver förslag för att testa Microsoft Authentication Library (MSAL) program.

## <a name="unit-testing"></a>Enhetstestning
MSAL-API: erna använder den [builder mönstret](https://wikipedia.org/wiki/Builder_pattern) kraftigt. Verktyg är svårt och tidsödande att skapa simulerade. I stället rekommenderar vi att du omsluter din autentiseringslogiken bakom ett gränssnitt och simulera som i din app.

## <a name="end-to-end-testing"></a>Slutpunkt till slutpunkt testning
För slutpunkt till slutpunkt testning bör du ställa in testkonton, testa program eller även separata kataloger. Användarnamn och lösenord kan distribueras via pipeline för kontinuerlig integrering (till exempel hemliga build variabler i Azure DevOps). En annan strategi är att testa autentiseringsuppgifter [Key Vault](/azure/key-vault/) och konfigurera den dator som kör tester för att få åtkomst till Key Vault), till exempel genom att installera ett certifikat. Passa på att du använder MSAL'S [strategi för åtkomst till Nyckelvalv](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/Microsoft.Identity.Test.LabInfrastructure/KeyVaultSecretsProvider.cs#L112).

När tokenförvärv inträffar, cachelagras både en åtkomsttoken och en uppdateringstoken. Först har en livslängd på en timme, det senare i flera månader. När åtkomsttoken upphör att gälla används MSAL uppdateringstoken för att skaffa en ny utan användaråtgärder. Du kan lita på det här beteendet för att etablera dina tester.

Om du har villkorlig åtkomst som är konfigurerad, kommer det vara svårt att automatisera runt den. Det blir enklare att ha en manuell åtgärd som behandlar villkorlig åtkomst (till exempel multifaktorautentisering), som lägger till token i MSAL cachen och sedan förlitar sig på tyst token förvärv, det vill säga förlitar sig på en tidigare inloggade användare.

## <a name="web-apps"></a>Webbappar
Använd selen eller en motsvarande teknik för att automatisera webbappen. Hämta användarnamn och lösenord från en säker plats.

## <a name="daemon-apps"></a>Daemon-appar
Daemon-appar använder redan distribuerade hemligheter (lösenord eller certifikat) för att tala till Azure Active Directory (AAD). Du kan distribuera en hemlighet i din testmiljö eller Använd token cachelagring teknik för att etablera dina tester. Den [klientens autentiseringsuppgifter bevilja](msal-authentication-flows.md#client-credentials) inte hämta uppdateringstoken, endast åtkomsttoken som går ut inom en timme.

## <a name="native-client-apps"></a>Ursprungliga klientappar
Det finns flera sätt att testa för interna klienter:

* Använd den [användarnamn/lösenord bevilja](msal-authentication-flows.md#usernamepassword) att hämta en token i ett icke-interaktivt sätt. Det här flödet rekommenderas inte i produktion, men är det rimligt att använda den för att testa.

* Använda ett ramverk som Appium eller Xamarin.Test som tillhandahåller ett automationsgränssnitt för både din app och MSAL skapat webbläsare.

* MSAL Exponerar en åtkomstpunkt för utökningsbarhet som gör att utvecklare kan mata in sina egna Bläddringsupplevelse. MSAL teamet använder detta internt för att testa scenarier för interaktiv autentisering. En titt på [testprojekt för den här .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/Microsoft.Identity.Test.Integration.net45/SeleniumTests/InteractiveFlowTests.cs) att se hur du mata in en [selen-baserade webbläsare](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/tree/master/tests/Microsoft.Identity.Test.Integration.net45/Infrastructure) som kan hantera autentisering.

## <a name="xamarin-apps"></a>Xamarin-appar
MSAL teamet körs testerna på en Xamarin-app som använder MSAL.NET; Vi använder [App Center](https://appcenter.ms/apps) testet körs för att hantera enheter, etc. Testramverket är [Xamarin.UITest](/appcenter/test-cloud/uitest/). En begränsning som vi har hittat är att vi kan inte testa system webbläsare, endast inbäddade webbläsare.

När du utvärderar en Testramverk, är det värt också att ha en titt på Appium och andra Testramverk, samt andra leverantörer av CI/CD i mobila utrymme.

## <a name="testing-the-token-cache"></a>Testa token-cache
Oavsett vilken plattform som du använder, lagrar MSAL token i en token-cache. På vissa plattformar måste hur du MSAL att serialisera det här cacheminnet. På de mobila plattformarna Serialiserar MSAL cachen för dig. En ur programmet ansvarar tokens cacheminne för tre saker:

* lagra token i cacheminnet när de har köpt (till exempel med hjälp av den `AcquireTokenInteractive` metod)
* Hämta token från cacheminnet när du anropar den `AcquireTokenSilent` metod
* Hämtar konto metadata från cachen när du anropar den `GetAccount` metod

Om du vill testa cache-scenarier bör du, så skriver ett scenario som skulle:

* Hämta en eller flera token (t.ex, använder [användarnamn/lösenord bevilja](msal-authentication-flows.md#usernamepassword) flödet som är den enklaste för testning)
* Kontrollera att `GetAccounts` fungerar
* Kontrollera att `AcquireTokenSilent` fungerar

Du kanske vill också testa som:

* Starta om appen tas inte bort cachen
* `AcquireTokenSilent` Du kan inte uppdatera uppdateringstoken (det vill säga nätverk anrop till AAD), men hanterar åtkomst-token om det inte har gått ut. Du kan åstadkomma detta och andra HTTP-relaterade scenarier genom att ta kontroll över HTTP-klienten.  En .NET-exempel finns i [att tillhandahålla egna HttpClient](msal-net-provide-httpclient.md)

## <a name="feedback"></a>Feedback
Du kan [logga problem](developer-support-help-options.md#create-a-github-issue) eller ställa frågor som rör testning. Att ge en bra test är ett av målen med teamet.

## <a name="next-steps"></a>Nästa steg
Läs om hur du implementerar [loggning](msal-logging.md) i programmets MSAL.