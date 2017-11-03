---
title: "Så här migrerar du Power BI-Arbetsytesamling innehåll till Power BI Embedded | Microsoft Docs"
description: "Lär dig hur du migrerar från Power BI arbetsytan samlingar till Power BI Embedded och utnyttja utvecklingen för inbäddning i appar."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 069f31c8213bd0d8586f7ca50e543acfdad8a2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Så här migrerar du Power BI-Arbetsytesamling innehåll till Power BI Embedded

Lär dig hur du migrerar från Power BI arbetsytan samlingar till Power BI Embedded. Den här artikeln innehåller anvisningar för att migrera från Azure Power BI arbetsytan samlingar till Power BI Embedded. Vi också titta på vad som händer för ändringar i programmet.

Power BI arbetsytan samlingar resursen fortsätter att vara tillgänglig under en begränsad tid efter allmän tillgänglighet för Power BI Premium-versionen. Kunder under ett Enterprise-avtal har åtkomst till sina befintliga arbetsytan samlingar via sina befintliga avtal upphör att gälla. Kunder som anskaffats Power BI arbetsytan samlingar genom direkt eller CSP kanaler få åtkomst för ett år efter allmän tillgänglighet för Power BI Premium.

> [!IMPORTANT]
> När migreringen tar ett beroende på Power BI-tjänsten, det är inte ett beroende på Power BI för användare av ditt program när du använder en **bädda in token**. De behöver inte registrera dig för Power BI om du vill visa det inbäddade innehållet i ditt program. Du kan använda den här bädda in metoden Bädda in Power BI för kunderna.

