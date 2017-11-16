---
title: "Stöd för anslutningar med IT Service Management-anslutningstjänsten i Azure Log Analytics | Microsoft Docs"
description: "Anslut din ITSM produkter och tjänster med IT Service Management-anslutningstjänsten i Azure Log Analytics centralt övervaka och hantera arbetsuppgifter ITSM."
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: v-jysur
ms.openlocfilehash: e801d484ffb40a0d4aed517a741c45dc76b62b37
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector-preview"></a>Anslut ITSM produkter och tjänster med IT Service Management-anslutningstjänsten (förhandsgranskning)
Den här artikeln innehåller information om hur du ansluter din ITSM produkter eller tjänster till IT Service Management koppling (ITSMC) i OMS och centralt hantera din arbetsobjekt. Läs mer om ITSMC [ITSMC översikt](log-analytics-itsmc-overview.md).

Följande produkter och tjänster stöds:

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-oms)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-oms)
- [Provance](#connect-provance-to-it-service-management-connector-in-oms)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-oms"></a>Anslut System Center Service Manager till IT-tjänst Management-anslutningstjänsten i OMS

Följande avsnitt innehåller information om hur du ansluter din System Center Service Manager-produkten till ITSMC i OMS.

### <a name="prerequisites"></a>Krav

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [att lägga till IT-tjänsten hanteringslösning Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Service Manager-webbprogram (webbprogram) distribuerats och konfigurerats. Information om webbapp är [här](#create-and-deploy-service-manager-web-app-service).
- Hybridanslutningen skapas och konfigureras. Mer information: [konfigurera hybrid anslutning](#configure-the-hybrid-connection).
- I Service Manager-versioner som stöds: 2012 R2 eller 2016.
- Användarrollen: [avancerad operatör](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Proceduren för anslutning

Använd följande procedur för att ansluta din System Center Service Manager-instans till ITSMC:

1. Gå till **OMS** >**inställningar** > **anslutna källor**.
2. Välj **ITSM Connector** klickar du på **Lägg till ny anslutning**.

    ![Service manager ](./media/log-analytics-itsmc/itsmc-service-manager-connection.png)
3. Ange informationen som beskrivs i följande tabell och klicka på **spara** att skapa anslutningen:

> [!NOTE]
> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Namn**   | Ange ett namn för den System Center Service Manager-instans som du vill ansluta med ITSMC.  Du använder det här namnet senare när du konfigurerar arbetsobjekt i den här instansen eller visa detaljerad logganalys. |
| **Välj typ av anslutning**   | Välj **System Center Service Manager**. |
| **Server-URL**   | Ange Webbadressen till Service Manager-webbprogrammet. Mer information om Service Manager-webbprogrammet [här](#create-and-deploy-service-manager-web-app-service).
| **Klient-ID**   | Ange klient-ID som du skapade (med skriptet för automatisk) för att autentisera webbprogrammet. Mer information om skriptet för automatisk [här.](log-analytics-itsmc-service-manager-script.md)|
| **Klienthemlighet**   | Skriv klienthemligheten genereras för detta ID.   |
| **Omfång för synkronisering av data**   | Välj Service Manager-arbetsobjekt som du vill synkronisera via ITSMC.  Dessa objekt har importerats till logganalys fungerar. **Alternativ:** incidenter, ändringsbegäranden.|
| **Synkronisera Data** | Ange antal föregående dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM produkten. När du väljer skapar OMS berörda konfigurationsobjekt som konfigurationsobjekt (vid CIs) i ITSM-system som stöds. **Som standard**: inaktiverad. |

**När du har anslutits och synkroniserats**:

- Valda objekt från Service Manager har importerats till OMS **logganalys.** Du kan visa en sammanfattning av dessa arbetsobjekt på panelen IT Service Management-anslutningstjänsten.

- Du kan skapa incidenter från OMS-varningar eller loggen Sök eller Azure aviseringar från OMS, i den här instansen för Service Manager.

Mer information: [skapa ITSM arbetsobjekt för aviseringar OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts), [skapa ITSM arbetsobjekt från OMS loggar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs) och [skapa ITSM arbetsobjekt från Azure aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Skapa och distribuera tjänsten för Service Manager web app

För att ansluta lokalt Service Manager med ITSMC på OMS måste har Microsoft skapat ett webbprogram i Service Manager på GitHub.

Ställ in ITSM webbprogrammet för Service Manager genom att göra följande:

- **Distribuera webbappen** – distribuera webbappen, ange egenskaperna och autentisera med Azure AD. Du kan distribuera webbappen med hjälp av den [automatiserade skript](log-analytics-itsmc-service-manager-script.md) att Microsoft tillhandahåller.
- **Konfigurera hybridanslutningen** - [konfigurera den här anslutningen](#configure-the-hybrid-connection)manuellt.

#### <a name="deploy-the-web-app"></a>Distribuera webbappen
Använd det automatiska [skriptet](log-analytics-itsmc-service-manager-script.md) ange egenskaper för att distribuera webbappen och autentisera med Azure AD.

Kör skript genom att tillhandahålla följande obligatoriska uppgifter:

- Information om Azure-prenumeration
- Resursgruppens namn
- Plats
- Information om Service Manager (servernamnet, domän, användarnamn och lösenord)
- Platsen namnprefixet för ditt webbprogram
- ServiceBus-Namespace.

Skriptet skapar webbappen med hjälp av namnet som du angav (tillsammans med några ytterligare strängar som gör det unikt). Den genererar den **Webbappens URL**, **klient-ID** och **klienthemlighet**.

Spara värden använda du dem när du skapar en anslutning med ITSMC.

**Kontrollera installationen av Web-appen**

1. Gå till **Azure-portalen** > **resurser**.
2. Välj webbprogrammet, klicka på **inställningar** > **programinställningar**.
3. Bekräfta informationen om Service Manager-instans som du angav vid tidpunkten för distribution av appen via skriptet.

### <a name="configure-the-hybrid-connection"></a>Konfigurera hybridanslutning

Använd följande procedur för att konfigurera hybridanslutning som ansluter Service Manager-instans med ITSMC i OMS.

1. Hitta Service Manager-webbappen under **Azure-resurser**.
2. Klicka på **inställningar** > **nätverk**.
3. Under **Hybridanslutningar**, klickar du på **konfigurera slutpunkter för din hybridanslutning**.

    ![Hybridnätverk för anslutning](./media/log-analytics-itsmc/itsmc-hybrid-connection-networking-and-end-points.png)
4. I den **Hybridanslutningar** bladet, klickar du på **lägga till hybridanslutning**.

    ![Lägg till hybridanslutning](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-add.png)

5. I den **lägga till Hybridanslutningar** bladet, klickar du på **skapa nya hybrid anslutning**.

    ![Ny hybridanslutning](./media/log-analytics-itsmc/itsmc-create-new-hybrid-connection.png)

6. Ange följande värden:

    - **Namnet på slutpunkten**: Ange ett namn för den nya Hybrid-anslutningen.
    -  **Slutpunkten värden**: FQDN för Service Manager-hanteringsservern.
    - **Port för slutpunkt**: Skriv 5724
    - **Servicebus-namnområdet**: Använd ett befintligt servicebus-namnområde eller skapa en ny.
    - **Plats**: Välj platsen.
    -  **Namnet**: Ange ett namn på servicebus om du skapar den.

    ![Värden för hybrid-anslutning](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-values.png)
6. Klicka på **OK** att stänga den **skapa hybridanslutning** bladet och börjar skapa hybridanslutningen.

    När hybridanslutningen har skapats visas den under bladet.

7. När du har skapat hybridanslutningen Välj anslutningen och klicka på **Lägg till valda hybridanslutning**.

    ![Ny hybridanslutning](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Konfigurera lyssnaren-installation

Använd följande procedur för att konfigurera installationen lyssnare för hybridanslutningen.

1. I den **Hybridanslutningar** bladet, klickar du på **hämta Anslutningshanteraren** och installera det på den dator där System Center Service Manager-instansen körs.

    När installationen är klar, **Hybridanslutningshanteraren UI** alternativet är tillgängligt under **starta** menyn.

2. Klicka på **Hybridanslutningshanteraren UI** , du uppmanas att dina autentiseringsuppgifter för Azure.

3. Logga in med dina autentiseringsuppgifter för Azure och välja din prenumeration där Hybrid-anslutningen har skapats.

4. Klicka på **Spara**.

Din hybridanslutning är korrekt ansluten.

![lyckad hybridanslutning](./media/log-analytics-itsmc/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> När hybrid anslutningen skapas, kontrollera och testa anslutningen genom att gå till distribuerade webbappen för Service Manager. Se till att anslutningen är klar innan du försöker ansluta till ITSMC i OMS.

Följande bild visar information om anslutningen lyckas:

![Hybrid anslutningstestet](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-oms"></a>Ansluta ServiceNow till IT-tjänst Management-anslutningstjänsten i OMS

Följande avsnitt innehåller information om hur du ansluter ServiceNow-produkten till ITSMC i OMS.

### <a name="prerequisites"></a>Krav

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [att lägga till IT-tjänsten hanteringslösning Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ServiceNow stöds versioner – Fuji, Geneva, Helsingfors.

ServiceNow Admins måste göra följande i sina ServiceNow-instans:
- Generera klient-ID och klienthemlighet för ServiceNow-produkten. Mer information om hur du skapar klient-ID och Hemlig finns [OAuth-installationen](http://wiki.servicenow.com/index.php?title=OAuth_Setup).
- Installera appen användaren för Microsoft OMS-integrering (ServiceNow-app). [Läs mer](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0 ).
- Skapa integration användarroll för användaren appen är installerad. Information om hur du skapar användarrollen integration [här](#create-integration-user-role-in-servicenow-app).


### <a name="connection-procedure"></a>**Proceduren för anslutning**
Använd följande procedur för att skapa en ServiceNow-anslutning:

1. Gå till **OMS** > **inställningar** > **anslutna källor**.
2. Välj **ITSM Connector** klickar du på **Lägg till ny anslutning**.

    ![ServiceNow-anslutning](./media/log-analytics-itsmc/itsmc-servicenow-connection.png)

3. Ange informationen som beskrivs i följande tabell och klicka på **spara** att skapa anslutningen:

> [!NOTE]
> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Namn**   | Ange ett namn för ServiceNow-instans som du vill ansluta med ITSMC.  Du kan använda det här namnet senare i OMS när du konfigurerar arbetsobjekt i den här ITSM eller visa detaljerad logganalys. |
| **Välj typ av anslutning**   | Välj **ServiceNow**. |
| **Användarnamn**   | Ange användarnamn integration som du skapade i ServiceNow-app som stöder anslutning till ITSMC. Mer information: [skapa ServiceNow app användarrollen](#create-integration-user-role-in-servicenow-app).|
| **Lösenord**   | Ange lösenordet associerat med det här användarnamnet. **Obs**: användarnamn och lösenord som används för att generera autentiseringstoken endast och lagras inte någonstans i OMS-tjänsten.  |
| **Server-URL**   | Ange Webbadressen till den ServiceNow-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID som du vill använda för OAuth2-autentisering, som du tidigare genererade.  Mer information om genererar klient-ID och Hemlig: [OAuth-installationen](http://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Klienthemlighet**   | Skriv klienthemligheten genereras för detta ID.   |
| **Omfång för synkronisering av data**   | Välj ServiceNow-arbetsobjekt som du vill synkronisera till OMS via ITSMC.  De valda värdena importeras till logganalys.   **Alternativ:** incidenter och ändringsbegäranden.|
| **Synkronisera Data** | Ange antal föregående dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM produkten. När du väljer skapar OMS berörda konfigurationsobjekt som konfigurationsobjekt (vid CIs) i ITSM-system som stöds. **Som standard**: inaktiverad. |


**När du har anslutits och synkroniserats**:

- Valda objekt från ServiceNow-anslutning har importerats till OMS logganalys arbete.  Du kan visa en sammanfattning av dessa arbetsobjekt på panelen IT Service Management-anslutningstjänsten.
- Du kan skapa incidenter, aviseringar och händelser från OMS aviseringar eller loggen Sök eller Azure-aviseringar i den här instansen av ServiceNow.  


Mer information: [skapa ITSM arbetsobjekt för aviseringar OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts), [skapa ITSM arbetsobjekt från OMS loggar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs) och [skapa ITSM arbetsobjekt från Azure aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Skapa en användarroll för integrering i ServiceNow-app

Användare i följande procedur:

1.  Besök den [ServiceNow store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0) och installera den **användaren App för ServiceNow och Microsoft OMS-integrering** till ServiceNow-instans.
2.  Efter installationen finns i det vänstra navigeringsfältet i instansen av ServiceNow, Sök och välj Microsoft OMS integrator.  
3.  Klicka på **checklista för installationen**.

    Status visas som **inte slutföra** om användarrollen ännu har skapats.

4.  I textrutorna bredvid **skapa Integrationsanvändare**, anger användarnamnet för den användare som kan ansluta till ITSMC i OMS.
5.  Ange lösenordet för den här användaren och klicka på **OK**.  

>[!NOTE]

> Du kan använda dessa autentiseringsuppgifter för att ansluta ServiceNow i OMS.

Den nyligen skapade användaren visas med standardrollerna tilldelad.

**Roller som standard**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.User
-   ITIL
-   template_editor
-   view_changer

När användaren har skapats, status för **Kontrollera installationschecklista** flyttar slutförd, med information om rollen skapats för appen.

> [!NOTE]

> Att tillåta en användare att skapa **aviseringar** och **händelser** i ServiceNow från OMS:

> - Kontrollera att du har Event Management-modulen installerad för ServiceNow-instans.

> - Lägg till följande roller till användaren integrering:
>      - evt_mgmt_integration
>      - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-oms"></a>Ansluta Provance till IT-tjänst Management-anslutningstjänsten i OMS

Följande avsnitt innehåller information om hur du ansluter Provance produkten till ITSMC i OMS.


### <a name="prerequisites"></a>Krav

Se till att följande krav är uppfyllda:


- ITSMC installerad. Mer information: [att lägga till IT-tjänsten hanteringslösning Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Provance App ska registreras med Azure AD - och klient-ID görs tillgängligt. Detaljerad information finns i [hur du konfigurerar active directory-autentisering](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Användarrollen: administratör.

### <a name="connection-procedure"></a>Proceduren för anslutning

Använd följande procedur för att skapa en Provance-anslutning:

1. Gå till **OMS** > **inställningar** > **anslutna källor**.
2. Välj **ITSM Connector** klickar du på **Lägg till ny anslutning**.  

    ![Provance anslutning](./media/log-analytics-itsmc/itsmc-provance-connection.png)
3. Ange informationen som beskrivs i följande tabell och klicka på **spara** att skapa anslutningen.

> [!NOTE]
> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Namn**   | Ange ett namn för den Provance-instans som du vill ansluta med ITSMC.  Du kan använda det här namnet senare i OMS när du konfigurerar arbetsobjekt i den här ITSM eller visa detaljerad logganalys. |
| **Välj typ av anslutning**   | Välj **Provance**. |
| **Användarnamn**   | Ange det användarnamn som kan ansluta till ITSMC.    |
| **Lösenord**   | Ange lösenordet associerat med det här användarnamnet. **Obs:** användarnamn och lösenord som används för att generera autentiseringstoken endast och lagras inte någonstans i OMS-tjänsten. _|
| **Server-URL**   | Ange Webbadressen till din Provance-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera den här anslutningen som du genererade i Provance-instans.  Mer information om klient-ID, se [hur du konfigurerar active directory-autentisering](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Omfång för synkronisering av data**   | Välj de Provance arbetsobjekt som du vill synkronisera till OMS via ITSMC.  Dessa objekt har importerats till logganalys fungerar.   **Alternativ:** incidenter, ändringsbegäranden.|
| **Synkronisera Data** | Ange antal föregående dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM produkten. När du väljer skapar OMS berörda konfigurationsobjekt som konfigurationsobjekt (vid CIs) i ITSM-system som stöds. **Som standard**: inaktiverad.|

**När du har anslutits och synkroniserats**:

- Valda objekt från Provance anslutning har importerats till OMS **logganalys.**  Du kan visa en sammanfattning av dessa arbetsobjekt på panelen IT Service Management-anslutningstjänsten.
- Du kan skapa incidenter och händelser från OMS aviseringar eller loggen Sök eller Azure-aviseringar i den här Provance-instansen.

Mer information: [skapa ITSM arbetsobjekt för aviseringar OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts), [skapa ITSM arbetsobjekt från OMS loggar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs) och [skapa ITSM arbetsobjekt från Azure aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-oms"></a>Ansluta Cherwell till IT-tjänst Management-anslutningstjänsten i OMS

Följande avsnitt innehåller information om hur du ansluter Cherwell produkten till ITSMC i OMS.

### <a name="prerequisites"></a>Krav

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [att lägga till IT-tjänsten hanteringslösning Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Klient-ID har genererats. Mer information: [generera klient-ID för Cherwell](#generate-client-id-for-cherwell).
- Användarrollen: administratör.

### <a name="connection-procedure"></a>Proceduren för anslutning

Använd följande procedur för att skapa en Cherwell-anslutning:

1. Gå till **OMS** >  **inställningar** > **anslutna källor**.
2. Välj **ITSM Connector** klickar du på **Lägg till ny anslutning**.  

    ![Cherwell användar-id](./media/log-analytics-itsmc/itsmc-cherwell-connection.png)

3. Ange informationen som beskrivs i följande tabell och klicka på **spara** att skapa anslutningen.

> [!NOTE]
> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Namn**   | Ange ett namn för den Cherwell-instans som du vill ansluta till ITSMC.  Du kan använda det här namnet senare i OMS när du konfigurerar arbetsobjekt i den här ITSM eller visa detaljerad logganalys. |
| **Välj typ av anslutning**   | Välj **Cherwell.** |
| **Användarnamn**   | Skriv användarnamnet Cherwell som kan ansluta till ITSMC. |
| **Lösenord**   | Ange lösenordet associerat med det här användarnamnet. **Obs:** användarnamn och lösenord som används för att generera autentiseringstoken endast och lagras inte någonstans i OMS-tjänsten.|
| **Server-URL**   | Ange Webbadressen till din Cherwell-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera den här anslutningen som du genererade i Cherwell-instans.   |
| **Omfång för synkronisering av data**   | Välj de Cherwell arbetsobjekt som du vill synkronisera via ITSMC.  Dessa objekt har importerats till logganalys fungerar.   **Alternativ:** incidenter, ändringsbegäranden. |
| **Synkronisera Data** | Ange antal föregående dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM produkten. När du väljer skapar OMS berörda konfigurationsobjekt som konfigurationsobjekt (vid CIs) i ITSM-system som stöds. **Som standard**: inaktiverad. |

**När du har anslutits och synkroniserats**:

- Valda objekt från den här anslutningen Cherwell importeras till OMS logganalys arbete. Du kan visa en sammanfattning av dessa arbetsobjekt på panelen IT Service Management-anslutningstjänsten.
- Du kan skapa incidenter och händelser från OMS aviseringar eller loggen Sök eller Azure-aviseringar i den här Cherwell-instansen.

Mer information: [skapa ITSM arbetsobjekt för aviseringar OMS](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts), [skapa ITSM arbetsobjekt från OMS loggar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs) och [skapa ITSM arbetsobjekt från Azure aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generera klient-ID för Cherwell

Använd följande procedur för att generera klient-ID-nyckel för Cherwell:

1. Logga in på din Cherwell-instans som administratör.
2. Klicka på **säkerhet** > **redigera REST API-klientinställningar**.
3. Välj **Skapa ny klient** > **klienthemlighet**.

    ![Cherwell användar-id](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Nästa steg
 - [Skapa ITSM arbetsobjekt för OMS-aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)
 - [Skapa ITSM arbetsobjekt från OMS-loggar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)
 - [Skapa ITSM arbetsobjekt från Azure-aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
 - [Visa logganalys för din anslutning](log-analytics-itsmc-overview.md#using-the-solution)
