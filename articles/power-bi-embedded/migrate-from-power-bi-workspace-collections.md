---
title: Så här migrerar du innehåll från Power BI-Arbetsytesamling till Power BI Embedded | Microsoft Docs
description: Lär dig hur du migrerar från Power BI-Arbetsytesamlingar till Power BI Embedded och utnyttjar nyheterna för inbäddning i appar.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: de20d532112ca73f34f7cb603d043579c28179d6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071240"
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Så här migrerar du innehåll från Power BI-Arbetsytesamling till Power BI Embedded

Lär dig hur du migrerar från Power BI-Arbetsytesamlingar till Power BI Embedded. Den här artikeln innehåller vägledning för att migrera från Azure Power BI-Arbetsytesamlingar till Power BI Embedded. Vi också titta på vad som händer för ändringar i programmet.

Power BI-Arbetsytesamlingar resursen fortsätter att vara tillgänglig under en begränsad tid efter allmän tillgänglighet för Power BI Premium-versionen. Kunder under ett Enterprise-avtal har åtkomst till sina befintliga samlingar med arbetsytor via deras nuvarande avtal upphör att gälla. Kunder som anskaffat Power BI-Arbetsytesamlingar via Direktkanaler eller CSP-kanaler ha tillgång i ett år efter allmän tillgänglighet för Power BI Premium.

> [!IMPORTANT]
> Medan migreringen tar ett beroende på Power BI-tjänsten, det är inte ett beroende på Power BI för ditt programs användare när du använder en **inbäddningstoken**. De behöver inte registrera dig för Power BI för att visa det inbäddade innehållet i ditt program. Du kan använda det här inbäddning metoden Bädda in Power BI för dina kunder.

