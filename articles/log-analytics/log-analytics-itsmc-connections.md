---
title: Stöd för anslutningar med IT Service Management Connector i Azure Log Analytics | Microsoft Docs
description: Den här artikeln innehåller information om hur du ansluter din ITSM-produkter/tjänster med IT Service Management Connector (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM-arbetsuppgifter.
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.component: ''
ms.openlocfilehash: 4faea38817a546dbab02064d22a0813362f77b3d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416468"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Träffa IT Service Management Connector ITSM-produkter/tjänster
Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan din ITSM-produkt/tjänst och IT Service Management Connector (ITSMC) i Log Analytics för att centralt hantera dina arbetsuppgifter. Läs mer om ITSMC [översikt](log-analytics-itsmc-overview.md).

Följande ITSM-produkter/tjänster stöds. Välj produkten som ska visa detaljerad information om hur du ansluter produkten till ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]

> ITSM-anslutningsprogrammet kan bara ansluta till molnbaserade ServiceNow-instanser. Lokala ServiceNow instanser stöds inte för närvarande.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Ansluta System Center Service Manager till IT Service Management Connector i Azure

Följande avsnitt innehåller information om hur du ansluter din produkt i System Center Service Manager till ITSMC i Azure.

### <a name="prerequisites"></a>Förutsättningar

