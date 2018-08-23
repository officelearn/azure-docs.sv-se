---
title: Autentisering och auktorisering med Power BI-Arbetsytesamlingar | Microsoft Docs
description: Autentisering och auktorisering med Power BI-Arbetsytesamlingar.
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
ms.openlocfilehash: 4bf92638e15767ea254b33e5804977f0d6ab5476
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "42057636"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Autentisering och auktorisering med Power BI-Arbetsytesamlingar

Power BI-Arbetsytesamlingar Använd **nycklar** och **Apptoken** för autentisering och auktorisering istället för explicit slutanvändarautentisering. I den här modellen hanterar programmet autentisering och auktorisering för dina slutanvändare. Om det behövs skapar din app och skickar apptoken som talar om för vår tjänst för att återge den begärda rapporten. Den här designen kräver inte din app att använda Azure Active Directory för autentisering och auktorisering, användare även om du fortfarande kan.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Två sätt att autentisera

**Nyckeln** – du kan använda nycklar för alla Power BI-arbetsyta samlingar REST API-anrop. Nycklarna kan hittas i den **Microsoft Azure-portalen** genom att välja **alla inställningar** och sedan **åtkomstnycklar**. Hantera din nyckel alltid som om det är ett lösenord. Dessa nycklar har behörighet att göra alla REST API-anrop på en viss arbetsytesamling.

Om du vill använda en nyckel på ett REST-anrop, lägger du till följande auktoriseringsrubriken:

    Authorization: AppKey {your key}

**Apptoken** -apptoken som används för alla bädda in förfrågningar. De är avsedd att köras på klientsidan. Token är begränsad till en enda rapport och det praxis att ställa in en förfallotid.

Token som appen är en JWT (JSON Web Token) som har signerats av en av dina nycklar.

Din apptoken kan innehålla följande anspråk:

| Begär | Beskrivning |    
| --- | --- |
| **ver** |Versionen av apptoken. 0.2.0 är den aktuella versionen. |
| **aud** |Den avsedda mottagaren av token. För att använda Power BI-Arbetsytesamlingar: *https:\//analysis.windows.net/powerbi/api*. |
| **ISS** |En sträng som anger det program som utfärdade token. |
| **typ** |Typ av apptoken som håller på att skapas. Aktuella den enda typ som stöds är **bädda in**. |
| **nätverkskonfiguration** |Namn på arbetsytesamling token utfärdas för. |
| **WID** |Arbetsyte-ID token utfärdas för. |
| **RID** |Rapport-ID token utfärdas för. |
| **användarnamn** (valfritt) |Användarnamn är används med RLS en sträng som kan hjälpa dig att identifiera användaren när du använder RLS-regler. |
| **roller** (valfritt) |En sträng som innehåller rollerna som kan väljas när du använder säkerhet på radnivå regler. Om du skickar mer än en roll bör de skickas som en matris för förekomster av textsträngen. |
| **SCP** (valfritt) |En sträng som innehåller omfång för behörigheter. Om du skickar mer än en roll bör de skickas som en matris för förekomster av textsträngen. |
| **EXP** (valfritt) |Anger den tidpunkt som token upphör att gälla. Värdet ska skickas i som Unix tidsstämplar. |
| **NBF** (valfritt) |Anger den tidpunkt som startar token är giltig. Värdet ska skickas i som Unix tidsstämplar. |

Det ser ut som en apptoken för exemplet:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

När avkodas, ut ungefär som:

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

Det finns metoder i SDK: er som underlättar skapandet av apptoken. Till exempel för .NET kan du titta på den [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) klass och [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metoder.

För .NET-SDK kan du referera till [scope](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Omfattningar

När du använder inbäddningstoken kan du begränsa användningen av de resurser som du ger åtkomst till. Därför kan du generera en token med begränsade behörigheter.

Här följer några tillgängliga scope för Power BI-Arbetsytesamlingar.

|Omfång|Beskrivning|
|---|---|
|Dataset.Read|Ger behörighet att läsa den angivna datamängden.|
|Dataset.Write|Ger behörighet att skriva till den angivna datamängden.|
|Dataset.ReadWrite|Ger behörighet att läsa och skriva till den angivna datamängden.|
|Report.Read|Ger behörighet att visa den angivna rapporten.|
|Report.ReadWrite|Ger behörighet att visa och redigera den angivna rapporten.|
|Workspace.Report.Create|Ger behörighet att skapa en ny rapport inom den angivna arbetsytan.|
|Workspace.Report.Copy|Ger behörighet att klona en befintlig rapport i den angivna arbetsytan.|

Du kan ange flera scope med hjälp av ett blanksteg mellan scope som liknar följande.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Begärda anspråk - scope**

SCP: {scopesClaim} scopesClaim kan vara en sträng eller en matris med strängar, notera de tillåtna behörigheterna till arbetsytan resurser (rapport, datauppsättning, osv.)

En avkodade token med omfång definierade, skulle se ut ungefär:

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
|Skapa en ny rapport utifrån en datauppsättning för (i minnet).|Datauppsättning|Dataset.Read|
|Skapa en ny rapport utifrån en datauppsättning för (i minnet) och spara rapporten.|Datauppsättning|* Dataset.Read<br>* Workspace.Report.Create|
|Visa och utforska och redigera (minnesintern) en befintlig rapport. Report.Read innebär Dataset.Read. Report.Read tillåter inte att spara redigeringar.|Rapport|Report.Read|
|Redigera och spara en befintlig rapport.|Rapport|Report.ReadWrite|
|Spara en kopia av en rapport (Spara som).|Rapport|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Så fungerar här flödet
1. Kopiera API-nycklar i ditt program. Du kan hämta nycklar **Azure-portalen**.
   
    ![Var du hittar API-nycklar i Azure portal](media/get-started-sample/azure-portal.png)
1. Token kontrollerar ett anspråk och har en förfallotid.
   
    ![Apptokenflöde - token kontrollerar anspråk](media/get-started-sample/token-2.png)
1. Token hämtar signerade med ett API-åtkomstnycklar.
   
    ![Apptokenflöde - token loggas](media/get-started-sample/token-3.png)
1. Användarförfrågningar om att visa en rapport.
   
    ![Apptokenflöde - användaren begär för att visa en rapport](media/get-started-sample/token-4.png)
1. Token har verifierats med en API-åtkomstnycklar.
   
   ![Apptokenflöde - token har verifierats](media/get-started-sample/token-5.png)
1. Power BI-Arbetsytesamlingar skickas en rapport till användaren.
   
   ![Apptokenflöde - tjänsten skicka rapporten till användare](media/get-started-sample/token-6.png)

Efter **Power BI-Arbetsytesamlingar** skickar en rapport till användaren, användaren kan visa rapporten i din egna app. Exempel: Om du har importerat den [analysera försäljning Data PBIX exempel](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), exempelwebbappen skulle se ut:

![Exempel på rapporten bäddas in i program](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Se även

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Kom igång med Microsoft Power BI-Arbetsytesamlingar exempel](get-started-sample.md)  
[Vanliga scenarier för Microsoft Power BI-Arbetsytesamlingar](scenarios.md)  
[Kom igång med Microsoft Power BI-Arbetsytesamlingar](get-started.md)  
[Power BI-CSharp Git-lagringsplats](https://github.com/Microsoft/PowerBI-CSharp)

Fler frågor? [Försök med Power BI Community](http://community.powerbi.com/)