![Power BI Embedded flöde](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Förbereda för migrering

Det finns några saker du behöver göra för att förbereda migreringen från Power BI-Arbetsytesamlingar-tjänsten över till Power BI Embedded. Du behöver en klient som är tillgänglig, tillsammans med en användare som har en Power BI Pro-licens.

1. Kontrollera att du har åtkomst till en Azure Active Directory (Azure AD)-klient.

    Vilken klient du använder?

    * Använda din befintliga företagets Power BI-klient?
    * Använd en separat klient för ditt program?
    * Använda en separat klient för varje kund?

    Om du vill skapa en ny klient för ditt program eller för varje kund finns i följande avsnitt:

    * [Skapa en Azure Active Directory-klient](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Skaffa en Azure Active Directory-klient](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. Skapa en användare i den nya klienten som fungerar som ditt program ”huvud”-konto. Att registrera sig för Power BI konto och måste ha en Power BI Pro-licens till den.

## <a name="accounts-within-azure-ad"></a>Konton i Azure AD

Följande konton måste finnas i din klient.

> [!NOTE]
> Dessa konton måste ha Power BI Pro-licenser för att kunna använda App-arbetsytor.

1. En klientadministratörsanvändare.

    Du rekommenderas att bädda in app-arbetsytan har administratör visas som en medlem.

2. Konton för analytiker som skapar innehåll.

    Dessa användare ska tilldelas apparbetsytor vid behov.

3. Ett program *master* användarkonto eller tjänstkonto.

    Programmets serverdel lagrar autentiseringsuppgifterna för det här kontot. Använd den *master* konto för att skaffa en Azure AD-token för användning med Power BI REST-API: er. Det här kontot används för att generera programmets inbäddningstoken för programmet. Den *master* kontot måste vara administratör för app-arbetsytor som skapats för inbäddning.

    **Det här kontot är bara ett vanligt användarkonto i din organisation som används för inbäddning.**

## <a name="app-registration-and-permissions"></a>Appregistrering och behörigheter

Om du vill göra REST API-anrop, registrera ett program med Azure AD. Ytterligare konfigurationen tillämpas i Microsoft Azure-portalen förutom registreringssidan för Power BI-appen. Mer information finns i [registrera en Azure AD-app för att bädda in Power BI-innehåll](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

Rekommendationen är att registrera programmet med hjälp av programmet **master** konto.

## <a name="create-app-workspaces-required"></a>Skapa apparbetsytor (obligatoriskt)

Om programmet betjänar flera kunder, kan du dra nytta av app-arbetsytor för bättre isolering. Instrumentpaneler och rapporter skulle isoleras mellan kunderna. Du kan sedan använda en Power BI-konto per apparbetsyta och därmed isolera programanvändandet mellan dina kunder, men du kan bara använda ett konto för att hålla det enkelt.

> [!IMPORTANT]
> Du kan inte använda en personlig arbetsyta (en ”Min arbetsyta”) att dra nytta av inbäddning för dina kunder.

Du behöver en användare som har en Pro-licens för att skapa en apparbetsyta i Power BI. Power BI-användare som skapar apparbetsytan är administratör för arbetsytan som standard. **Programmet *master* kontot måste vara administratör för arbetsytan.**

## <a name="content-migration"></a>Migrering av innehåll

Migrering av innehållet från dina samlingar med arbetsytor till Power BI Embedded kan ske parallellt med din aktuella lösning och kräver inget driftstopp.

En **Migreringsverktyget** är tillgänglig för dig att använda för att kopiera innehåll från Power BI-Arbetsytesamlingar till Power BI Embedded. Särskilt om du har många rapporter. Mer information finns i [Migreringsverktyg för Power BI Embedded](migrate-tool.md).

Innehållsmigreringen förlitar sig i huvudsak på två API: er.

1. Hämta PBIX – detta API kan hämta PBIX-filer som har överförts till Power BI efter oktober 2016.
2. Importera PBIX – detta API överför valfri PBIX till Power BI.

Relaterade kodfragment, finns i [kodfragment för att migrera innehåll från Power BI Embedded](migrate-code-snippets.md).

### <a name="report-types"></a>Rapporttyper

Det finns flera typer av rapporter, var och en kräver ett annorlunda migreringsflöde.

#### <a name="cached-dataset-and-report"></a>Cachelagrad datauppsättning och rapport

Cachelagrade datauppsättningar avses PBIX-filer som har importerade data istället för en live-anslutning eller DirectQuery-anslutning.

**Flöde**

1. Anropa Anropshämta PBIX API från din Power BI-Arbetsytesamling arbetsyta.
2. Spara PBIX.
3. Anropa Anropsimportera PBIX för Power BI Embedded-arbetsytan.

#### <a name="directquery-dataset-and-report"></a>DirectQuery-datauppsättning och rapport

**Flöde**

1. Anropa GET `https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources` och spara anslutningssträngen som tagits emot.
2. Anropa Anropshämta PBIX API från din Power BI-Arbetsytesamling arbetsyta.
3. Spara PBIX.
4. Anropa Anropsimportera PBIX för Power BI Embedded-arbetsytan.
5. Uppdatera anslutningssträngen genom att anropa – POST  `https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections`
6. Hämta GW-ID och datakällans ID genom att anropa – hämta `https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources`
7. Uppdatera användarens autentiseringsuppgifter genom att anropa – PATCH `https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}`

#### <a name="old-dataset-and-reports"></a>Gammal datauppsättning och rapporter

Rapporter som överförts innan oktober 2016 inte stöder hämta PBIX-funktionen. 

**Flöde**

1. Hämta PBIX från din utvecklingsmiljö (din interna källkontroll).
2. Anropa Anropsimportera PBIX för Power BI Embedded-arbetsytan.

#### <a name="push-dataset-and-report"></a>Push-datauppsättning och rapport

Hämta PBIX stöder inte *Push API* datauppsättningar. API-datauppsättningsdata för push kan inte porteras från Power BI-Arbetsytesamlingar till Power BI Embedded.

**Flöde**

1. Anropa ”skapa datauppsättningen” API med datauppsättnings-Json för att skapa datauppsättningen för Power BI Embedded-arbetsytan.
2. Återskapa rapporten för den skapade datauppsättningen *.

Det är möjligt använda vissa lösningar för att migrera push api rapport från Power BI-Arbetsytesamlingar till Power BI Embedded genom att göra följande:

1. Ladda upp några dummy-PBIX till Power BI-Arbetsytesamling-arbetsytan.
2. Klona push api rapporten och bind den till dummy-PBIX från steg 1.
3. Hämta push-API-rapporten med dummy-PBIX.
4. Överför dummy-PBIX till Power BI Embedded-arbetsytan.
5. Skapa push-datauppsättning i Power BI Embedded-arbetsytan.
6. Bind om rapporten till push-api-datauppsättningen.

## <a name="create-and-upload-new-reports"></a>Skapa och ladda upp nya rapporter

Utöver det innehåll som du har migrerat från Power BI-Arbetsytesamlingar, kan du skapa rapporter och datauppsättningar som använder Power BI Desktop och publicera dessa rapporter till en app-arbetsyta. Användaren som publicerar rapporterna behöver ha en Power BI Pro-licens för att publicera till en app-arbetsyta.

## <a name="rebuild-your-application"></a>Återskapa ditt program

1. Ändra programmet så att Power BI REST-API: er och rapportplatsen på powerbi.com.

2. Återskapa din AuthN/AuthZ-autentisering med den *master* för ditt program. Du kan dra nytta av med hjälp av en [inbäddningstoken](https://msdn.microsoft.com/library/mt784614.aspx) så att användaren kan agera som ombud för andra användare.

3. Bädda in dina rapporter från Power BI Embedded i ditt program. Mer information finns i [hur du bäddar in dina Power BI-instrumentpaneler, rapporter och paneler](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Mappa dina användare till en Power BI-användare

I ditt program måste du mappa användare som du hanterar i programmet till en *master* Power BI-autentiseringsuppgifter för ditt program. Autentiseringsuppgifterna för den här Power BI *master* konto lagras i ditt program och används för att skapa inbäddningstoken.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Vad du ska göra när du är redo för produktion

När du är redo att flytta till produktion, måste du göra följande:

- Om du använder en separat klient för utveckling, måste du kontrollera att dina apparbetsytor, tillsammans med instrumentpaneler och rapporter, är tillgängliga i din produktionsmiljö. Kontrollera att du skapat programmet i Azure AD för din produktionsklient och tilldelat rätt appbehörigheter som beskrivs i steg 1.

- Köp en kapacitet som passar dina behov. Du kan använda den [analytics kapacitetsplanering för inbäddad White Paper](https://aka.ms/pbiewhitepaper) för att förstå vad du behöver. När du är redo att köpa, kan du köpa en Power BI Embedded-resurs i Azure-portalen.

- Redigera apparbetsytan och tilldela den till en kapacitet under Avancerat.

    ![Tilldela apparbetsytan till en kapacitet i powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Distribuera ditt uppdaterade program till produktion och börja bädda in rapporter från Power BI Embedded.

## <a name="after-migration"></a>Efter migreringen

Rensa krävs inom Power BI-Arbetsytesamlingar.

- Ta bort alla arbetsytor från den distribuerade lösningen i Azure-tjänsten för Power BI-Arbetsytesamlingar.
- Ta bort eventuella Arbetsytesamlingar från Azure.

## <a name="next-steps"></a>Nästa steg

Grattis! Programmet har migrerats till Power BI Embedded. Information om hur du bäddar in Power BI-instrumentpaneler, rapporter och datauppsättningar som finns i [hur du bäddar in dina Power BI-instrumentpaneler, rapporter och paneler](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Fler frågor? [Fråga Power BI Community](http://community.powerbi.com/)