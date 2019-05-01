---
title: Uppdatera med Logic Apps för Azure Analysis Services-modeller | Microsoft Docs
description: Lär dig hur du kodar asynkron uppdatering med hjälp av Azure Logic Apps.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6e1ac5dfd1972e406a1bd8dcd26e6aef2c4ea6d1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919865"
---
# <a name="refresh-with-logic-apps"></a>Uppdatera med Logic Apps

Du kan utföra automatiserade datauppdateringar på din Azure Analysis-tabellmodeller, inklusive synkronisering av skrivskyddade repliker för frågeutskalning genom att använda Logic Apps och REST-anrop.

Mer information om hur du använder REST API: er med Azure Analysis Services finns [asynkron uppdatering med REST API](analysis-services-async-refresh.md).

## <a name="authentication"></a>Autentisering

Alla anrop måste autentiseras med en giltig token för Azure Active Directory (OAuth 2).  Exemplen i den här artikeln använder tjänstens huvudnamn (SPN) för att autentisera till Azure Analysis Services. Mer information finns i [skapa ett huvudnamn för tjänsten med hjälp av Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Utforma Logic App

> [!IMPORTANT]
> I följande exempel förutsätter att Azure Analysis Services-brandväggen är inaktiverad.  Om en brandvägg har aktiverats, måste offentliga IP-adressen för begäran-initierare bli godkänd i Azure Analysis Services-brandväggen. Läs mer om Logic App IP-adressintervall per region i [begränsningar och konfigurationsinformation för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Nödvändiga komponenter

#### <a name="create-a-service-principal-spn"></a>Skapa tjänstens huvudnamn (SPN)

Läs om hur du skapar ett huvudnamn för tjänsten i [skapa ett huvudnamn för tjänsten med hjälp av Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurera behörigheter i Azure Analysis Services
 
Tjänstens huvudnamn som du skapar måste ha administratörsbehörighet för servern på servern. Mer information finns i [lägga till tjänstens huvudnamn till administratörsrollen för servern](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Konfigurera Logikappen

I det här exemplet är Logikappen utformad för att utlösa när en HTTP-begäran tas emot. Detta aktiverar användningen av en orchestration-verktyg, till exempel Azure Data Factory att utlösa modelluppdatering Azure Analysis Services.

När du har skapat en Logikapp:

1. I Logic App designer, väljer du den första åtgärden som **när en HTTP-begäran tas emot**.

   ![Lägg till tog emot HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/1.png)

Det här steget fylls med HTTP post-URL när Logikappen har sparats.

2. Lägg till ett nytt steg och Sök efter **HTTP**.  

   ![Lägg till HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Lägg till HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/10.png)

3. Välj **HTTP** att lägga till den här åtgärden.

   ![Lägg till HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/2.png)

Konfigurera HTTP-aktivitet på följande sätt:

|Egenskap   |Värde  |
|---------|---------|
|**Metod**     |POST         |
|**URI**     | https://*din server region*/servers/*aas servernamn*/models/*databasnamnet*/ <br /> <br /> Exempel: https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**Headers**     |   Content-Type application/json <br /> <br />  ![Rubriker](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Brödtext**     |   Läs mer om som utgör begärandetexten i [asynkron uppdatering med REST API - INLÄGGET /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Autentisering**     |Active Directory OAuth         |
|**klient**     |Fyll i din Azure Active Directory-Tenantd         |
|**Målgrupp**     |https://*.asazure.windows.net         |
|**Klient-ID**     |Ange din Service Principal Name ClientID         |
|**Typ av autentiseringsuppgift**     |Hemlighet         |
|**Hemlighet**     |Ange ditt namn hemlighet för tjänstens huvudnamn         |

Exempel:

![Slutförda HTTP-aktivitet](./media/analysis-services-async-refresh-logic-app/7.png)

Nu ska du testa Logikappen.  I Logic App designer klickar du på **kör**.

![Testa Logikappen](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Använda Logic App med Azure Data Factory

När Logikappen har sparats kan du granska den **när en HTTP-begäran tas emot** aktivitet och kopiera sedan den **HTTP post-URL** som genereras nu.  Det här är den URL som kan användas av Azure Data Factory för att göra asynkrona anrop att utlösa Logikappen.

Här är ett exempel Azure Data Factory Web-aktivitet som utför den här åtgärden.

![Webbaktivitet för data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Använda en självständig Logikapp

Om du inte tänker använda ett Orchestration-verktyg som Data Factory för att utlösa modelluppdatering, kan du ange logikappen som ska utlösa den uppdatering som enligt ett schema.

Använd exemplet ovan och ta bort den första aktiviteten och Ersätt den med en **schema** aktivitet.

![Schema-aktivitet](./media/analysis-services-async-refresh-logic-app/12.png)

![Schema-aktivitet](./media/analysis-services-async-refresh-logic-app/13.png)

Det här exemplet används **upprepning**.

När aktiviteten har lagts till, konfigurera intervall och frekvens, och sedan lägga till en ny parameter och välj **vid dessa timmar**.

![Schema-aktivitet](./media/analysis-services-async-refresh-logic-app/16.png)

Välj önskad timmar.

![Schema-aktivitet](./media/analysis-services-async-refresh-logic-app/15.png)

Spara Logikappen.

## <a name="next-steps"></a>Nästa steg

[Exempel](analysis-services-samples.md)  
[REST-API](https://docs.microsoft.com/rest/api/analysisservices/servers)
