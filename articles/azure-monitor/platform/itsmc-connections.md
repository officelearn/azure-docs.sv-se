---
title: Anslutningar som stöds med Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure Log Analytics | Microsoft Docs
description: Den här artikeln innehåller information om hur du ansluter dina ITSM-produkter/-tjänster med Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM arbets objekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: c6cad29b6cc392746a2e56323302521302835b2f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394951"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Anslut ITSM produkter/tjänster med Anslutningsprogram för hantering av IT-tjänster (ITSM)
Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan din ITSM-produkt/-tjänst och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter. Mer information om ITSMC finns i [Översikt](../../azure-monitor/platform/itsmc-overview.md).

Följande ITSM-produkter/-tjänster stöds. Välj produkten om du vill visa detaljerad information om hur du ansluter produkten till ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Styrka](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> ITSM-anslutningsprogram kan bara ansluta till molnbaserade ServiceNow-instanser. Lokala ServiceNow-instanser stöds inte för närvarande.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Ansluta System Center Service Manager till Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure

I följande avsnitt finns information om hur du ansluter System Center Service Manager-produkten till ITSMC i Azure.

### <a name="prerequisites"></a>Förutsättningar

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Service Manager webb program (webbapp) distribueras och konfigureras. Information om Web App finns [här](#create-and-deploy-service-manager-web-app-service).
- Hybrid anslutning har skapats och kon figurer ATS. Mer information: [Konfigurera hybrid anslutningen](#configure-the-hybrid-connection).
- Versioner av Service Manager som stöds: 2012 R2 eller 2016.
- Användar roll: [Avancerad operatör](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Anslutnings procedur

Använd följande procedur för att ansluta din System Center Service Manager-instans till ITSMC:

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2.  Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.

    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till**längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> 
> Alla dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Ange ett namn för den System Center Service Manager-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbets objekt i den här instansen/Visa detaljerad Log Analytics. |
| **Partner typ**   | Välj **System Center Service Manager**. |
| **Server-URL**   | Ange webb adressen till Service Manager webbappen. Mer information om Service Manager Web App finns [här](#create-and-deploy-service-manager-web-app-service).
| **Klient-ID**   | Ange det klient-ID som du genererade (med hjälp av det automatiska skriptet) för autentisering av webbappen. Mer information om det automatiserade skriptet finns [här.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Klienthemlighet**   | Ange klient hemligheten som genererats för detta ID.   |
| **Synkronisera data**   | Välj de Service Manager arbets objekt som du vill synkronisera genom ITSMC.  Dessa arbets uppgifter importeras till Log Analytics. **Alternativ:**  Incidenter, ändrings begär Anden.|
| **Omfång för data synkronisering** | Ange antalet senaste dagar som du vill att data ska visas. **Maxgräns**: 120 dagar. |
| **Skapa nytt konfigurations objekt i ITSM-lösningen** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När det här alternativet är markerat skapar Log Analytics det påverkade CIs-objektet som konfigurations objekt (om det inte finns något befintligt CIs) i det ITSM system som stöds. **Standard**: inaktiverat. |

![Service Manager-anslutning](media/itsmc-connections/service-manager-connection.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från Service Manager importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här Service Manager-instansen.


Läs mer: [skapa ITSM arbets objekt från Azure-aviseringar](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Skapa och distribuera Service Manager Web App Service

För att ansluta den lokala Service Manager med ITSMC i Azure har Microsoft skapat en Service Manager webbapp på GitHub.

Gör så här för att konfigurera ITSM-webbappen för din Service Manager:

- **Distribuera webbappen** – distribuera webbappen, ange egenskaperna och autentisera med Azure AD. Du kan distribuera webbappen med hjälp av det [automatiserade skriptet](../../azure-monitor/platform/itsmc-service-manager-script.md) som Microsoft tillhandahåller.
- **Konfigurera hybrid anslutningen** - [Konfigurera den här anslutningen](#configure-the-hybrid-connection)manuellt.

#### <a name="deploy-the-web-app"></a>Distribuera webbappen
Använd det automatiserade [skriptet](../../azure-monitor/platform/itsmc-service-manager-script.md) för att distribuera webbappen, ange egenskaperna och autentisera med Azure AD.

Kör skriptet genom att ange följande obligatoriska information:

- Information om Azure-prenumeration
- Namn på resursgrupp
- plats.
- Service Manager Server information (Server namn, domän, användar namn och lösen ord)
- Prefix för webbplats namn för din webbapp
- Service Bus-namnrymd.

Skriptet skapar webbappen med det namn som du har angett (tillsammans med några ytterligare strängar för att göra den unik). Den genererar **webbappens webb adress**, **klient-ID** och **klient hemlighet**.

Spara värdena, du använder dem när du skapar en anslutning med ITSMC.

**Kontrol lera installationen av webbapp**

1. Gå till **Azure Portal** > **resurser**.
2. Välj webbapp, klicka på **inställningar** > **program inställningar**.
3. Bekräfta informationen om Service Manager-instansen som du angav när du distribuerade appen via skriptet.

### <a name="configure-the-hybrid-connection"></a>Konfigurera hybrid anslutningen

Använd följande procedur för att konfigurera hybrid anslutningen som ansluter Service Manager-instansen med ITSMC i Azure.

1. Hitta Service Manager-webbappen under **Azure-resurser**.
2. Klicka på **inställningar** > **nätverk**.
3. Under **hybridanslutningar**klickar **du på Konfigurera hybrid anslutnings slut punkter**.

    ![Anslutning till hybrid anslutning](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Klicka på **Lägg till hybrid anslutning**på bladet **hybridanslutningar** .

    ![Hybrid anslutning, Lägg till](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Klicka på **Skapa ny hybrid anslutning**i bladet **Lägg till hybridanslutningar** .

    ![Ny hybrid anslutning](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Ange följande värden:

   - **Slut punkts namn**: Ange ett namn för den nya hybrid anslutningen.
   - **Slut punkts värd**: FQDN för service Managers hanterings servern.
   - **Slut punkts port**: typ 5724
   - **Service Bus-namnrymd**: Använd ett befintligt Service Bus-namnområde eller skapa ett nytt.
   - **Plats**: Välj platsen.
   - **Namn**: Ange ett namn på Service Bus om du skapar det.

     ![Hybrid anslutnings värden](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Klicka på **OK** för att stänga bladet **skapa hybrid anslutning** och börja skapa hybrid anslutningen.

    När hybrid anslutningen har skapats visas den under bladet.

7. När hybrid anslutningen har skapats väljer du anslutningen och klickar på **Lägg till vald hybrid anslutning**.

    ![Ny hybrid anslutning](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Konfigurera Listener-installationen

Använd följande procedur för att konfigurera Listener-installationen för Hybrid anslutningen.

1. I bladet **hybridanslutningar** klickar **du på Hämta anslutnings hanteraren** och installerar den på den dator där System Center Service Manager instansen körs.

    När installationen är klar finns **HYBRIDANSLUTNINGSHANTERAREN UI** -alternativet på **Start** menyn.

2. Klicka på **Hybridanslutningshanteraren användar gränssnitt** , så uppmanas du att ange dina autentiseringsuppgifter för Azure.

3. Logga in med dina Azure-autentiseringsuppgifter och välj din prenumeration där hybrid anslutningen skapades.

4. Klicka på **Save** (Spara).

Din hybrid anslutning har upprättats.

![lyckad hybrid anslutning](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> När hybrid anslutningen har skapats kontrollerar du och testar anslutningen genom att gå till den distribuerade Service Manager-webbappen. Se till att anslutningen lyckas innan du försöker ansluta till ITSMC i Azure.

Följande exempel bild visar information om en lyckad anslutning:

![Test av hybrid anslutning](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Ansluta ServiceNow till Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure

Följande avsnitt innehåller information om hur du ansluter din ServiceNow-produkt till ITSMC i Azure.

### <a name="prerequisites"></a>Förutsättningar
Se till att följande krav är uppfyllda:
- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ServiceNow-versioner som stöds: New York, Madrid, London, Kingston, Jakarta, Istanbul, Helsingfors, Genève.

**ServiceNow-administratörer måste göra följande i sin ServiceNow-instans**:
- Generera klient-ID och klient hemlighet för ServiceNow-produkten. Information om hur du genererar klient-ID och hemlighet finns i följande information som krävs:

    - [Konfigurera OAuth för New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Helsingfors](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurera OAuth för Genève](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Installera User-appen för Microsoft Log Analytics integration (ServiceNow-app). [Läs mer](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Skapa en integrerings användar roll för användar programmet som är installerat. Information om hur du skapar integrations användar rollen finns [här](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Anslutnings procedur**
Använd följande procedur för att skapa en ServiceNow-anslutning:


1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2.  Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.
    ![ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till**längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.


> [!NOTE]
> Alla dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Ange ett namn för den ServiceNow-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare i Log Analytics när du konfigurerar arbets objekt i den här ITSM/Visa detaljerad logg analys. |
| **Partner typ**   | Välj **ServiceNow**. |
| **Användarnamn**   | Skriv det integrations användar namn som du skapade i ServiceNow-appen för att stödja anslutningen till ITSMC. Mer information: [skapa ServiceNow app User Role](#create-integration-user-role-in-servicenow-app).|
| **Lösenord**   | Ange lösen ordet som är kopplat till det här användar namnet. **Obs!** användar namn och lösen ord används enbart för att skapa autentiseringstoken och lagras inte var som helst i ITSMC-tjänsten.  |
| **Server-URL**   | Ange URL: en för den ServiceNow-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange det klient-ID som du vill använda för OAuth2-autentisering, som du skapade tidigare.  Mer information om hur du genererar klient-ID och hemlighet: [OAuth-installation](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Klienthemlighet**   | Ange klient hemligheten som genererats för detta ID.   |
| **Omfång för data synkronisering**   | Välj de ServiceNow arbets objekt som du vill synkronisera till Azure Log Analytics via ITSMC.  De valda värdena importeras till Log Analytics.   **Alternativ:**  Incidenter och ändrings begär Anden.|
| **Synkronisera data** | Ange antalet senaste dagar som du vill att data ska visas. **Maxgräns**: 120 dagar. |
| **Skapa nytt konfigurations objekt i ITSM-lösningen** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När det här alternativet är markerat skapar ITSMC det påverkade CIs-objektet som konfigurations objekt (om det inte finns något befintligt CIs) i det ITSM system som stöds. **Standard**: inaktiverat. |

![ServiceNow-anslutning](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från ServiceNow-instansen importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här ServiceNow-instansen.

Läs mer: [skapa ITSM arbets objekt från Azure-aviseringar](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Skapa en användar roll för integrering i ServiceNow-appen

Användaren följer följande procedur:

1. Besök [ServiceNow Store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) och installera User- **appen för SERVICENOW och Microsoft OMS-integration** till ServiceNow-instansen.
   
   >[!NOTE]
   >Som en del av den pågående över gången från Microsoft Operations Management Suite (OMS) till Azure Monitor, kallas OMS nu för Log Analytics.     
2. Efter installationen går du till det vänstra navigerings fältet för ServiceNow-instansen, söker och väljer Microsoft OMS Integrator.  
3. Klicka på **installations check lista**.

   Statusen visas som **inte slutförd** om användar rollen ännu inte har skapats.

4. I text rutorna bredvid **skapa integrations användare**anger du användar namnet för den användare som kan ansluta till ITSMC i Azure.
5. Ange lösen ordet för den här användaren och klicka på **OK**.  

> [!NOTE]
> 
> Du använder dessa autentiseringsuppgifter för att skapa ServiceNow-anslutningen i Azure.

Den nyskapade användaren visas med de standard roller som tilldelats.

**Standard roller**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   ITIL
-   template_editor
-   view_changer

När användaren har skapats flyttas statusen för **kontrol lera installations check listan** till slutförd, som visar information om den användar roll som har skapats för appen.

> [!NOTE]
> 
> ITSM-anslutningsprogram kan skicka incidenter till ServiceNow utan att några andra moduler är installerade på din ServiceNow-instans. Om du använder EventManagement-modulen i din ServiceNow-instans och vill skapa händelser eller varningar i ServiceNow med hjälp av anslutnings tjänsten lägger du till följande roller i integrations användaren:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Ansluta bevisa till Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure

Följande avsnitt innehåller information om hur du ansluter din bestyrkande produkt till ITSMC i Azure.


### <a name="prerequisites"></a>Förutsättningar

Se till att följande krav är uppfyllda:


- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Upphandlings appen bör registreras med Azure AD-och klient-ID är tillgängligt. Detaljerad information finns i [så här konfigurerar du Active Directory-autentisering](../../app-service/configure-authentication-provider-aad.md).

- Användar roll: administratör.

### <a name="connection-procedure"></a>Anslutnings procedur

Använd följande procedur för att skapa en upphandlings anslutning:

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2.  Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.
    ![ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till**längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> 
> Alla dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Ange ett namn på den instans av upphandlingen som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbets objekt i den här ITSM/Visa detaljerad Log Analytics. |
| **Partner typ**   | Välj **styrka**. |
| **Användarnamn**   | Ange användar namnet som kan ansluta till ITSMC.    |
| **Lösenord**   | Ange lösen ordet som är kopplat till det här användar namnet. **Obs:** Användar namn och lösen ord används endast för att skapa autentiseringstoken och lagras inte var som helst i ITSMC-tjänsten. _|
| **Server-URL**   | Ange URL: en för din instans av instansen som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera anslutningen, som du skapade i din upphandlings instans.  Mer information om klient-ID finns i [Konfigurera Active Directory-autentisering](../../app-service/configure-authentication-provider-aad.md). |
| **Omfång för data synkronisering**   | Välj de arbets objekt för styrka som du vill synkronisera till Azure Log Analytics via ITSMC.  Dessa arbets uppgifter importeras till Log Analytics.   **Alternativ:**   Incidenter, ändrings begär Anden.|
| **Synkronisera data** | Ange antalet senaste dagar som du vill att data ska visas. **Maxgräns**: 120 dagar. |
| **Skapa nytt konfigurations objekt i ITSM-lösningen** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När det här alternativet är markerat skapar ITSMC det påverkade CIs-objektet som konfigurations objekt (om det inte finns något befintligt CIs) i det ITSM system som stöds. **Standard**: inaktiverat.|

![Upphandlings anslutning](media/itsmc-connections/itsm-connections-provance-latest.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från denna instans av bevisning importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här upphandlings instansen.

Läs mer: [skapa ITSM arbets objekt från Azure-aviseringar](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Ansluta Cherwell till Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure

Följande avsnitt innehåller information om hur du ansluter din Cherwell-produkt till ITSMC i Azure.

### <a name="prerequisites"></a>Förutsättningar

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Ett klient-ID genererades. Mer information: [generera klient-ID för Cherwell](#generate-client-id-for-cherwell).
- Användar roll: administratör.

### <a name="connection-procedure"></a>Anslutnings procedur

Använd följande procedur för att skapa en upphandlings anslutning:

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2.  Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.
    ![ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till**längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> 
> Alla dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutningsnamn**   | Ange ett namn för den Cherwell-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbets objekt i den här ITSM/Visa detaljerad Log Analytics. |
| **Partner typ**   | Välj **Cherwell.** |
| **Användarnamn**   | Ange det användar namn för Cherwell som kan ansluta till ITSMC. |
| **Lösenord**   | Ange lösen ordet som är kopplat till det här användar namnet. **Obs:** Användar namn och lösen ord används endast för att skapa autentiseringstoken och lagras inte var som helst i ITSMC-tjänsten.|
| **Server-URL**   | Ange URL: en för den Cherwell-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera anslutningen, som du skapade i din Cherwell-instans.   |
| **Omfång för data synkronisering**   | Välj de Cherwell arbets objekt som du vill synkronisera via ITSMC.  Dessa arbets uppgifter importeras till Log Analytics.   **Alternativ:**  Incidenter, ändrings begär Anden. |
| **Synkronisera data** | Ange antalet senaste dagar som du vill att data ska visas. **Maxgräns**: 120 dagar. |
| **Skapa nytt konfigurations objekt i ITSM-lösningen** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När det här alternativet är markerat skapar ITSMC det påverkade CIs-objektet som konfigurations objekt (om det inte finns något befintligt CIs) i det ITSM system som stöds. **Standard**: inaktiverat. |


![Upphandlings anslutning](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från den här Cherwell-instansen importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här Cherwell-instansen.

Läs mer: [skapa ITSM arbets objekt från Azure-aviseringar](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generera klient-ID för Cherwell

Använd följande procedur för att generera klient-ID/nyckel för Cherwell:

1. Logga in på din Cherwell-instans som administratör.
2. Klicka på **säkerhets** > **Redigera REST API klient inställningar**.
3. Välj **Skapa ny klient** > **klient hemlighet**.

    ![Användar-ID för Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Nästa steg
 - [Skapa ITSM arbets objekt från Azure-aviseringar](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
