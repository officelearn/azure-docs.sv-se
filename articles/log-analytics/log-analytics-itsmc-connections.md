---
title: Stöd för anslutningar med IT Service Management-anslutningstjänsten i Azure Log Analytics | Microsoft Docs
description: Den här artikeln innehåller information om hur du ansluter din ITSM produkter och tjänster med IT Service Management koppling (ITSMC) i OMS logganalys centralt övervaka och hantera arbetsuppgifter ITSM.
documentationcenter: ''
author: JYOTHIRMAISURI
manager: riyazp
editor: ''
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: fdf3c7d1a6621437c414e2c1fe0628f2f585d98a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Anslut ITSM produkter och tjänster med IT Service Management-anslutningstjänsten
Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan din ITSM produkter eller tjänster och IT Service Management-koppling (ITSMC) i logganalys centralt hantera din arbetsobjekt. Läs mer om ITSMC [översikt](log-analytics-itsmc-overview.md).

Följande produkter och tjänster i ITSM stöds. Välj produkt att visa detaljerad information om hur du ansluter produkten till ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]

> ITSM anslutningen kan endast ansluta till molnbaserade ServiceNow-instanser. Lokala ServiceNow instanser stöds inte för närvarande.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Anslut System Center Service Manager till IT-tjänst Management-anslutningstjänsten i Azure

Följande avsnitt innehåller information om hur du ansluter din System Center Service Manager-produkten till ITSMC i Azure.

