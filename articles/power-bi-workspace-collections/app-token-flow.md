---
title: Autentisera och auktorisera med Power BI arbetsytan samlingar | Microsoft Docs
description: Autentisera och auktorisera med Power BI arbetsytan samlingar.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 74d34e708fb74daa295642d50643b78af8f6cb7a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412193"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Autentisera och auktorisera med Power BI arbetsytan samlingar

Power BI arbetsytan samlingar används **nycklar** och **Apptoken** för autentisering och auktorisering, i stället för att explicit slutanvändarens autentisering. I den här modellen hanterar programmet autentisering och auktorisering för dina slutanvändare. Vid behov, din app skapar och skickar apptoken som talar om för vår tjänst för att återge den begärda rapporten. Den här designen kräver inte din app att använda Azure Active Directory för autentisering och auktorisering, även om du fortfarande kan.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Två sätt att autentisera

**Nyckeln** -du kan använda nycklar för alla Power BI arbetsytan samlingar REST API-anrop. Nycklarna kan hittas i den **Microsoft Azure-portalen** genom att välja **alla inställningar** och sedan **åtkomstnycklar**. Behandla alltid din nyckel som om det är ett lösenord. Dessa nycklar har behörighet att göra REST API: er anropa för en viss arbetsytesamling.

Om du vill använda en nyckel på ett REST-anrop, lägger du till följande authorization-huvud:

    Authorization: AppKey {your key}

**Apptoken** -apptoken som används för inbäddning begäranden. De har utformats för att köras på klientsidan. Token är begränsad till en rapport och dess bäst att ange en förfallotid.

Apptoken är en JWT (JSON Web Token) som är signerat av någon av dina nycklar.

App-token kan innehålla följande krav:

| Begär | Beskrivning |
| --- | --- |
| **ver** |Versionen av app-token. 0.2.0 är den aktuella versionen. |
| **eller** |Den avsedda mottagaren av token. Använd Power BI arbetsytan samlingar ”:https://analysis.windows.net/powerbi/api”. |
| **ISS** |En sträng som anger det program som utfärdade token. |
| **typ** |Typen av apptoken som håller på att skapas. Aktuella den enda typ som stöds är **bädda in**. |
| **nätverkskonfiguration** |Arbetsytan samlingsnamn token utfärdas för. |
| **WID** |Arbetsyte-ID token utfärdas för. |
| **RID** |ID token utfärdas för en rapport. |
| **användarnamnet** (valfritt) |Användarnamnet är används med RLS en sträng som kan hjälpa dig att identifiera användaren när du använder RLS regler. |
| **roller** (valfritt) |En sträng som innehåller rollerna kan välja vid tillämpning av säkerhet på radnivå. Om skicka fler än en roll ska de skickas som en förekomster av textsträngen-matris. |
| **SCP** (valfritt) |En sträng som innehåller behörigheter scope. Om skicka fler än en roll ska de skickas som en förekomster av textsträngen-matris. |
| **EXP** (valfritt) |Anger vilket token upphör att gälla. Värdet ska skickas i som Unix tidsstämplar. |
| **NBF** (valfritt) |Anger den tidpunkt som startar token är giltig. Värdet ska skickas i som Unix tidsstämplar. |

Det ser ut som ett exempel app-token:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

När avkodas, ser ut ungefär så:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Det finns metoder i SDK: er som gör det enklare att skapa apptoken. Till exempel för .NET du titta på den [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) klass och [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metoder.

För .NET-SDK kan du gå till [scope](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Omfattningar

När du använder Embed token, kanske du vill begränsa användningen av du ger åtkomst till resurser. Därför kan du generera en token med begränsade behörigheter.

Följande är tillgängliga scope för Power BI arbetsytan samlingar.

|Omfång|Beskrivning|
|---|---|
|Dataset.Read|Ger behörighet att läsa den angivna datamängden.|
|Dataset.Write|Ger behörighet att skriva till den angivna datamängden.|
|Dataset.ReadWrite|Ger behörighet att läsa och skriva till den angivna datamängden.|
|Report.Read|Ger behörighet att visa den angivna rapporten.|
|Report.ReadWrite|Ger behörighet att visa och redigera den angivna rapporten.|
|Workspace.Report.Create|Ger behörighet att skapa en ny rapport på den angivna arbetsytan.|
|Workspace.Report.Copy|Ger behörighet att klona en befintlig rapport på den angivna arbetsytan.|

Du kan ange flera scope med ett blanksteg mellan scope som liknar följande.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Nödvändiga anspråk - scope**

SCP: {scopesClaim} scopesClaim kan vara en sträng eller en matris med strängar som påpekas tillåtna behörigheter till arbetsytan resurser (rapport, Dataset, etc.)

En avkodade token med scope som definierats, skulle se ut ungefär så:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Åtgärder och scope

|Åtgärd|Målresurs|Token behörigheter|
|---|---|---|
|Skapa en ny rapport utifrån en datamängd för (i minnet).|Datauppsättning|Dataset.Read|
|Skapa en ny rapport utifrån en datamängd för (i minnet) och spara rapporten.|Datauppsättning|* Dataset.Read<br>* Workspace.Report.Create|
|Visa och utforska/Redigera (i minnet) en befintlig rapport. Report.Read innebär Dataset.Read. Report.Read tillåter inte att spara ändringarna.|Rapport|Report.Read|
|Redigera och spara en befintlig rapport.|Rapport|Report.ReadWrite|
|Spara en kopia av en rapport (Spara som).|Rapport|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Här är hur flödet fungerar
1. Kopiera API-nycklar till ditt program. Du kan hämta nycklarna i **Azure-portalen**.
   
    ![Var du hittar API-nycklar i Azure-portalen](media/get-started-sample/azure-portal.png)
1. Token Assert ett anspråk och har en förfallotid.
   
    ![Apptokenflöde - token Assert anspråk](media/get-started-sample/token-2.png)
1. Token hämtar signeras med ett API-åtkomstnycklar.
   
    ![Hämtar signerade apptokenflöde - token](media/get-started-sample/token-3.png)
1. Användarförfrågningar om att visa en rapport.
   
    ![Apptokenflöde - användaren begär för att visa en rapport](media/get-started-sample/token-4.png)
1. Token verifieras med en API-åtkomstnycklar.
   
   ![Apptokenflöde - token har verifierats](media/get-started-sample/token-5.png)
1. Power BI arbetsytan samlingar skickar en rapport till användaren.
   
   ![Apptokenflöde - tjänsten skicka rapport till användare](media/get-started-sample/token-6.png)

Efter **Power BI arbetsytan samlingar** skickar en rapport till användaren, användaren kan visa rapporten i din egen app. Till exempel om du har importerat den [analysera försäljning Data PBIX exempel](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), exempelwebbapp skulle se ut:

![Exempel på rapporten bäddas in i program](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Se även

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Komma igång med Microsoft Power BI arbetsytan samlingar exemplet](get-started-sample.md)  
[Vanliga scenarier för Microsoft Power BI arbetsytan samlingar](scenarios.md)  
[Kom igång med Microsoft Power BI arbetsytan samlingar](get-started.md)  
[PowerBI CSharp Git Repo](https://github.com/Microsoft/PowerBI-CSharp)

Fler frågor? [Försök med Power BI Community](http://community.powerbi.com/)