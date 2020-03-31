---
title: IT Service Management Connector i Azure Monitor
description: Den här artikeln innehåller information om hur du ansluter dina ITSM-produkter/tjänster till ITSMC (IT Service Management Connector) i Azure Monitor för att centralt övervaka och hantera ITSM-arbetsobjekten.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: eb3b09c6f349024d30d68a6c970770e2a78924ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132320"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Anslut ITSM-produkter/-tjänster med IT Service Management Connector
Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan DIN ITSM-produkt/tjänst och ITSMC (IT Service Management Connector) i Log Analytics för att centralt hantera dina arbetsobjekt. Mer information om ITSMC finns i [Översikt](../../azure-monitor/platform/itsmc-overview.md).

Följande ITSM-produkter/-tjänster stöds. Välj produkten om du vill visa detaljerad information om hur du ansluter produkten till ITSMC.

- [Servicehanteraren för SystemCenter](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance (bevis)](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> ITSM Connector kan bara ansluta till molnbaserade ServiceNow-instanser. Lokala ServiceNow-instanser stöds för närvarande inte.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Ansluta Servicehanteraren för System Center till IT Service Management Connector i Azure

I följande avsnitt finns information om hur du ansluter din System Center Service Manager-produkt till ITSMC i Azure.

### <a name="prerequisites"></a>Krav

Se till att följande förutsättningar är uppfyllda:

- ITSMC installerat. Mer information: [Lägga till lösning för IT Service Management Connector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Webbprogrammet Service Manager (webbapp) distribueras och konfigureras. Information om webbapp finns [här](#create-and-deploy-service-manager-web-app-service).
- Hybridanslutning skapad och konfigurerad. Mer information: [Konfigurera hybridanslutningen](#configure-the-hybrid-connection).
- Versioner av Service Manager som stöds: 2012 R2 eller 2016.
- Användarroll: [Avancerad operator](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Anslutningsprocedur

Använd följande procedur för att ansluta instansen systemcenterservicemanage till ITSMC:

1. Gå till Alla **resurser i** Azure-portalen och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Klicka på **ITSM-anslutningar**under RESURSER PÅ **ARBETSYTAN.**

    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till**högst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> 
> Alla dessa parametrar är obligatoriska.

| **Field** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Ange ett namn för den System Center Service Manager-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbetsobjekt i den här instansen/visar detaljerad logganalys. |
| **Typ av partner**   | Välj **Service manager för System Center**. |
| **Server URL**   | Skriv URL:en till webbappen Service Manager. Mer information om Service Manager Web App finns [här](#create-and-deploy-service-manager-web-app-service).
| **Klient-ID**   | Skriv det klient-ID som du har skapat (med det automatiska skriptet) för att autentisera webbappen. Mer information om det automatiserade skriptet finns [här.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Klienthemlighet**   | Skriv klienthemligheten som genereras för det här ID:t.   |
| **Synkronisera data**   | Välj de arbetsobjekt för ServiceHanteraren som du vill synkronisera via ITSMC.  Dessa arbetsobjekt importeras till Log Analytics. **Alternativ:**  Incidenter, ändringsbegäranden.|
| **Datasynkroniseringsscope** | Ange antalet tidigare dagar som du vill ha data från. **Maxgräns:** 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM-lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekten i ITSM-produkten. När du väljer det här alternativet skapar Log Analytics de berörda CIs-filerna som konfigurationsobjekt (vid icke-befintliga CIs) i itsm-systemet som stöds. **Standard**: inaktiverad. |

![Service manager-anslutning](media/itsmc-connections/service-manager-connection.png)

**När den har anslutits och synkroniserats:**

- Valda arbetsobjekt från Service Manager importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbetsobjekt på panelen IT Service Management Connector.

- Du kan skapa incidenter från Log Analytics-aviseringar eller från loggposter eller från Azure-aviseringar i den här Service Manager-instansen.


Läs mer: [Skapa ITSM-arbetsobjekt från Azure-aviseringar](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Skapa och distribuera Tjänsten Manager webbapptjänst

Microsoft har skapat en Service Manager-webbapp på GitHub för att ansluta den lokala Service Manager-tjänsten till ITSMC.

Så här konfigurerar du ITSM-webbappen för Service Manager:

- **Distribuera webbappen** – Distribuera webbappen, ange egenskaper och autentisera med Azure AD. Du kan distribuera webbappen med hjälp av det [automatiska skript](../../azure-monitor/platform/itsmc-service-manager-script.md) som Microsoft har gett dig.
- **Konfigurera hybridanslutningen** - [Konfigurera den här anslutningen](#configure-the-hybrid-connection)manuellt.

#### <a name="deploy-the-web-app"></a>Distribuera webbappen
Använd det automatiska [skriptet](../../azure-monitor/platform/itsmc-service-manager-script.md) för att distribuera webbappen, ange egenskaper och autentisera med Azure AD.

Kör skriptet genom att ange följande information:

- Information om Azure-prenumeration
- Namn på resursgrupp
- Location
- Serverinformation för Service Manager (servernamn, domän, användarnamn och lösenord)
- Prefix för webbplatsnamn för webbappen
- Namespace för ServiceBus.

Skriptet skapar webbappen med det namn som du angav (tillsammans med några ytterligare strängar för att göra den unik). Den genererar **webbapp-URL,** **klient-ID** och **klienthemlighet**.

Spara värdena, du använder dem när du skapar en anslutning till ITSMC.

**Kontrollera installationen av webbappen**

1. Gå till **Azure Portal** > **Resources**.
2. Markera webbappen, klicka på **Inställningar** > **Programinställningar**.
3. Bekräfta informationen om Service Manager-instansen som du angav när appen distribueras via skriptet.

### <a name="configure-the-hybrid-connection"></a>Konfigurera hybridanslutningen

Använd följande procedur för att konfigurera hybridanslutningen som ansluter Service Manager-instansen med ITSMC i Azure.

1. Hitta webbappen Service Manager under **Azure Resources**.
2. Klicka på **Inställningar** > **Nätverk**.
3. Klicka på Konfigurera **slutpunkter för hybridanslutning under Hybridanslutning.** **Hybrid Connections**

    ![Nätverk för hybridanslutning](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Klicka på Lägg till **hybridanslutning**i bladet **Hybridanslutningar** .

    ![Tillägg av hybridanslutning](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Klicka på Skapa ny hybridanslutning i bladet **Lägg till hybridanslutningar** . **Create new hybrid Connection**

    ![Ny hybridanslutning](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Skriv följande värden:

   - **Slutpunktsnamn:** Ange ett namn för den nya Hybrid-anslutningen.
   - **Slutpunktsvärd**: FQDN för servicehanterarens hanteringsserver.
   - **Slutpunktsport:** Typ 5724
   - **Servicebus-namnområde**: Använd ett befintligt servicebus-namnområde eller skapa ett nytt.
   - **Plats**: välj plats.
   - **Namn**: Ange ett namn på servicebussen om du skapar den.

     ![Värden för hybridanslutning](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Klicka på **OK** för att stänga **bladet Skapa hybridanslutning** och börja skapa hybridanslutningen.

    När Hybridanslutningen har skapats visas den under bladet.

7. När hybridanslutningen har skapats markerar du anslutningen och klickar på **Lägg till markerad hybridanslutning**.

    ![Ny hybridanslutning](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Konfigurera lyssnarinställningarna

Använd följande procedur för att konfigurera lyssnarinställningarna för hybridanslutningen.

1. I bladet **Hybridanslutningar** klickar du på **Hämta Anslutningshanteraren** och installerar den på datorn där System Center Service Manager-instansen körs.

    När installationen är klar är alternativet **Hybrid Connection Manager-gränssnitt** tillgängligt under **Start-menyn.**

2. Klicka på **Azure Connection Manager-användargränssnittet** , du kommer att bli tillfrågad om dina Azure-autentiseringsuppgifter.

3. Logga in med dina Azure-autentiseringsuppgifter och välj din prenumeration där Hybrid-anslutningen skapades.

4. Klicka på **Spara**.

Hybridanslutningen har anslutits.

![lyckad hybridanslutning](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> När hybridanslutningen har skapats kontrollerar och testar du anslutningen genom att besöka den distribuerade Service Manager-webbappen. Kontrollera att anslutningen lyckas innan du försöker ansluta till ITSMC i Azure.

Följande exempelbild visar information om en lyckad anslutning:

![Hybridanslutningstest](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Ansluta ServiceNow till IT Service Management Connector i Azure

I följande avsnitt finns information om hur du ansluter din ServiceNow-produkt till ITSMC i Azure.

### <a name="prerequisites"></a>Krav
Se till att följande förutsättningar är uppfyllda:
- ITSMC installerat. Mer information: [Lägga till lösning för IT Service Management Connector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ServiceNow stöds versioner: New York, Madrid, London, Kingston, Jakarta, Istanbul, Helsingfors, Genève.
> [!NOTE]
> ITSMC stöder endast det officiella SaaS-erbjudandet från Service Now. Privata distributioner av Tjänsten nu stöds inte. 

**ServiceNow-administratörer måste göra följande i sin ServiceNow-instans:**
- Generera klient-ID och klienthemlighet för ServiceNow-produkten. Information om hur du genererar klient-ID och hemlighet finns i följande information efter behov:

    - [Konfigurera OAuth för New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Ställ in OAuth för Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Ställ in OAuth för London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Ställ in OAuth för Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Ställ in OAuth för Helsingfors](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Ställ in OAuth för Genève](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Installera User App för Microsoft Log Analytics-integrering (ServiceNow-appen). [Läs mer](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Skapa användarroll för integrering för användarappen som är installerad. Information om hur du skapar rollen integrationsanvändare finns [här](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Anslutningsprocedur**
Använd följande procedur för att skapa en ServiceNow-anslutning:


1. Gå till Alla **resurser i** Azure-portalen och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Klicka på **ITSM-anslutningar**under RESURSER PÅ **ARBETSYTAN.**
    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till**högst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.


> [!NOTE]
> Alla dessa parametrar är obligatoriska.

| **Field** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Ange ett namn för Den ServiceNow-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare i Logganalys när du konfigurerar arbetsobjekt i den här ITSM/-vyn detaljerad logganalys. |
| **Typ av partner**   | Välj **ServiceNow**. |
| **Användarnamn**   | Skriv det integrationsanvändarnamn som du skapade i Appen ServiceNow för att stödja anslutningen till ITSMC. Mer information: [Skapa ServiceNow-appanvändarroll](#create-integration-user-role-in-servicenow-app).|
| **Lösenord**   | Skriv lösenordet som är associerat med det här användarnamnet. **Användarnamn**och lösenord används endast för att generera autentiseringstoken och lagras inte någonstans i ITSMC-tjänsten.  |
| **Server URL**   | Skriv URL:en för den ServiceNow-instans som du vill ansluta till ITSMC. URL:en bör peka på en SaaS-version som stöds med suffixet ".servicenow.com".|
| **Klient-ID**   | Skriv det klient-ID som du vill använda för OAuth2-autentisering, som du skapade tidigare.  Mer information om hur du genererar klient-ID och hemlighet: [OAuth Setup](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Klienthemlighet**   | Skriv klienthemligheten som genereras för det här ID:t.   |
| **Datasynkroniseringsscope**   | Välj de ServiceNow-arbetsobjekt som du vill synkronisera med Azure Log Analytics, via ITSMC.  De valda värdena importeras till logganalys.   **Alternativ:**  Incidenter och ändringsbegäranden.|
| **Synkronisera data** | Ange antalet tidigare dagar som du vill ha data från. **Maxgräns:** 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM-lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekten i ITSM-produkten. När det här alternativet är markerat skapar ITSMC de berörda CI:erna som konfigurationsobjekt (vid icke-befintliga CIs) i itsm-systemet som stöds. **Standard**: inaktiverad. |

![ServiceNow-anslutning](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**När den har anslutits och synkroniserats:**

- Valda arbetsobjekt från ServiceNow-instansen importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbetsobjekt på panelen IT Service Management Connector.

- Du kan skapa incidenter från Log Analytics-aviseringar eller från loggposter eller från Azure-aviseringar i den här ServiceNow-instansen.

Läs mer: [Skapa ITSM-arbetsobjekt från Azure-aviseringar](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Skapa användarroll för integrering i Appen ServiceNow

Användaren följande procedur:

1. Besök [ServiceNow-butiken](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) och installera **User App for ServiceNow och Microsoft OMS-integreringen** i din ServiceNow-instans.
   
   >[!NOTE]
   >Som en del av den pågående övergången från OmS (Microsoft Operations Management Suite) till Azure Monitor kallas OMS nu för Logganalys.     
2. Efter installationen besöker du det vänstra navigeringsfältet för TjänstenNow-instansen, söker och väljer Microsoft OMS-integratör.  
3. Klicka på **Checklista för installation**.

   Statusen visas som **Inte fullständig** om användarrollen ännu inte har skapats.

4. Ange användarnamnet för användaren som kan ansluta till ITSMC i Azure bredvid **Skapa integrationsanvändare.**
5. Ange lösenordet för den här användaren och klicka på **OK**.  

> [!NOTE]
> 
> Du använder dessa autentiseringsuppgifter för att göra ServiceNow-anslutningen i Azure.

Den nyskapade användaren visas med de tilldelade standardrollerna.

**Standardroller:**
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   Itil
-   template_editor
-   view_changer

När användaren har skapats flyttas statusen **checklistan checkning** till Slutförd och anger information om användarrollen som skapats för appen.

> [!NOTE]
> 
> ITSM Connector kan skicka incidenter till ServiceNow utan några andra moduler installerade på din ServiceNow-instans. Om du använder EventManagement-modulen i din ServiceNow-instans och vill skapa händelser eller aviseringar i ServiceNow med kopplingen lägger du till följande roller i integrationsanvändaren:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Ansluta Provance till IT Service Management Connector i Azure

I följande avsnitt finns information om hur du ansluter din Provance-produkt till ITSMC i Azure.


### <a name="prerequisites"></a>Krav

Se till att följande förutsättningar är uppfyllda:


- ITSMC installerat. Mer information: [Lägga till lösning för IT Service Management Connector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Provance-appen ska registreras med Azure AD - och klient-ID görs tillgängligt. Detaljerad information finns i [hur du konfigurerar active directory-autentisering](../../app-service/configure-authentication-provider-aad.md).

- Användarroll: Administratör.

### <a name="connection-procedure"></a>Anslutningsprocedur

Använd följande procedur för att skapa en Provance-anslutning:

1. Gå till Alla **resurser i** Azure-portalen och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Klicka på **ITSM-anslutningar**under RESURSER PÅ **ARBETSYTAN.**
    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till**högst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> 
> Alla dessa parametrar är obligatoriska.

| **Field** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Skriv ett namn på den Provance-förekomst som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbetsobjekt i den här ITSM/visa detaljerade logganalysen. |
| **Typ av partner**   | Välj **Provance**. |
| **Användarnamn**   | Skriv det användarnamn som kan ansluta till ITSMC.    |
| **Lösenord**   | Skriv lösenordet som är associerat med det här användarnamnet. **Anm.:** Användarnamn och lösenord används endast för att generera autentiseringstoken och lagras inte någonstans i ITSMC-tjänsten._|
| **Server URL**   | Skriv url:en för din Provance-förekomst som du vill ansluta till ITSMC. |
| **Klient-ID**   | Skriv klient-ID för att autentisera den här anslutningen, som du genererade i din Provance-förekomst.  Mer information om klient-ID, se [hur du konfigurerar active directory-autentisering](../../app-service/configure-authentication-provider-aad.md). |
| **Datasynkroniseringsscope**   | Välj de Provance-arbetsobjekt som du vill synkronisera med Azure Log Analytics via ITSMC.  Dessa arbetsobjekt importeras till logganalys.   **Alternativ:**   Incidenter, ändringsbegäranden.|
| **Synkronisera data** | Ange antalet tidigare dagar som du vill ha data från. **Maxgräns:** 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM-lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekten i ITSM-produkten. När det här alternativet är markerat skapar ITSMC de berörda CI:erna som konfigurationsobjekt (vid icke-befintliga CIs) i itsm-systemet som stöds. **Standard**: inaktiverad.|

![Provance-anslutning](media/itsmc-connections/itsm-connections-provance-latest.png)

**När den har anslutits och synkroniserats:**

- Valda arbetsobjekt från den här Provance-förekomsten importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbetsobjekt på panelen IT Service Management Connector.

- Du kan skapa incidenter från Log Analytics-aviseringar eller från loggposter eller från Azure-aviseringar i den här Provance-instansen.

Läs mer: [Skapa ITSM-arbetsobjekt från Azure-aviseringar](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Ansluta Cherwell till IT Service Management Connector i Azure

I följande avsnitt finns information om hur du ansluter din Cherwell-produkt till ITSMC i Azure.

### <a name="prerequisites"></a>Krav

Se till att följande förutsättningar är uppfyllda:

- ITSMC installerat. Mer information: [Lägga till lösning för IT Service Management Connector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Klient-ID har genererats. Mer information: [Generera klient-ID för Cherwell](#generate-client-id-for-cherwell).
- Användarroll: Administratör.

### <a name="connection-procedure"></a>Anslutningsprocedur

Använd följande procedur för att skapa en Provance-anslutning:

1. Gå till Alla **resurser i** Azure-portalen och leta efter **ServiceDesk(YourWorkspaceName)**

2.  Klicka på **ITSM-anslutningar**under RESURSER PÅ **ARBETSYTAN.**
    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till**högst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> 
> Alla dessa parametrar är obligatoriska.

| **Field** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Skriv ett namn på Cherwell-instansen som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbetsobjekt i den här ITSM/visa detaljerade logganalysen. |
| **Typ av partner**   | Välj **Cherwell.** |
| **Användarnamn**   | Skriv det Cherwell-användarnamn som kan ansluta till ITSMC. |
| **Lösenord**   | Skriv lösenordet som är associerat med det här användarnamnet. **Anm.:** Användarnamn och lösenord används endast för att generera autentiseringstoken och lagras inte någonstans i ITSMC-tjänsten.|
| **Server URL**   | Skriv url:en för din Cherwell-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Skriv klient-ID för att autentisera den här anslutningen, som du genererade i din Cherwell-instans.   |
| **Datasynkroniseringsscope**   | Välj de Cherwell-arbetsobjekt som du vill synkronisera via ITSMC.  Dessa arbetsobjekt importeras till logganalys.   **Alternativ:**  Incidenter, ändringsbegäranden. |
| **Synkronisera data** | Ange antalet tidigare dagar som du vill ha data från. **Maxgräns:** 120 dagar. |
| **Skapa nytt konfigurationsobjekt i ITSM-lösning** | Välj det här alternativet om du vill skapa konfigurationsobjekten i ITSM-produkten. När det här alternativet är markerat skapar ITSMC de berörda CI:erna som konfigurationsobjekt (vid icke-befintliga CIs) i itsm-systemet som stöds. **Standard**: inaktiverad. |


![Provance-anslutning](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**När den har anslutits och synkroniserats:**

- Valda arbetsobjekt från den här Cherwell-instansen importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbetsobjekt på panelen IT Service Management Connector.

- Du kan skapa incidenter från Log Analytics-aviseringar eller från loggposter eller från Azure-aviseringar i den här Cherwell-instansen.

Läs mer: [Skapa ITSM-arbetsobjekt från Azure-aviseringar](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generera klient-ID för Cherwell

Om du vill generera klient-ID/nyckeln för Cherwell använder du följande procedur:

1. Logga in på din Cherwell-instans som administratör.
2. Klicka på**Klientinställningar för REST-API för** **säkerhetsredigering** > .
3. Välj Skapa ny**klientklienthemlighet** **Create new client** > .

    ![Id för Cherwell-användare](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Nästa steg
 - [Skapa ITSM-arbetsobjekt från Azure-aviseringar](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
