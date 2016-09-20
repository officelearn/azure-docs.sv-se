<properties
    pageTitle="Skapa ett Media Services-konto | Microsoft Azure"
    description="Beskriver hur du skapar ett nytt Azure Media Services-konto i Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/22/2016"
    ms.author="juliako"/>


# Skapa ett Azure Media Services-konto

> [AZURE.SELECTOR]
- [Portalen](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)


> [AZURE.NOTE] Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
 
Den klassiska Azure-portalen är ett sätt att snabbt skapa ett Azure Media Services-konto. Du kan använda ditt konto för att få åtkomst till Media Services för att lagra, kryptera, koda, hantera och strömma medieinnehåll i Azure. När du skapar ett Media Services-konto skapar du också ett associerat lagringskonto (eller använder ett befintligt) i samma geografiska område som Media Services-kontot.

I den här artikeln förklaras hur du använder metoden Snabbregistrering för att skapa ett nytt Media Services-konto och sedan associera det till ett lagringskonto.

<a id="concepts"></a>
## Koncept

Åtkomst till Media Services kräver två associerade konton:

-   **Ett Media Services-konto**. Med ditt konto får du åtkomst till en uppsättning molnbaserade Media Services-tjänster som är tillgängliga i Azure. På ett Media Services-konto lagras inget faktiskt medieinnehåll. I stället lagras metadata om medieinnehåll och mediebearbetningsjobb på ditt konto. När du skapar kontot väljer du en tillgänglig Media Services-region. Den regionen som du väljer är ett datacenter som lagrar metadataposterna för ditt konto.

    Tillgängliga Media Services-regioner (AMS) är bland annat följande: Nordeuropa, Västeuropa, västra USA, östra USA, Sydostasien, Östasien samt västra och östra Japan. Media Services använder inte tillhörighetsgrupper.
    
    AMS finns nu även i följande datacenter: södra Brasilien, västra Indien, södra Indien och centrala Indien. Nu kan du använda den klassiska Azure-portalen för att [skapa Media Service-konton](media-services-create-account.md#create-a-media-services-account-using-quick-create) och utföra olika uppgifter som beskrivs [här](https://azure.microsoft.com/documentation/services/media-services/). Dock är Live Encoding inte aktiverat i dessa datacenter. Dessutom finns inte alla typer av Encoding-reserverade enheter i dessa datacenter.
    
    - Södra Brasilien:                                          Endast Encoding-reserverade enheter av grundläggande och standardtyp är tillgängliga
    - Västra Indien, södra Indien och centrala Indien:              Endast Encoding-reserverade enheter av grundläggande typ är tillgängliga


-   **Ett associerat lagringskonto**. Ditt lagringskonto är ett Azure-lagringskonto som är associerat till ditt Media Services-konto. Lagringskontot tillhandahåller Blob Storage för mediefiler och måste finnas i samma geografiska region som Media Services-kontot. När du skapar ett Media Services-konto kan du antingen välja ett befintligt lagringskonto i samma region eller skapa ett nytt lagringskonto i samma region. Om du tar bort ett Media Services-konto raderas inte blobbarna på ditt relaterade lagringskonto.

<a id="quick"></a>
## Skapa ett Media Services-konto med Snabbregistrering

1. I den [Klassisk Azure-portal][] klickar du på **Ny**, på **Media Service** och sedan på **Snabbregistrering**.

![Snabbregistrering för Media Services](./media/media-services-create-account/wams-QuickCreate.png)

2. Vid **NAMN** anger du namnet på det nya kontot. Namnet på ett Media Services-konto består av gemena bokstäver eller siffror utan blanksteg och 3 till 24 tecken.

3. Vid **REGION** väljer du den geografiska region som ska användas för att lagra metadataposter för ditt Media Services-konto. Endast de tillgängliga Media Services-regionerna visas i listrutan.

4. Vid **LAGRINGSKONTO** väljer du ett Storage-konto för att tillhandahålla Blob Storage för medieinnehållet från ditt Media Services-konto. Du kan välja ett befintligt lagringskonto i samma geografiska region som ditt Media Services-konto eller skapa ett nytt lagringskonto. Ett nytt lagringskonto skapas i samma region.

5. Om du har skapat ett nytt lagringskonto anger du ett namn för det i **NYTT LAGRINGSKONTONAMN**. Reglerna för namn på lagringskonton är desamma som för Media Services-konton.

6. Klicka på **Snabbregistrering** längst ned i formuläret.

Du kan övervaka statusen för processen i meddelandeområdet längst ned i fönstret.

Statusen ändras till aktiv när kontot har skapats. **Media Services**-sidan öppnas och det nya kontot visas.

Längst ned på sidan visas knappen **HANTERA NYCKLAR**. När du klickar på den här knappen, visas en sida med namnet på Media Services-kontot och de primära och sekundära nycklarna. Du behöver ha namnet på kontot och information om den primära nyckeln för att komma åt Media Services-kontot programmässigt.

![Media Services-sidan](./media/media-services-create-account/wams-mediaservices-page.png)

När du dubbelklickar på kontonamnet visas sidan **Snabbstart** som standard. På den här sidan kan du utföra viss hantering som även är tillgänglig på andra sidor i portalen. Du kan till exempel överföra en videofil från den här sidan eller från sidan **INNEHÅLL**.

Du kan dessutom visa kod som använder Azure Media Services SDK för att utföra följande uppgifter: överföra, koda och publicera videoklipp. Du kan klicka på en av länkarna under avsnittet **SKRIVA KOD**, kopiera koden och använda den i din app.



##Utbildningsvägar för Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Ge feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## Nästa steg

- [Komma igång med att leverera Video-on-Demand-innehåll (VoD) med hjälp av .NET-SDK](media-services-dotnet-get-started.md)

- [Använd .NET SDK för att skapa kanaler som utför Live Encoding från en dataström enkel bithastighet till en dataström med multibithastighet](media-services-dotnet-creating-live-encoder-enabled-channel.md)

<!-- Reusable paths. -->

<!-- Anchors. -->
  [Koncept]: #concepts
  [Innan du börjar]: #begin
  [Så här gör du: Skapa ett Media Services-konto med Snabbregistrering]: #quick

<!-- URLs. -->
  [Installationsprogram för webbplattform ]: http://go.microsoft.com/fwlink/?linkid=255386

  [Klassisk Azure-portal]: http://manage.windowsazure.com/



<!--HONumber=sep16_HO1-->