Kontrollera att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [att lägga till IT Service Management Connector-lösningen](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Service Manager-webbprogram (webbapp) distribuerat och konfigurerat. Information om webbapp är [här](#create-and-deploy-service-manager-web-app-service).
- Hybridanslutning skapas och konfigureras. Mer information: [konfigurera hybrid anslutning](#configure-the-hybrid-connection).
- Versioner av Service Manager som stöds: 2012 R2 eller 2016.
- Användarroll: [avancerad operatör](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Proceduren för anslutning

Använd följande procedur för att ansluta din System Center Service Manager-instans till ITSMC:

1. I Azure-portalen går du till **alla resurser** och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Under **DATAKÄLLOR för ARBETSYTA** klickar du på **ITSM-anslutningar**.

    ![Ny anslutning](media/log-analytics-itsmc-connections/add-new-itsm-connection.png)

3. Längst upp i den högra rutan, klickar du på **Lägg till**.

4. Ange informationen som beskrivs i följande tabell och klicka på **OK** att skapa anslutningen.

> [!NOTE]

> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Skriv ett namn för den System Center Service Manager-instans som du vill ansluta med ITSMC.  Du använder det här namnet senare när du konfigurerar arbetsobjekt i den här instansen / visa detaljerad logganalys. |
| **Partnertyp**   | Välj **System Center Service Manager**. |
| **Serveradress**   | Ange Webbadressen till Service Manager-webbapp. Mer information om Service Manager Web app är [här](#create-and-deploy-service-manager-web-app-service).
| **Klient-ID**   | Ange klient-ID som du skapade (med hjälp av automatisk skriptet) för att autentisera webbappen. Mer information om automatiserat skript är [här.](log-analytics-itsmc-service-manager-script.md)|
| **Klienthemlighet**   | Ange klienthemlighet genereras för detta ID.   |
| **Datasynkroniseringsomfång**   | Välj Service Manager-arbetsuppgifter som du vill synkronisera via ITSMC.  Dessa fungerar objekt har importerats till Log Analytics. **Alternativ:** incidenter, ändringsbegäranden.|
| **Synkronisera Data** | Ange antalet tidigare dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM-lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM-produkten. När du väljer skapar Log Analytics berörda konfigurationsobjekt som konfigurationsobjekt (när det gäller icke-existerande CIs) i ITSM-system som stöds. **Standard**: inaktiverad. |

![Service manager-anslutningen](media/log-analytics-itsmc-connections/service-manager-connection.png)

**När du har anslutits och synkroniserats**:

- Valda arbetsobjekt från Service Manager importeras till Azure **Log Analytics.** Du kan visa en sammanfattning av dessa arbetsuppgifter på panelen IT Service Management Connector.

- Du kan skapa incidenter från Log Analytics-aviseringar eller från loggposter eller från Azure-aviseringar i den här instansen för Service Manager.


Läs mer: [skapa ITSM-arbetsuppgifter från Azure-aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Skapa och distribuera Service Manager web app service

Om du vill ansluta den lokala Service Manager ITSMC i Azure, har Microsoft skapat en Service Manager Web app på GitHub.

Om du vill konfigurera ITSM-webbapp för Service Manager, gör du följande:

- **Distribuera webbappen** – distribuera webbappen, ställer du in egenskaperna och autentisera med Azure AD. Du kan distribuera webbappen med hjälp av den [automatiserade skript](log-analytics-itsmc-service-manager-script.md) att Microsoft tillhandahåller.
- **Konfigurera hybridanslutningen** - [konfigurera den här anslutningen](#configure-the-hybrid-connection), manuellt.

#### <a name="deploy-the-web-app"></a>Distribuera webbappen
Använd den automatiserade [skriptet](log-analytics-itsmc-service-manager-script.md) ställer du in egenskaperna för att distribuera webbappen, och autentisera med Azure AD.

Kör skriptet genom att tillhandahålla följande nödvändiga data:

- Prenumerationsinformation för Azure
- Resursgruppsnamn
- Plats
- Service Manager server-information (namn, domän, användarnamn och lösenord)
- Plats-namnprefixet för din webbapp
- ServiceBus Namespace.

Skriptet skapar webbappen med namnet som du har angett (tillsammans med några ytterligare strängar som gör det unikt). Den genererar den **Webbappens URL**, **klient-ID** och **klienthemlighet**.

Spara värden använda du dem när du skapar en anslutning med ITSMC.

**Kontrollera installationen av Web-appen**

1. Gå till **Azure-portalen** > **resurser**.
2. Välj webbappen, klicka på **inställningar** > **programinställningar**.
3. Bekräfta informationen om Service Manager-instans som du angav vid tidpunkten för distribution av appen via skriptet.

### <a name="configure-the-hybrid-connection"></a>Konfigurera hybridanslutningen

Du kan använda följande procedur för att konfigurera hybridanslutningen som sammanför Service Manager-instans med ITSMC i Azure.

1. Hitta Service Manager Web-app under **Azure-resurser**.
2. Klicka på **inställningar** > **nätverk**.
3. Under **Hybridanslutningar**, klickar du på **konfigurera slutpunkter för din hybridanslutning**.

    ![Hybrid anslutning nätverk](media/log-analytics-itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. I den **Hybridanslutningar** bladet klickar du på **Lägg till hybridanslutning**.

    ![Lägg till hybridanslutning](media/log-analytics-itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. I den **Lägg till Hybrid Connections** bladet klickar du på **skapa nya hybrid anslutning**.

    ![Ny hybridanslutning](media/log-analytics-itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Ange följande värden:

    - **Namnet på slutpunkten**: Ange ett namn för den nya Hybrid-anslutningen.
    -  **Slutpunktsvärd**: FQDN för Service Manager-hanteringsservern.
    - **Slutpunktsport**: Skriv 5724
    - **Servicebus-namnområdet**: Använd en befintlig servicebus namespace eller skapa en ny.
    - **Plats**: Välj platsen.
    -  **Namn på**: Ange ett namn som servicebus om du skapar den.

    ![Hybrid anslutningsvärden](media/log-analytics-itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Klicka på **OK** att Stäng den **skapa hybridanslutning** bladet och börja skapa hybridanslutningen.

    När hybridanslutningen har skapats visas den under bladet.

7. När du har skapat hybridanslutningen väljer anslutningen och klickar på **Lägg till markerade hybridanslutning**.

    ![Ny hybridanslutning](media/log-analytics-itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Konfigurera listener-konfiguration

Använd följande procedur för att konfigurera listener-konfiguration för hybridanslutningen.

1. I den **Hybridanslutningar** bladet klickar du på **ladda ned Anslutningshanteraren** och installera den på den dator där System Center Service Manager-instansen körs.

    När installationen är klar, **Hybridanslutningshanteraren UI** alternativet är tillgängligt under **starta** menyn.

2. Klicka på **Hybridanslutningshanteraren UI** , uppmanas du att dina Azure autentiseringsuppgifter.

3. Logga in med dina autentiseringsuppgifter för Azure och välj din prenumeration där hybridanslutningen har skapats.

4. Klicka på **Spara**.

Din hybridanslutning har anslutits.

![lyckad anslutning](media/log-analytics-itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> Efter hybriden anslutningen har skapats, kontrollera och testa anslutningen genom att gå till den distribuerade webbappen för Service Manager. Se till att anslutningen är klar innan du försöker ansluta till ITSMC i Azure.

Följande exempelbild visar information om en lyckad anslutning:

![Anslutningstestet hybridmoln](media/log-analytics-itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Anslut ServiceNow till IT Service Management Connector i Azure

Följande avsnitt innehåller information om hur du ansluter din ServiceNow-produkt till ITSMC i Azure.

### <a name="prerequisites"></a>Förutsättningar
Kontrollera att följande krav är uppfyllda:
- ITSMC installerad. Mer information: [att lägga till IT Service Management Connector-lösningen](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ServiceNow-versioner som stöds: Kingston, Jakarta, Istanbul, Helsingfors, Geneva.

**ServiceNow Admins måste göra följande i sina ServiceNow-instans**:
- Generera klient-ID och klienthemlighet för ServiceNow-produkten. Information om hur du skapar klient-ID och hemlighet finns i följande information som krävs:

    - [Konfigurera OAuth för Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Helsingfors](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Installera appen användare för Microsoft Log Analytics-integrering (ServiceNow-app). [Läs mer](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Skapa integration användarroll för användaren appen är installerad. Information om hur du skapar rollen integration är [här](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Proceduren för anslutning**
Använd följande procedur för att skapa en ServiceNow-anslutning:


1. I Azure-portalen går du till **alla resurser** och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Under **DATAKÄLLOR för ARBETSYTA** klickar du på **ITSM-anslutningar**.
    ![Ny anslutning](media/log-analytics-itsmc-connections/add-new-itsm-connection.png)

3. Längst upp i den högra rutan, klickar du på **Lägg till**.

4. Ange informationen som beskrivs i följande tabell och klicka på **OK** att skapa anslutningen.


> [!NOTE]
> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Skriv ett namn för den ServiceNow-instans som du vill ansluta med ITSMC.  Du använder det här namnet senare i Log Analytics när du konfigurerar arbetsobjekt i den här ITSM / visa detaljerad logganalys. |
| **Partnertyp**   | Välj **ServiceNow**. |
| **Användarnamn**   | Typ integration användarnamnet som du skapade i ServiceNow-app för anslutningen till ITSMC. Mer information: [skapa ServiceNow app användarrollen](#create-integration-user-role-in-servicenow-app).|
| **Lösenord**   | Ange lösenordet som är associerade med det här användarnamnet. **Obs**: användarnamn och lösenord används för att generera autentiseringstoken endast och lagras inte någonstans i ITSMC-tjänsten.  |
| **Serveradress**   | Ange Webbadressen till den ServiceNow-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID som du vill använda för OAuth2-autentisering, som du skapade tidigare.  Mer information om att generera klient-ID och hemlighet: [OAuth-installationen](http://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Klienthemlighet**   | Ange klienthemlighet genereras för detta ID.   |
| **Datasynkroniseringsomfång**   | Välj ServiceNow-arbetsobjekten som du vill synkronisera till Azure Log Analytics, via ITSMC.  De valda värdena importeras till log analytics.   **Alternativ:** incidenter och ändringsförfrågningar.|
| **Synkronisera Data** | Ange antalet tidigare dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM-lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM-produkten. När du väljer skapar ITSMC berörda konfigurationsobjekt som konfigurationsobjekt (när det gäller icke-existerande CIs) i ITSM-system som stöds. **Standard**: inaktiverad. |

![ServiceNow-anslutning](media/log-analytics-itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**När du har anslutits och synkroniserats**:

- Valda objekt från ServiceNow-instans har importerats till Azure **Log Analytics.** Du kan visa en sammanfattning av dessa arbetsuppgifter på panelen IT Service Management Connector.

- Du kan skapa incidenter från Log Analytics-aviseringar eller från loggposter eller från Azure-aviseringar i den här instansen av ServiceNow.

Läs mer: [skapa ITSM-arbetsuppgifter från Azure-aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Skapa integration i ServiceNow-app

Användare nedan:

1.  Gå till den [ServiceNow store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) och installera den **användaren appen för ServiceNow och Microsoft OMS-integrering** i din ServiceNow-instans.
   
   >[!NOTE]
   >Som en del av pågående övergången från Microsoft Operations Management Suite (OMS) till Azure Monitor kallas OMS nu till Log Analytics.     
2.  Gå till det vänstra navigeringsfältet ServiceNow-instans, Sök och välj Microsoft OMS integrator efter installationen.  
3.  Klicka på **checklista för installationen**.

    Status visas som **inte att slutföra** om rollen ännu som ska skapas.

4.  I textrutorna bredvid **skapa integreringsanvändaren**, ange användarnamn för den användare som kan ansluta till ITSMC i Azure.
5.  Ange lösenordet för den här användaren klicka sedan på **OK**.  

>[!NOTE]

> Du kan använda dessa autentiseringsuppgifter för att göra ServiceNow-anslutning i Azure.

Den nyligen skapade användaren visas med standardroller tilldelad.

**Standard roller**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   ITIL
-   template_editor
-   view_changer

När användaren har skapats, status för **Kontrollera installationschecklista** flyttar slutförd, visa en lista över information om rollen skapats för appen.

> [!NOTE]

> ITSM-anslutningsprogrammet kan skicka incidenter till ServiceNow utan andra moduler som installerats på din ServiceNow-instans. Om du använder EventManagement modul i din ServiceNow-instans och vill skapa händelser eller aviseringar i ServiceNow med kopplingen kan du lägga till följande roller för integrering av användaren:

>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Ansluta Provance till IT Service Management Connector i Azure

Följande avsnitt innehåller information om hur du ansluter din Provance produkt till ITSMC i Azure.


### <a name="prerequisites"></a>Förutsättningar

Kontrollera att följande krav är uppfyllda:


- ITSMC installerad. Mer information: [att lägga till IT Service Management Connector-lösningen](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Provance App ska registreras med Azure AD - och klient-ID är tillgänglig. Detaljerad information finns i [hur du konfigurerar active directory-autentisering](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Användarroll: administratör.

### <a name="connection-procedure"></a>Proceduren för anslutning

Använd följande procedur för att skapa en Provance-anslutning:

1. I Azure-portalen går du till **alla resurser** och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Under **DATAKÄLLOR för ARBETSYTA** klickar du på **ITSM-anslutningar**.
    ![Ny anslutning](media/log-analytics-itsmc-connections/add-new-itsm-connection.png)

3. Längst upp i den högra rutan, klickar du på **Lägg till**.

4. Ange informationen som beskrivs i följande tabell och klicka på **OK** att skapa anslutningen.

> [!NOTE]

> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Skriv ett namn för den Provance-instans som du vill ansluta med ITSMC.  Du använder det här namnet senare när du konfigurerar arbetsobjekt i den här ITSM / visa detaljerad logganalys. |
| **Partnertyp**   | Välj **Provance**. |
| **Användarnamn**   | Ange det användarnamn som kan ansluta till ITSMC.    |
| **Lösenord**   | Ange lösenordet som är associerade med det här användarnamnet. **Obs:** användarnamn och lösenord används för att generera autentiseringstoken endast och lagras inte någonstans i tjänsten ITSMC. _|
| **Serveradress**   | Ange Webbadressen till din Provance-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera den här anslutningen som du genererade i din Provance-instans.  Mer information om klient-ID finns i [hur du konfigurerar active directory-autentisering](../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Datasynkroniseringsomfång**   | Välj Provance arbetsobjekten som du vill synkronisera till Azure Log Analytics, via ITSMC.  Dessa objekt har importerats till logganalys fungerar.   **Alternativ:** incidenter, ändringsbegäranden.|
| **Synkronisera Data** | Ange antalet tidigare dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM-lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM-produkten. När du väljer skapar ITSMC berörda konfigurationsobjekt som konfigurationsobjekt (när det gäller icke-existerande CIs) i ITSM-system som stöds. **Standard**: inaktiverad.|

![Provance anslutning](media/log-analytics-itsmc-connections/itsm-connections-provance-latest.png)

**När du har anslutits och synkroniserats**:

- Valda objekt från den här instansen Provance importeras till Azure **Log Analytics.** Du kan visa en sammanfattning av dessa arbetsuppgifter på panelen IT Service Management Connector.

- Du kan skapa incidenter från Log Analytics-aviseringar eller från loggposter eller från Azure-aviseringar i den här Provance-instansen.

Läs mer: [skapa ITSM-arbetsuppgifter från Azure-aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Ansluta Cherwell till IT Service Management Connector i Azure

Följande avsnitt innehåller information om hur du ansluter din Cherwell produkt till ITSMC i Azure.

### <a name="prerequisites"></a>Förutsättningar

Kontrollera att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [att lägga till IT Service Management Connector-lösningen](log-analytics-itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Klient-ID som genereras. Mer information: [generera klient-ID för Cherwell](#generate-client-id-for-cherwell).
- Användarroll: administratör.

### <a name="connection-procedure"></a>Proceduren för anslutning

Använd följande procedur för att skapa en Provance-anslutning:

1. I Azure-portalen går du till **alla resurser** och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Under **DATAKÄLLOR för ARBETSYTA** klickar du på **ITSM-anslutningar**.
    ![Ny anslutning](media/log-analytics-itsmc-connections/add-new-itsm-connection.png)

3. Längst upp i den högra rutan, klickar du på **Lägg till**.

4. Ange informationen som beskrivs i följande tabell och klicka på **OK** att skapa anslutningen.

> [!NOTE]

> Dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Skriv ett namn för den Cherwell-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbetsobjekt i den här ITSM / visa detaljerad logganalys. |
| **Partnertyp**   | Välj **Cherwell.** |
| **Användarnamn**   | Skriv användarnamnet Cherwell som kan ansluta till ITSMC. |
| **Lösenord**   | Ange lösenordet som är associerade med det här användarnamnet. **Obs:** användarnamn och lösenord används för att generera autentiseringstoken endast och lagras inte någonstans i ITSMC-tjänsten.|
| **Serveradress**   | Ange Webbadressen till din Cherwell-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera den här anslutningen som du genererade i din instans av Cherwell.   |
| **Datasynkroniseringsomfång**   | Välj Cherwell arbetsobjekten som du vill synkronisera via ITSMC.  Dessa objekt har importerats till logganalys fungerar.   **Alternativ:** incidenter, ändringsbegäranden. |
| **Synkronisera Data** | Ange antalet tidigare dagar som du vill att data från. **Maxgränsen**: 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM-lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekt i ITSM-produkten. När du väljer skapar ITSMC berörda konfigurationsobjekt som konfigurationsobjekt (när det gäller icke-existerande CIs) i ITSM-system som stöds. **Standard**: inaktiverad. |


![Provance anslutning](media/log-analytics-itsmc-connections/itsm-connections-cherwell-latest.png)

**När du har anslutits och synkroniserats**:

- Valda objekt från den här instansen Cherwell importeras till Azure **Log Analytics.** Du kan visa en sammanfattning av dessa arbetsuppgifter på panelen IT Service Management Connector.

- Du kan skapa incidenter från Log Analytics-aviseringar eller från loggposter eller från Azure-aviseringar i den här Cherwell-instansen.

Läs mer: [skapa ITSM-arbetsuppgifter från Azure-aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generera klient-ID för Cherwell

Använd följande procedur för att generera klientens ID-nyckel för Cherwell:

1. Logga in på din Cherwell-instans som administratör.
2. Klicka på **Security** > **redigera REST API-klientinställningar**.
3. Välj **Skapa ny klient** > **klienthemlighet**.

    ![Cherwell användar-id](media/log-analytics-itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Nästa steg
 - [Skapa ITSM-arbetsuppgifter från Azure-aviseringar](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
