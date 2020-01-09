---
title: Autentisera och auktorisera-Power BI arbets ytans samlingar
description: Autentisering och auktorisering med Power BI arbets ytans samlingar.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 8fcd7caffb041c57090d7256361421cb49a9a5fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427116"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Autentisering och auktorisering med Power BI arbets ytans samlingar

Power BI arbets ytans samlingar använder **nycklar** och **app-token** för autentisering och auktorisering, i stället för explicit slut användar autentisering. I den här modellen hanterar ditt program autentisering och auktorisering för dina slutanvändare. Vid behov skapar din app och skickar de app-token som ber tjänsten att återge den begärda rapporten. Den här designen kräver inte att din app använder Azure Active Directory för användarautentisering och auktorisering, även om du fortfarande kan.

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Två sätt att autentisera

**Nyckel** – du kan använda nycklar för alla samlingar med Power BI-arbetsytor REST API anrop. Du hittar nycklarna i **Microsoft Azure-portalen** genom att välja **alla inställningar** och sedan **åtkomst nycklar**. Behandla alltid nyckeln som om det är ett lösen ord. Dessa nycklar har behörighet att göra REST API-anrop på en viss arbets yta samling.

Om du vill använda en nyckel på ett REST-anrop lägger du till följande Authorization-huvud:

    Authorization: AppKey {your key}

**App-token** – app-token används för alla inbäddnings begär Anden. De är avsedda att köras på klient sidan. Token är begränsad till en enda rapport och dess bästa praxis att ange en förfallo tid.

App-token är ett JWT (JSON Web Token) som är signerat av en av dina nycklar.

Din app-token kan innehålla följande anspråk:

| Begär | Beskrivning |    
| --- | --- |
| **ver** |Versionen av app-token. 0.2.0 är den aktuella versionen. |
| **AUD** |Den avsedda mottagaren av token. För Power BI arbets ytans samlingar använder du: *https:\//Analysis.Windows.net/PowerBI/API*. |
| **ISS** |En sträng som anger det program som utfärdade token. |
| **typ** |Den typ av app-token som skapas. Aktuell den enda typ som stöds är **embed**. |
| **wcn** |Namn på arbets ytans samling som token utfärdas för. |
| **WID** |Arbetsyte-ID som token utfärdas för. |
| **rid** |Rapport-ID som token utfärdas för. |
| **användar namn** (valfritt) |Som används med RLS är username en sträng som kan hjälpa dig att identifiera användaren när du tillämpar RLS-regler. |
| **roller** (valfritt) |En sträng som innehåller de roller som ska väljas när du tillämpar säkerhets regler på radnivå. Om du skickar mer än en roll ska de skickas som en STING-matris. |
| **SCP** (valfritt) |En sträng som innehåller behörighets omfattningarna. Om du skickar mer än en roll ska de skickas som en STING-matris. |
| **exp** (valfritt) |Anger tiden då token upphör att gälla. Värdet ska skickas in som UNIX-tidsstämplar. |
| **NBF** (valfritt) |Anger tiden då token börjar gälla. Värdet ska skickas in som UNIX-tidsstämplar. |

Ett exempel på en app-token ser ut så här:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Vid avkodning ser det ut ungefär så här:

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

Det finns metoder som är tillgängliga i SDK: er som gör det enklare att skapa app-token. För .NET kan du till exempel titta på klassen [Microsoft. powerbi. Security. PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) och [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) -metoderna.

För .NET SDK kan du referera till [omfång](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Omfattningar

När du använder embed-token kanske du vill begränsa användningen av de resurser som du ger åtkomst till. Därför kan du generera en token med begränsad behörighet.

Följande är tillgängliga omfattningar för Power BI samlingar av arbets ytor.

|Omfång|Beskrivning|
|---|---|
|Data uppsättning. Läs|Ger behörighet att läsa den angivna data mängden.|
|Data uppsättning. Write|Ger behörighet att skriva till den angivna data mängden.|
|Dataset. ReadWrite|Ger behörighet att läsa och skriva till den angivna data mängden.|
|Rapport. Läs|Ger behörighet att visa den angivna rapporten.|
|Report. ReadWrite|Ger behörighet att visa och redigera den angivna rapporten.|
|Workspace.Report.Create|Ger behörighet att skapa en ny rapport i den angivna arbets ytan.|
|Workspace.Report.Copy|Ger behörighet att klona en befintlig rapport inom den angivna arbets ytan.|

Du kan ange flera omfång genom att använda ett blank steg mellan omfattningarna som nedan.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Obligatoriska anspråk – omfattningar**

SCP: {scopesClaim} scopesClaim kan vara antingen en sträng eller en sträng mat ris som anger tillåtna behörigheter till arbets ytans resurser (rapport, data uppsättning osv.)

En avkodad token, med definierade omfattningar, ser ut ungefär så här:

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

### <a name="operations-and-scopes"></a>Åtgärder och omfång

|Åtgärd|Målresurs|Token-behörigheter|
|---|---|---|
|Skapa en ny rapport (i minnet) som baseras på en data uppsättning.|Datauppsättning|Data uppsättning. Läs|
|Skapa en ny rapport (i minnet) som baseras på en data uppsättning och spara rapporten.|Datauppsättning|* Data mängd. Läs<br>* Arbets yta. rapport. skapa|
|Visa och utforska/redigera (i minnet) en befintlig rapport. Report. Read antyder DataSet. Read. Report. Read tillåter inte sparande av redigeringar.|Rapport|Rapport. Läs|
|Redigera och spara en befintlig rapport.|Rapport|Report. ReadWrite|
|Spara en kopia av en rapport (Spara som).|Rapport|* Rapport. Läs<br>* Arbets yta. rapport. kopia|

## <a name="heres-how-the-flow-works"></a>Så här fungerar flödet
1. Kopiera API-nycklarna till ditt program. Du kan hämta nycklarna i **Azure Portal**.
   
    ![Var du hittar API-nycklarna i Azure Portal](media/get-started-sample/azure-portal.png)
1. Token förutsätter ett anspråk och har en förfallo tid.
   
    ![Anspråk för token för app-token](media/get-started-sample/token-2.png)
1. Token signeras med en API-åtkomst nycklar.
   
    ![App-token Flow-token har signerats](media/get-started-sample/token-3.png)
1. Användare begär att visa en rapport.
   
    ![Flöde för app-token – användar förfrågningar för att visa en rapport](media/get-started-sample/token-4.png)
1. Token verifieras med en API-åtkomst nycklar.
   
   ![App token Flow-token har verifierats](media/get-started-sample/token-5.png)
1. Power BI arbets ytans samlingar skickar en rapport till användaren.
   
   ![App-token Flow-skicka rapport till användare](media/get-started-sample/token-6.png)

När **Power BI arbets ytans samlingar** skickar en rapport till användaren kan användaren Visa rapporten i din anpassade app. Om du till exempel har importerat [exemplet för att analysera försäljnings data PBIX](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)skulle exempel webb programmet se ut så här:

![Exempel på inbäddad rapport i program](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Se även

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Kom igång med samlings exempel för Microsoft Power BI-arbetsyta](get-started-sample.md)  
[Vanliga scenarier för samlingar med Microsoft Power BI-arbetsytor](scenarios.md)  
[Kom igång med samlingar för Microsoft Power BI-arbetsytor](get-started.md)  
[PowerBI-CSharp git-lagrings platsen](https://github.com/Microsoft/PowerBI-CSharp)

Fler frågor? [Försök med Power BI Community](https://community.powerbi.com/)
