---
title: Uppdatera med Logic Apps för Azure Analysis Services-modeller | Microsoft-dokument
description: I den här artikeln beskrivs hur du kodar asynkron uppdatering för Azure Analysis Services med hjälp av Azure Logic Apps.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127045"
---
# <a name="refresh-with-logic-apps"></a>Uppdatera med Logic Apps

Genom att använda Logic Apps- och REST-anrop kan du utföra automatiska datauppdateringsåtgärder på dina Tabellmodeller för Azure Analysis, inklusive synkronisering av skrivskyddade repliker för frågeskalning.

Mer information om hur du använder REST-API:er med Azure Analysis Services finns i [Asynkron uppdatering med REST API](analysis-services-async-refresh.md).

## <a name="authentication"></a>Autentisering

Alla anrop måste autentiseras med en giltig Azure Active Directory -token (OAuth 2).  Exemplen i den här artikeln använder ett servicehuvudnamn (SPN) för att autentisera till Azure Analysis Services. Mer information finns i [Skapa ett tjänsthuvudnamn med hjälp av Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Utforma logikappen

> [!IMPORTANT]
> Följande exempel förutsätter att Azure Analysis Services-brandväggen är inaktiverad. Om brandväggen är aktiverad måste den offentliga IP-adressen för begärandeinitieraren vitlistas i Azure Analysis Services-brandväggen. Mer information om AZURE Logic Apps IP-intervall per region finns i [Gränser och konfigurationsinformation för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Krav

#### <a name="create-a-service-principal-spn"></a>Skapa ett tjänsthuvudnamn (SPN)

Mer information om hur du skapar en tjänsthuvudnamn finns i [Skapa ett huvudnamn för tjänsten med hjälp av Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurera behörigheter i Azure Analysis Services
 
Den tjänstansvarige som du skapar måste ha serveradministratörsbehörighet på servern. Mer information finns i [Lägga till ett tjänsthuvudnamn i serveradministratörsrollen](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Konfigurera Logic App

I det här exemplet är Logic App utformad för att utlösa när en HTTP-begäran tas emot. Detta gör det möjligt att använda ett orchestration-verktyg, till exempel Azure Data Factory, för att utlösa uppdatering av Azure Analysis Services-modellen.

När du har skapat en logikapp:

1. I Logic App-designern väljer du den första åtgärden som **när en HTTP-begäran tas emot**.

   ![Lägga till HTTP-mottagen aktivitet](./media/analysis-services-async-refresh-logic-app/1.png)

Det här steget fylls i med HTTP POST-URL:en när Logic App har sparats.

2. Lägg till ett nytt steg och sök efter **HTTP**.  

   ![Lägga till HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Lägga till HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/10.png)

3. Välj **HTTP** om du vill lägga till den här åtgärden.

   ![Lägga till HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/2.png)

Konfigurera HTTP-aktiviteten på följande sätt:

|Egenskap  |Värde  |
|---------|---------|
|**Metod**     |POST         |
|**Uri**     | https://*serverregionen*/servers/*aas servernamn*/models/*ditt databasnamn*/refreshes <br /> <br /> Till exempel:\/https: /westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Rubriker**     |   Innehåll-Typ, ansökan/json <br /> <br />  ![Rubriker](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Brödtext**     |   Mer information om hur du skapar begärandetexten finns i [Asynkron uppdatering med REST API - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Autentisering**     |Active Directory OAuth         |
|**Klientorganisation**     |Fyll i din Azure Active Directory TenantId         |
|**Målgrupp**     |https://*.asazure.windows.net         |
|**Klient-ID**     |Ange ditt klientnamn för tjänstens huvudnamn         |
|**Typ av autentiseringsuppgifter**     |Hemlighet         |
|**Hemlighet**     |Ange ditt huvudnamnshemlighet för tjänsten         |

Exempel:

![Slutförd HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/7.png)

Testa nu Logikappen.  Klicka på **Kör**i Logic App-designern .

![Testa logikappen](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Använda Logic App med Azure Data Factory

När Logikappen har sparats granskar du aktiviteten **När en HTTP-begäran tas emot** och kopierar sedan HTTP **POST-URL:en** som nu genereras.  Det här är url:en som kan användas av Azure Data Factory för att göra det asynkrona anropet för att utlösa Logic App.

Här är ett exempel på Azure Data Factory Web Activity som gör den här åtgärden.

![Webbaktivitet för Data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Använda en fristående Logic App

Om du inte planerar att använda ett orchestration-verktyg som Data Factory för att utlösa modelluppdateringen kan du ställa in logikappen så att uppdateringen utlöses baserat på ett schema.

Med exemplet ovan tar du bort den första aktiviteten och ersätter den med en **Schemaaktivitet.**

![Schemalägg aktivitet](./media/analysis-services-async-refresh-logic-app/12.png)

![Schemalägg aktivitet](./media/analysis-services-async-refresh-logic-app/13.png)

I det här exemplet används **Återkommande**.

När aktiviteten har lagts till konfigurerar du intervall och frekvens och lägger sedan till en ny parameter och väljer **Vid dessa timmar**.

![Schemalägg aktivitet](./media/analysis-services-async-refresh-logic-app/16.png)

Välj önskade timmar.

![Schemalägg aktivitet](./media/analysis-services-async-refresh-logic-app/15.png)

Spara logikappen.

## <a name="next-steps"></a>Nästa steg

[Prover](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