![Power BI Embedded flöde](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Förbereda för migrering

Det finns några saker du behöver göra för att förbereda för att migrera från arbetsytan samlingar för Power BI-tjänsten över till Power BI Embedded. Du behöver en klient som är tillgängliga, tillsammans med en användare som har en licens för Power BI Pro.

1. Kontrollera att du har åtkomst till en Azure Active Directory (Azure AD)-klient.

    Vilken klient som ska användas?

    * Använda din befintliga företagets Power BI-klient?
    * Använda en separat klient för ditt program?
    * Använda en separat klient för varje kund?

    Om du vill skapa en ny klient för programmet eller varje kund finns i något av följande:

    * [Skapa en Azure Active Directory-klient](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Skaffa en Azure Active Directory-klient](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. Skapa en användare i den här nya klienten som fungerar som ditt program ”överordnad” konto. Att konto måste registrera dig för Power BI och måste ha en licens för Power BI Pro tilldelade.

## <a name="accounts-within-azure-ad"></a>Konton i Azure AD

Följande konton måste finnas inom din klient.

> [!NOTE]
> Dessa konton måste du ha Power BI Pro licenser för att kunna använda appen arbetsytor.

1. En klient administratörsanvändare.

    Du rekommenderas att arbetsytan för inbäddning app har innehavaradministration listats som en medlem.

2. Konton för analytiker som skapar innehåll.

    Dessa användare ska tilldelas till app arbetsytor efter behov.

3. Ett program *master* användarkontot eller -tjänstkontot.

    Serverdelen program lagrar autentiseringsuppgifterna för det här kontot. Använd den *master* konto för att skaffa en Azure AD-token för användning med Power BI REST-API: er. Det här kontot används för att generera inbäddningstoken för programmet. Den *master* konto måste vara administratör för app-arbetsytor skapas för att bädda in.

    **Det här kontot är bara ett vanligt användarkonto i din organisation som används för att bädda in.**

## <a name="app-registration-and-permissions"></a>Appregistrering och behörigheter

Registrera ett program med Azure AD för att göra REST API-anrop. Ytterligare konfiguration används i Microsoft Azure-portalen förutom registreringssidan för Power BI-appen. Mer information finns i [registrerar en Azure AD-app för att bädda in Power BI-innehåll](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

Det rekommenderas att registrera programmet med hjälp av programmet **master** konto.

## <a name="create-app-workspaces-required"></a>Skapa app arbetsytor (krävs)

Om ditt program betjänar flera kunder, kan du dra nytta av appen arbetsytor att tillhandahålla bättre isolering. Instrumentpaneler och rapporter skulle isoleras mellan dina kunder. Du kan sedan använda en Power BI-konto per app arbetsytan för att isolera program upplevelser ytterligare mellan dina kunder, men du kan bara använda ett konto för att enkelhet.

> [!IMPORTANT]
> Du kan inte använda personliga arbetsyta (en ”Min arbetsyta”) att dra nytta av bädda in till dina kunder.

Du måste en användare som har en Pro-licens för att skapa en arbetsyta för appen i Power BI. Power BI-användare som skapar arbetsytan App är administratör för arbetsytan som standard. **Programmet *master* konto måste vara administratör för arbetsytan.**

## <a name="content-migration"></a>Migrering av innehåll

Migrera innehållet från arbetsytan samlingar till Power BI Embedded kan göras parallella till lösningen och kräver inte någon avbrottstid.

En **Migreringsverktyget** är tillgänglig som du kan använda för att kopiera innehållet från Power BI arbetsytan samlingar till Power BI Embedded. Särskilt om du har många rapporter. Mer information finns i [Power BI Embedded Migreringsverktyget](migrate-tool.md).

Innehåll migrering använder främst två API: erna.

1. Hämta PBIX - detta API kan hämta PBIX-filer som har överförts till Power BI efter oktober 2016.
2. Importera PBIX - detta API överför alla PBIX till Power BI.

Vissa relaterade kodstycken finns [kodfragment för att migrera innehåll från Power BI Embedded](migrate-code-snippets.md).

### <a name="report-types"></a>Rapporttyper

Det finns flera typer av rapporter, som kräver en annan migrering flöde.

#### <a name="cached-dataset-and-report"></a>Cachelagrade datasetet och rapporten

Cachelagrade datauppsättningar avser PBIX-filer som har importerat data i stället för en live-anslutning eller DirectQuery-anslutning.

**Flöde**

1. Anropa hämta PBIX-API från Power BI-Arbetsytesamling arbetsytan.
2. Spara PBIX.
3. Anropa importera PBIX för din Power BI Embedded arbetsyta.

#### <a name="directquery-dataset-and-report"></a>DirectQuery datasetet och rapporten

**Flöde**

1. Anropa GET https://api.powerbi.com/v1.0/collections/ {collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources och spara anslutningssträngen som tagits emot.
2. Anropa hämta PBIX-API från Power BI-Arbetsytesamling arbetsytan.
3. Spara PBIX.
4. Anropa importera PBIX för din Power BI Embedded arbetsyta.
5. Uppdatera anslutningssträngen genom att anropa - efter https://api.powerbi.com/v1.0/myorg/datasets/ {dataset_id}/Default.SetAllConnections
6. Hämta GW-ID och datasource-ID genom att anropa - hämta https://api.powerbi.com/v1.0/myorg/datasets/ {dataset_id}/Default.GetBoundGatewayDataSources
7. Uppdatera användarens autentiseringsuppgifter genom att anropa - korrigering https://api.powerbi.com/v1.0/myorg/gateways/ {gateway_id} /datasources/ {datasource_id}

#### <a name="old-dataset-and-reports"></a>Gamla datamängden och rapporter

Rapporter upp innan oktober 2016 inte stöder hämta PBIX-funktionen.

**Flöde**

1. Hämta PBIX från din utvecklingsmiljö (din interna källkontroll).
2. Anropa importera PBIX för din Power BI Embedded arbetsyta.

#### <a name="push-dataset-and-report"></a>Push-datasetet och rapporten

Hämta PBIX stöder inte *Push API* datauppsättningar. Skicka API dataset data inte går att återföra från Power BI arbetsytan samlingar till Power BI Embedded.

**Flöde**

1. Anropa ”skapa dataset” API med datamängd Json att skapa datauppsättningen för din Power BI Embedded arbetsyta.
2. Återskapa rapporten för den skapade dataset *.

Det är möjligt använda vissa lösningar för att migrera push api rapport från Power BI arbetsytan samlingar till Power BI Embedded genom att försöka med följande:

1. Ladda upp vissa dummy PBIX till Power BI-Arbetsytesamling arbetsytan.
2. Klona push api rapportera och bind det till dummy PBIX från steg 1.
3. Hämta push API rapporten med dummy PBIX.
4. Överför dummy PBIX till Power BI Embedded arbetsytan.
5. Skapa push dataset i Power BI Embedded arbetsytan.
6. Binda om rapporten för att push-api dataset.

## <a name="create-and-upload-new-reports"></a>Skapa och ladda upp nya rapporter

Utöver det innehåll som du migrerat från Power BI arbetsytan samlingar, kan du skapa rapporter och datauppsättningar som använder Power BI Desktop och publicera dessa rapporter till en app-arbetsyta. Publicera rapporterna användaren måste ha en licens för Power BI Pro för att publicera till en app-arbetsyta.

## <a name="rebuild-your-application"></a>Återskapa ditt program

1. Ändra ditt program att använda Power BI REST-API: er och platsen för rapporten i powerbi.com.

2. Återskapa din AuthN/AuthZ autentisering med hjälp av den *master* för ditt program. Du kan dra nytta av med hjälp av en [bädda in token](https://msdn.microsoft.com/library/mt784614.aspx) så att användaren ska fungera som ombud för andra användare.

3. Bädda in dina rapporter från Power BI Embedded till programmet. Mer information finns i [så att bädda in din Power BI-instrumentpaneler, rapporter och paneler](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Mappa användare till en Power BI-användare

I ditt program kan du mappa användare som du hanterar i programmet för att en *master* Power BI-autentiseringsuppgifter för ditt program. Autentiseringsuppgifterna för den här Power BI *master* konto lagras i ditt program och användas för att skapa bädda in token.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Vad du ska göra när du är klar för produktion

När du är redo att flytta till produktion, måste du göra följande:

- Om du använder en separat klient för utveckling, måste du kontrollera att din app arbetsytor, tillsammans med instrumentpaneler och rapporter, som är tillgängliga i din produktionsmiljö. Kontrollera att du skapat programmet i Azure AD för din klient för produktion och behörigheterna rätt app som anges i steg 1.

- Köp en kapacitet som passar dina behov. Du kan använda den [inbäddade analytics kapacitetsplanering whitepaper](https://aka.ms/pbiewhitepaper) för att förstå vad du kanske behöver. När du är redo att köpa, kan du köpa en Power BI Embedded resurs i Azure-portalen.

- Redigera arbetsytan App och tilldela den till en kapacitet under avancerade.

    ![Tilldela en kapacitet på powerbi.com app arbetsytan](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Distribuera uppdaterade programmet för produktion och börja bädda in rapporter från Power BI Embedded.

## <a name="after-migration"></a>Efter migreringen

Vissa Rensa krävs i Power BI arbetsytan samlingar.

- Ta bort alla arbetsytor från den distribuerade lösningen i Azure-tjänsten för Power BI arbetsytan samlingar.
- Ta bort några arbetsytan samlingar som finns i Azure.

## <a name="next-steps"></a>Nästa steg

Grattis! Ditt program nu migreras till Power BI Embedded. Information om hur du bäddar in din Power BI-instrumentpaneler, rapporter och datauppsättningar finns [så att bädda in din Power BI-instrumentpaneler, rapporter och paneler](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Fler frågor? [Försök be Power BI-communityn](http://community.powerbi.com/)