### <a name="prerequisites"></a>Förutsättningar

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [att lägga till IT-tjänsten hanteringslösning Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Service Manager-webbprogram (webbprogram) distribuerats och konfigurerats. Information om webbapp är [här](#create-and-deploy-service-manager-web-app-service).
- Hybridanslutningen skapas och konfigureras. Mer information: [konfigurera hybrid anslutning](#configure-the-hybrid-connection).
- I Service Manager-versioner som stöds: 2012 R2 eller 2016.
- Användarrollen: [avancerad operatör](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Proceduren för anslutning

Använd följande procedur för att ansluta din System Center Service Manager-instans till ITSMC:

1. I Azure-portalen går du till **alla resurser** och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Under **ARBETSYTAN DATAKÄLLOR** klickar du på **ITSM anslutningar**.

    ![Ny anslutning](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Längst upp i den högra rutan, klickar du på **Lägg till**.

4. Ange informationen som beskrivs i följande tabell och klicka på **OK** att skapa anslutningen.

> [!NOTE]

> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Ange ett namn för den System Center Service Manager-instans som du vill ansluta med ITSMC.  Du använder det här namnet senare när du konfigurerar arbetsobjekt i den här instansen eller visa detaljerad logganalys. |
| **Typ av**   | Välj **System Center Service Manager**. |
| **Server-URL**   | Ange Webbadressen till Service Manager-webbprogrammet. Mer information om Service Manager-webbprogrammet [här](#create-and-deploy-service-manager-web-app-service).
| **Klient-ID**   | Ange klient-ID som du skapade (med skriptet för automatisk) för att autentisera webbprogrammet. Mer information om skriptet för automatisk [här.](log-analytics-itsmc-service-manager-script.md)|
| **Klienthemlighet**   | Skriv klienthemligheten genereras för detta ID.   |
| **Omfång för synkronisering av data**   | Välj Service Manager-arbetsobjekt som du vill synkronisera via ITSMC.  Dessa objekt har importerats till logganalys fungerar. **Alternativ:** incidenter, ändringsbegäranden.|
| **Synkronisera Data** | Ange antal föregående dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM produkten. När du väljer skapar OMS berörda konfigurationsobjekt som konfigurationsobjekt (vid CIs) i ITSM-system som stöds. **Som standard**: inaktiverad. |

![Service manager-anslutning](./media/log-analytics-itsmc/service-manager-connection.png)

**När du har anslutits och synkroniserats**:

- Valda objekt från Service Manager har importerats till Azure **logganalys.** Du kan visa en sammanfattning av dessa arbetsobjekt på panelen IT Service Management-anslutningstjänsten.

- Du kan skapa incidenter från logganalys varningar eller loggposter eller Azure-aviseringar i den här instansen för Service Manager.


Läs mer: [skapa ITSM arbetsobjekt för Log Analytics aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [skapa ITSM arbetsobjekt från logganalys loggar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) och [skapa ITSM arbetsobjekt från Azure aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Skapa och distribuera tjänsten för Service Manager web app

Microsoft har skapat ett webbprogram i Service Manager på GitHub för att ansluta lokalt Service Manager med ITSMC i Azure.

Ställ in ITSM webbprogrammet för Service Manager genom att göra följande:

- **Distribuera webbappen** – distribuera webbappen, ange egenskaperna och autentisera med Azure AD. Du kan distribuera webbappen med hjälp av den [automatiserade skript](log-analytics-itsmc-service-manager-script.md) att Microsoft tillhandahåller.
- **Konfigurera hybridanslutningen** - [konfigurera den här anslutningen](#configure-the-hybrid-connection)manuellt.

#### <a name="deploy-the-web-app"></a>Distribuera webbappen
Använd det automatiska [skriptet](log-analytics-itsmc-service-manager-script.md) ange egenskaper för att distribuera webbappen och autentisera med Azure AD.

Kör skript genom att tillhandahålla följande obligatoriska uppgifter:

- Information om Azure-prenumeration
- Resursgruppsnamn
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

Använd följande procedur för att konfigurera hybridanslutning som ansluter Service Manager-instans med ITSMC i Azure.

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

> När hybrid anslutningen skapas, kontrollera och testa anslutningen genom att gå till distribuerade webbappen för Service Manager. Se till att anslutningen är klar innan du försöker ansluta till ITSMC i Azure.

Följande exempel bilden visar information om anslutningen lyckas:

![Hybrid anslutningstestet](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Ansluta ServiceNow till IT-tjänst Management-anslutningstjänsten i Azure

Följande avsnitt innehåller information om hur du ansluter ServiceNow-produkten till ITSMC i Azure.

### <a name="prerequisites"></a>Förutsättningar
Se till att följande krav är uppfyllda:
- ITSMC installerad. Mer information: [att lägga till IT-tjänsten hanteringslösning Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ServiceNow-versioner som stöds: Kingston, Djakarta, Istanbul, Helsingfors, Geneva.

**ServiceNow Admins måste göra följande i sina instansen av ServiceNow**:
- Generera klient-ID och klienthemlighet för ServiceNow-produkten. Information om hur du skapar klient-ID och Hemlig, finns följande information som krävs:

    - [Konfigurera OAuth för Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/concept/OAuth-setup.html)
    - [Konfigurera OAuth för Djakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Helsingfors](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Installera appen användaren för Microsoft OMS-integrering (ServiceNow-app). [Läs mer](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Skapa integration användarroll för användaren appen är installerad. Information om hur du skapar användarrollen integration [här](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Proceduren för anslutning**
Använd följande procedur för att skapa en ServiceNow-anslutning:


1. I Azure-portalen går du till **alla resurser** och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Under **ARBETSYTAN DATAKÄLLOR** klickar du på **ITSM anslutningar**.
    ![Ny anslutning](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Längst upp i den högra rutan, klickar du på **Lägg till**.

4. Ange informationen som beskrivs i följande tabell och klicka på **OK** att skapa anslutningen.


> [!NOTE]
> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Ange ett namn för ServiceNow-instans som du vill ansluta med ITSMC.  Du kan använda det här namnet senare i OMS när du konfigurerar arbetsobjekt i den här ITSM eller visa detaljerad logganalys. |
| **Typ av**   | Välj **ServiceNow**. |
| **Användarnamn**   | Ange användarnamn integration som du skapade i ServiceNow-app som stöder anslutning till ITSMC. Mer information: [skapa ServiceNow app användarrollen](#create-integration-user-role-in-servicenow-app).|
| **Lösenord**   | Ange lösenordet associerat med det här användarnamnet. **Obs**: användarnamn och lösenord som används för att generera autentiseringstoken endast och lagras inte någonstans i tjänsten ITSMC.  |
| **Server-URL**   | Ange Webbadressen till den ServiceNow-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID som du vill använda för OAuth2-autentisering, som du tidigare genererade.  Mer information om genererar klient-ID och Hemlig: [OAuth-installationen](http://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Klienthemlighet**   | Skriv klienthemligheten genereras för detta ID.   |
| **Omfång för synkronisering av data**   | Välj ServiceNow-arbetsobjekt som du vill synkronisera till Azure logganalys via ITSMC.  De valda värdena importeras till logganalys.   **Alternativ:** incidenter och ändringsbegäranden.|
| **Synkronisera Data** | Ange antal föregående dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM produkten. När du väljer skapar ITSMC berörda konfigurationsobjekt som konfigurationsobjekt (vid CIs) i ITSM-system som stöds. **Som standard**: inaktiverad. |

![ServiceNow-anslutning](./media/log-analytics-itsmc/itsm-connection-servicenow-connection-latest.png)

**När du har anslutits och synkroniserats**:

- Valda objekt från instansen av ServiceNow importeras till Azure **logganalys.** Du kan visa en sammanfattning av dessa arbetsobjekt på panelen IT Service Management-anslutningstjänsten.

- Du kan skapa incidenter från logganalys varningar eller loggposter eller Azure-aviseringar i den här instansen av ServiceNow.

Läs mer: [skapa ITSM arbetsobjekt för Log Analytics aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [skapa ITSM arbetsobjekt från logganalys loggar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) och [skapa ITSM arbetsobjekt från Azure aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Skapa en användarroll för integrering i ServiceNow-app

Användare i följande procedur:

1.  Besök den [ServiceNow store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) och installera den **användaren App för ServiceNow och Microsoft OMS-integrering** till ServiceNow-instans.
2.  Efter installationen finns i det vänstra navigeringsfältet i instansen av ServiceNow, Sök och välj Microsoft OMS integrator.  
3.  Klicka på **checklista för installationen**.

    Status visas som **inte slutföra** om användarrollen ännu har skapats.

4.  I textrutorna bredvid **skapa Integrationsanvändare**, anger användarnamnet för den användare som kan ansluta till ITSMC i Azure.
5.  Ange lösenordet för den här användaren och klicka på **OK**.  

>[!NOTE]

> Du kan använda dessa autentiseringsuppgifter för att ansluta ServiceNow i Azure.

Den nyligen skapade användaren visas med standardrollerna tilldelad.

**Roller som standard**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   ITIL
-   template_editor
-   view_changer

När användaren har skapats, status för **Kontrollera installationschecklista** flyttar slutförd, med information om rollen skapats för appen.

> [!NOTE]

> ITSM kopplingen kan skicka incidenter till ServiceNow utan några moduler som installerats på ServiceNow-instansen. Om du använder EventManagement modul i din ServiceNow-instans och vill skapa händelser eller varningar i ServiceNow med connector, lägger du till följande roller till användaren integrering:

>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Ansluta Provance till IT-tjänst Management-anslutningstjänsten i Azure

Följande avsnitt innehåller information om hur du ansluter Provance produkten till ITSMC i Azure.


### <a name="prerequisites"></a>Förutsättningar

Se till att följande krav är uppfyllda:


- ITSMC installerad. Mer information: [att lägga till IT-tjänsten hanteringslösning Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Provance App ska registreras med Azure AD - och klient-ID görs tillgängligt. Detaljerad information finns i [hur du konfigurerar active directory-autentisering](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Användarrollen: administratör.

### <a name="connection-procedure"></a>Proceduren för anslutning

Använd följande procedur för att skapa en Provance-anslutning:

1. I Azure-portalen går du till **alla resurser** och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Under **ARBETSYTAN DATAKÄLLOR** klickar du på **ITSM anslutningar**.
    ![Ny anslutning](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Längst upp i den högra rutan, klickar du på **Lägg till**.

4. Ange informationen som beskrivs i följande tabell och klicka på **OK** att skapa anslutningen.

> [!NOTE]

> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Ange ett namn för den Provance-instans som du vill ansluta med ITSMC.  Du använder det här namnet senare när du konfigurerar arbetsobjekt i den här ITSM eller visa detaljerad logganalys. |
| **Typ av**   | Välj **Provance**. |
| **Användarnamn**   | Ange det användarnamn som kan ansluta till ITSMC.    |
| **Lösenord**   | Ange lösenordet associerat med det här användarnamnet. **Obs:** användarnamn och lösenord som används för att generera autentiseringstoken endast och lagras inte någonstans i tjänsten ITSMC. _|
| **Server-URL**   | Ange Webbadressen till din Provance-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera den här anslutningen som du genererade i Provance-instans.  Mer information om klient-ID, se [hur du konfigurerar active directory-autentisering](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Omfång för synkronisering av data**   | Välj de Provance arbetsobjekt som du vill synkronisera till Azure logganalys via ITSMC.  Dessa objekt har importerats till logganalys fungerar.   **Alternativ:** incidenter, ändringsbegäranden.|
| **Synkronisera Data** | Ange antal föregående dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM produkten. När du väljer skapar ITSMC berörda konfigurationsobjekt som konfigurationsobjekt (vid CIs) i ITSM-system som stöds. **Som standard**: inaktiverad.|

![Provance anslutning](./media/log-analytics-itsmc/itsm-connections-provance-latest.png)

**När du har anslutits och synkroniserats**:

- Valda objekt från den här instansen Provance importeras till Azure **logganalys.** Du kan visa en sammanfattning av dessa arbetsobjekt på panelen IT Service Management-anslutningstjänsten.

- Du kan skapa incidenter från logganalys varningar eller loggposter eller Azure-aviseringar i den här Provance-instansen.

Läs mer: [skapa ITSM arbetsobjekt för Log Analytics aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [skapa ITSM arbetsobjekt från logganalys loggar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) och [skapa ITSM arbetsobjekt från Azure aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Ansluta Cherwell till IT-tjänst Management-anslutningstjänsten i Azure

Följande avsnitt innehåller information om hur du ansluter Cherwell produkten till ITSMC i Azure.

### <a name="prerequisites"></a>Förutsättningar

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [att lägga till IT-tjänsten hanteringslösning Connector](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Klient-ID har genererats. Mer information: [generera klient-ID för Cherwell](#generate-client-id-for-cherwell).
- Användarrollen: administratör.

### <a name="connection-procedure"></a>Proceduren för anslutning

Använd följande procedur för att skapa en Provance-anslutning:

1. I Azure-portalen går du till **alla resurser** och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Under **ARBETSYTAN DATAKÄLLOR** klickar du på **ITSM anslutningar**.
    ![Ny anslutning](./media/log-analytics-itsmc/add-new-itsm-connection.png)

3. Längst upp i den högra rutan, klickar du på **Lägg till**.

4. Ange informationen som beskrivs i följande tabell och klicka på **OK** att skapa anslutningen.

> [!NOTE]

> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Ange ett namn för den Cherwell-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbetsobjekt i den här ITSM eller visa detaljerad logganalys. |
| **Typ av**   | Välj **Cherwell.** |
| **Användarnamn**   | Skriv användarnamnet Cherwell som kan ansluta till ITSMC. |
| **Lösenord**   | Ange lösenordet associerat med det här användarnamnet. **Obs:** användarnamn och lösenord som används för att generera autentiseringstoken endast och lagras inte någonstans i tjänsten ITSMC.|
| **Server-URL**   | Ange Webbadressen till din Cherwell-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera den här anslutningen som du genererade i Cherwell-instans.   |
| **Omfång för synkronisering av data**   | Välj de Cherwell arbetsobjekt som du vill synkronisera via ITSMC.  Dessa objekt har importerats till logganalys fungerar.   **Alternativ:** incidenter, ändringsbegäranden. |
| **Synkronisera Data** | Ange antal föregående dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM produkten. När du väljer skapar ITSMC berörda konfigurationsobjekt som konfigurationsobjekt (vid CIs) i ITSM-system som stöds. **Som standard**: inaktiverad. |


![Provance anslutning](./media/log-analytics-itsmc/itsm-connections-cherwell-latest.png)

**När du har anslutits och synkroniserats**:

- Valda objekt från den här instansen Cherwell importeras till Azure **logganalys.** Du kan visa en sammanfattning av dessa arbetsobjekt på panelen IT Service Management-anslutningstjänsten.

- Du kan skapa incidenter från logganalys varningar eller loggposter eller Azure-aviseringar i den här Cherwell-instansen.

Läs mer: [skapa ITSM arbetsobjekt för Log Analytics aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts), [skapa ITSM arbetsobjekt från logganalys loggar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records) och [skapa ITSM arbetsobjekt från Azure aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generera klient-ID för Cherwell

Använd följande procedur för att generera klient-ID-nyckel för Cherwell:

1. Logga in på din Cherwell-instans som administratör.
2. Klicka på **säkerhet** > **redigera REST API-klientinställningar**.
3. Välj **Skapa ny klient** > **klienthemlighet**.

    ![Cherwell användar-id](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Nästa steg
 - [Skapa ITSM arbetsobjekt för logganalys-aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-alerts)
 - [Skapa ITSM arbetsobjekt från logganalys-loggen innehåller loggarna](log-analytics-itsmc-overview.md#create-itsm-work-items-from-log-analytics-log-records)
 - [Skapa ITSM arbetsobjekt från Azure-aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
