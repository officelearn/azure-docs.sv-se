---
title: 'Självstudiekurs: Konfigurera arbetsdag för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Workday.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf0cbfb91332d60516432a7a67fb10404d89113
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683839"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera arbetsdag för automatisk etablering av användare

Syftet med den här självstudien är att visa de steg du behöver utföra för att importera arbetarprofiler från Workday till både Active Directory och Azure Active Directory, med valfri tillbakaskrivning av e-postadress och användarnamn till Workday.

## <a name="overview"></a>Översikt

[Tjänsten Azure Active Directory-användaretablering](../app-provisioning/user-provisioning.md) integreras med [API:et för arbetsdagars personal](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) för att etablera användarkonton. Azure AD använder den här anslutningen för att aktivera följande arbetsflöden för användaretablering:

* **Etablera användare i Active Directory** - Etablera valda uppsättningar användare från Workday till en eller flera Active Directory-domäner.

* **Etablera molnanvändare till Azure Active Directory** – I scenarier där lokal Active Directory inte används kan användare etableras direkt från Arbetsdag till Azure Active Directory med hjälp av Azure AD-användaretableringstjänsten.

* **Skriv tillbaka e-postadress och användarnamn till Workday** - Azure AD-användarens etableringstjänst kan skriva tillbaka e-postadresserna och användarnamnet från Azure AD till Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Vilka personalscenarier täcker det?

Arbetsflöden för etablering av Workday-användare som stöds av azure AD-användarens etableringstjänst möjliggör automatisering av följande scenarier för hantering av personal och identitetslivscykel:

* **Anställa nya medarbetare** – När en ny medarbetare läggs till i Workday skapas ett användarkonto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md), med tillbakaskrivning av e-postadressen till Workday.

* **Medarbetarattribut och profiluppdateringar** – När en medarbetarpost uppdateras i Workday (till exempel namn, titel eller chef) uppdateras deras användarkonto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program](../app-provisioning/user-provisioning.md)som stöds av Azure AD .

* **Uppsägning av medarbetare** – När en medarbetare avslutas under Arbetsdag inaktiveras deras användarkonto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

* **Medarbetare återanställer** - När en medarbetare återanställs i Workday kan deras gamla konto återaktiveras eller återupprättas (beroende på dina önskemål) till Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här lösning för etablering av användare som är bäst lämpad för?

Den här lösningen för etablering av arbetsdagsanvändare är idealisk för:

* Organisationer som önskar en förbyggd, molnbaserad lösning för användaretablering av Workday

* Organisationer som kräver direkt användaretablering från Arbetsdag till Active Directory eller Azure Active Directory

* Organisationer som kräver att användare etableras med hjälp av data som hämtats från workday HCM-modulen (se [Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organisationer som kräver att ansluta, flytta och låta användare synkroniseras till en eller flera Active Directory-skogar, domäner och organisationsenheter baserat endast på ändringsinformation som identifieras i workday HCM-modulen (se [Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organisationer som använder Office 365 för e-post

## <a name="solution-architecture"></a>Lösningsarkitektur

I det här avsnittet beskrivs lösningsarkitekturen för heltäckande användaretablering för vanliga hybridmiljöer. Det finns två relaterade flöden:

* **Auktoritärt HR-dataflöde – från arbetsdag till lokal Active Directory:** I det här flödet uppstår arbetarhändelser (till exempel Nyanställda, Överföringar, Uppsägningar) först i molnet Workday HR-klienten och sedan flödar händelsedata till lokal Active Directory via Azure AD och etableringsagenten. Beroende på händelsen kan det leda till att skapa/uppdatera/aktivera/inaktivera åtgärder i AD.
* **Återskrivningsflöde för e-post och användarnamn – från lokal Active Directory till Arbetsdag:** När kontoskapandet är klart i Active Directory synkroniseras det med Azure AD via Azure AD Connect och e-post- och användarnamnsattribut kan skrivas tillbaka till Workday.

![Översikt](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Dataflöde från till användare

1. HR-teamet utför arbetartransaktioner (Joiners/Movers/Leavers eller New Hires/Transfers/Terminations) i Workday HCM
2. Azure AD-etableringstjänsten kör schemalagda synkroniseringar av identiteter från Workday HR och identifierar ändringar som måste bearbetas för synkronisering med lokal Active Directory.
3. Azure AD-etableringstjänsten anropar den lokala Azure AD Connect-etableringsagenten med en begäranhetstjänst som innehåller AD-konto skapa/uppdatera/aktivera/inaktivera åtgärder.
4. Azure AD Connect-etableringsagenten använder ett tjänstkonto för att lägga till/uppdatera AD-kontodata.
5. Azure AD Connect/ AD Sync-motorn kör deltasynkronisering för att hämta uppdateringar i AD.
6. Active Directory-uppdateringarna synkroniseras med Azure Active Directory.
7. Om den nedskrivningsanslutningen för arbetsdag är konfigurerad skrivs tillbaka e-postattribut och användarnamn till Workday, baserat på det matchande attribut som används.

## <a name="planning-your-deployment"></a>Planera distributionen

Innan du påbörjar workday-integreringen bör du läsa förutsättningarna nedan och läs följande vägledning om hur du matchar dina aktuella Active Directory-arkitektur- och användaretableringskrav med de lösningar som tillhandahålls av Azure Active Directory. En omfattande [distributionsplan](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) med planeringsförslag finns också för att hjälpa dig att samarbeta med din Workday-integrationspartner och HR-intressenter.

Detta avsnitt omfattar följande aspekter av planering:

* [Krav](#prerequisites)
* [Välja etablering av anslutningsappar som ska distribueras](#selecting-provisioning-connector-apps-to-deploy)
* [Planera distribution av Azure AD Connect-etableringsagent](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integrera med flera Active Directory-domäner](#integrating-with-multiple-active-directory-domains)
* [Planera arbetsdag till mappning och omvandlingar för Active Directory-användarattribut](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En giltig Azure AD Premium P1 eller högre prenumerationslicens för varje användare som kommer från Workday och etableras i antingen lokal Active Directory eller Azure Active Directory.
* Global azure AD-administratörsåtkomst för att konfigurera etableringsagenten
* En Workday-implementeringsklient för testning och integration
* Administratörsbehörigheter i Workday för att skapa en användare av systemintegration och göra ändringar i testmedardata för testning
* För användaretablering till Active Directory krävs en server som kör Windows Server 2012 eller senare med .NET 4.7.1+ runtime för att vara värd för den [lokala etableringsagenten](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) för synkronisering av användare mellan Active Directory och Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Välja etablering av anslutningsappar som ska distribueras

För att underlätta etablering av arbetsflöden mellan Workday och Active Directory tillhandahåller Azure AD flera etableringsappar som du kan lägga till från Azure AD-appgalleriet:

![Azure AD App-galleriet](./media/workday-inbound-tutorial/wd_gallery.png)

* **Arbetsdag till Active Directory-användaretablering** – Den här appen underlättar etablering av användarkonton från Arbetsdag till en enda Active Directory-domän. Om du har flera domäner kan du lägga till en instans av den här appen från Azure AD-appgalleriet för varje Active Directory-domän som du behöver etablera till.

* **Arbetsdag till Azure AD-användaretablering** – Medan Azure AD Connect är det verktyg som ska användas för att synkronisera Active Directory-användare till Azure Active Directory, kan den här appen användas för att underlätta etablering av molnanvändare från Arbetsdag till en enda Azure Active Directory-klientorganisation.

* **Svar på arbetsdag** - Den här appen underlättar återskrivning av användarens e-postadresser från Azure Active Directory till Workday.

> [!TIP]
> Den vanliga "Workday"-appen används för att konfigurera enkel inloggning mellan Workday och Azure Active Directory.

Använd beslutsflödesdiagrammet nedan för att identifiera vilka workday-etableringsappar som är relevanta för ditt scenario.
    ![Beslut Flödesschema](./media/workday-inbound-tutorial/wday_app_flowchart.png "Beslut Flödesschema")

Använd innehållsförteckningen för att gå till det relevanta avsnittet i den här självstudien.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Planera distribution av Azure AD Connect-etableringsagent

> [!NOTE]
> Det här avsnittet är bara relevant om du planerar att distribuera Workday till Active Directory User Provisioning App. Du kan hoppa över detta om du distribuerar tillbakaskrivningen arbetsdag eller arbetsdagen till Azure AD-användarenableringsappen.

Lösningen för etablering av arbetsdagar till AD-användare kräver distribution av en eller flera etableringsagenter på servrar som kör Windows 2012 R2 eller senare med minst 4 GB RAM och .NET 4.7.1+ körningstid. Följande överväganden måste beaktas innan avsättningsagenten installeras:

* Kontrollera att värdservern som kör etableringsagenten har nätverksåtkomst till ad-måldomänen
* Konfigurationsguiden för etableringsagenten registrerar agenten med din Azure AD-klientorganisation och registreringsprocessen kräver åtkomst till *.msappproxy.net över TLS-port 443. Kontrollera att regler för utgående brandväggar finns på plats som aktiverar den här kommunikationen. Agenten stöder [utgående HTTPS-proxykonfiguration](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* Etableringsagenten använder ett tjänstkonto för att kommunicera med de lokala AD-domänerna. Innan agenten installeras rekommenderar vi att du skapar ett tjänstkonto med domänadministratörsbehörigheter och ett lösenord som inte upphör att gälla.  
* Under etableringsagentkonfigurationen kan du välja domänkontrollanter som ska hantera etableringsbegäranden. Om du har flera geografiskt distribuerade domänkontrollanter installerar du etableringsagenten på samma plats som den eller dina önskade domänkontrollanter för att förbättra tillförlitligheten och prestandan hos slutlösningen
* För hög tillgänglighet kan du distribuera mer än en etableringsagent och registrera den för att hantera samma uppsättning lokala AD-domäner.

> [!IMPORTANT]
> I produktionsmiljöer rekommenderar Microsoft att du har minst 3 etableringsagenter konfigurerade med din Azure AD-klient för hög tillgänglighet.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integrera med flera Active Directory-domäner

> [!NOTE]
> Det här avsnittet är bara relevant om du planerar att distribuera Workday till Active Directory User Provisioning App. Du kan hoppa över detta om du distribuerar tillbakaskrivningen arbetsdag eller arbetsdagen till Azure AD-användarenableringsappen.

Beroende på din Active Directory-topologi måste du bestämma antalet anslutningsappar för användaretablering och antalet etableringsagenter som ska konfigureras. Nedan listas några av de vanliga distributionsmönster som du kan referera till när du planerar distributionen.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Distributionsscenario #1 : En arbetsdagsklient -> en ad-domän

I det här fallet har du en Workday-klient och du vill etablera användare till en enda AD-domän för mål. Här är den rekommenderade produktionskonfigurationen för den här distributionen.

|   |   |
| - | - |
| Nej. av avstämmda medel för att sätta in på lokal | 3 (för hög tillgänglighet och växla över) |
| Nej. av Workday to AD-användaretableringsappar att konfigurera i Azure-portalen | 1 |

  ![Scenario 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Distributionsscenario #2 : En arbetsdagsklient -> flera underordnade AD-domäner

Det här scenariot innebär att etablera användare från Workday till flera ad-underordnade domäner i en skog. Här är den rekommenderade produktionskonfigurationen för den här distributionen.

|   |   |
| - | - |
| Nej. av avstämmda medel för att sätta in på lokal | 3 (för hög tillgänglighet och växla över) |
| Nej. av Workday to AD-användaretableringsappar att konfigurera i Azure-portalen | en app per underordnad domän |

  ![Scenario 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Distributionsscenario #3 : Single Workday Tenant -> Disjoint AD-skogar

Det här scenariot innebär att användare etableras från Workday till domäner i osammanhängande AD-skogar. Här är den rekommenderade produktionskonfigurationen för den här distributionen.

|   |   |
| - | - |
| Nej. av avstämmda medel för att sätta in på lokal | 3 per osammanhängande AD-skog |
| Nej. av Workday to AD-användaretableringsappar att konfigurera i Azure-portalen | en app per underordnad domän |

  ![Scenario 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Planera arbetsdag till mappning och omvandlingar för Active Directory-användarattribut

> [!NOTE]
> Det här avsnittet är bara relevant om du planerar att distribuera Workday till Active Directory User Provisioning App. Du kan hoppa över detta om du distribuerar tillbakaskrivningen arbetsdag eller arbetsdagen till Azure AD-användarenableringsappen.

Innan du konfigurerar användaretablering till en Active Directory-domän bör du tänka på följande frågor. Svaren på dessa frågor avgör hur dina omfångsfilter och attributmappningar måste ställas in.

* **Vilka användare i Workday måste etableras i den här Active Directory-skogen?**

  * *Exempel: Användare där attributet Workday "Company" innehåller värdet "Contoso" och attributet "Worker_Type" innehåller "Regular"*

* **Hur dirigeras användare till olika organisationsenheter?**

  * *Exempel: Användare dirigeras till RU:er som motsvarar en kontorsplats, enligt definitionen i attributen "Kommun" och "Country_Region_Reference"*

* **Hur ska följande attribut fyllas i i Active Directory?**

  * Gemensamt namn (cn)
    * *Exempel: Använd värdet Workday User_ID, enligt personaluppsättningen*

  * Anställnings-ID (employeeid)
    * *Exempel: Använd värdet Workday Worker_ID*

  * NAMN PÅ SAM-konto (sAMAccountName)
    * *Exempel: Använd värdet Workday User_ID, filtrerat via ett Azure AD-etableringsuttryck för att ta bort ogiltiga tecken*

  * Namn på användarnamn (userPrincipalName)
    * *Exempel: Använd värdet Workday User_ID, med ett Azure AD-etableringsuttryck för att lägga till ett domännamn*

* **Hur ska användare matchas mellan Workday och Active Directory?**

  * *Exempel: Användare med ett specifikt Worker_ID-värde för arbetsdag matchas med Active Directory-användare där "employeeID" har samma värde. Om värdet Worker_ID inte hittas i Active Directory skapar du en ny användare.*
  
* **Innehåller Active Directory-skogen redan de användar-ID:er som krävs för att matchningslogiken ska fungera?**

  * *Exempel: Om den här inställningen är en ny Workday-distribution rekommenderar vi att Active Directory fylls i i förinfylld med rätt workday-Worker_ID värden (eller unikt ID-värde som du väljer) för att hålla matchningslogiken så enkel som möjligt.*

Hur du ställer in och konfigurerar dessa speciella etableringsappar är ämnet för de återstående avsnitten i den här självstudien. Vilka appar du väljer att konfigurera beror på vilka system du behöver etablera till och hur många Active Directory-domäner och Azure AD-klienter som finns i din miljö.

## <a name="configure-integration-system-user-in-workday"></a>Konfigurera användare av integrationssystem i Workday

Ett vanligt krav för alla workday-etableringskopplingskopplingar är att de kräver autentiseringsuppgifter för en användare av workday-integrationssystem för att ansluta till ARBETSDAGS-PERSONAL-API:ET. I det här avsnittet beskrivs hur du skapar en användare av integrationssystemet i Workday och har följande avsnitt:

* [Skapa en användare av integrationssystemet](#creating-an-integration-system-user)
* [Skapa en integrationssäkerhetsgrupp](#creating-an-integration-security-group)
* [Konfigurera domänsäkerhetsprincipbehörigheter](#configuring-domain-security-policy-permissions)
* [Konfigurera säkerhetsprincipbehörigheter för affärsprocesser](#configuring-business-process-security-policy-permissions)
* [Aktivera säkerhetsprincipändringar](#activating-security-policy-changes)

> [!NOTE]
> Det är möjligt att kringgå den här proceduren och i stället använda ett globalt workday-administratörskonto som systemintegrationskonto. Detta kan fungera bra för demonstrationer, men rekommenderas inte för produktionsdistributioner.

### <a name="creating-an-integration-system-user"></a>Skapa en användare av integrationssystemet

**Så här skapar du en användare av integrationssystemet:**

1. Logga in på din Workday-klient med ett administratörskonto. Ange skapa användare i sökrutan i **arbetsdagsprogrammet**och klicka sedan på **Skapa användare av integrationssystemet**.

   ![Skapa användare](./media/workday-inbound-tutorial/wd_isu_01.png "Skapa användare")
2. Slutför aktiviteten **Skapa integrationssystemanvändare** genom att ange ett användarnamn och lösenord för en ny användare av integrationssystemet.  
  
   * Lämna alternativet **Kräv nytt lösenord vid nästa inloggning** avmarkerat, eftersom den här användaren kommer att logga in programmässigt.
   * Lämna **timeout-protokollet** för session med standardvärdet 0, vilket förhindrar att användarens sessioner tar tid i förtid.
   * Välj alternativet **Tillåt inte gränssnittssessioner** eftersom det ger ett extra säkerhetslager som förhindrar att en användare med lösenordet för integrationssystemet loggar in på Workday.

   ![Skapa användare av integrationssystem](./media/workday-inbound-tutorial/wd_isu_02.png "Skapa användare av integrationssystem")

### <a name="creating-an-integration-security-group"></a>Skapa en integrationssäkerhetsgrupp

I det här steget skapar du en obegränsad eller begränsad säkerhetsgrupp för integrationssystem i Workday och tilldelar integrationssystemets användare som skapats i föregående steg till den här gruppen.

**Så här skapar du en säkerhetsgrupp:**

1. Ange skapa säkerhetsgrupp i sökrutan och klicka sedan på **Skapa säkerhetsgrupp**.

    ![Skapasäkerhetsgrupp](./media/workday-inbound-tutorial/wd_isu_03.png "Skapasäkerhetsgrupp")
2. Slutför aktiviteten **Skapa säkerhetsgrupp.** 

   * Det finns två typer av säkerhetsgrupper i Workday:
     * **Obegränsad:** Alla medlemmar i säkerhetsgruppen kan komma åt alla datainstanser som skyddas av säkerhetsgruppen.
     * **Begränsad:** Alla medlemmar i säkerhetsgruppen har kontextuell åtkomst till en delmängd av datainstanser (rader) som säkerhetsgruppen kan komma åt.
   * Kontrollera med din Workday-integrationspartner att välja lämplig säkerhetsgrupptyp för integreringen.
   * När du känner till grupptypen väljer du **Security Group (Unconstrained)** eller **Integration System Security Group (Begränsad)** från listrutan **Typ av klientsäkerhetsgrupp.**

     ![Skapasäkerhetsgrupp](./media/workday-inbound-tutorial/wd_isu_04.png "Skapasäkerhetsgrupp")

3. När skapandet av säkerhetsgruppen har lyckats visas en sida där du kan tilldela medlemmar till säkerhetsgruppen. Lägg till den nya integrationssystemet användaren som skapats i föregående steg till den här säkerhetsgruppen. Om du använder *begränsad* säkerhetsgrupp måste du också välja lämpligt organisationsomfång.

    ![Redigera säkerhetsgrupp](./media/workday-inbound-tutorial/wd_isu_05.png "Redigera säkerhetsgrupp")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurera domänsäkerhetsprincipbehörigheter

I det här steget ska du bevilja principbehörigheter för domänsäkerhet för arbetardata till säkerhetsgruppen.

**Så här konfigurerar du domänsäkerhetsprincipbehörigheter:**

1. Ange **domänsäkerhetskonfiguration** i sökrutan och klicka sedan på länken **Domänsäkerhetskonfigurationsrapport**.  

    ![Principer för domänsäkerhet](./media/workday-inbound-tutorial/wd_isu_06.png "Principer för domänsäkerhet")  
2. Sök efter följande domäner i textrutan **Domän** och lägg till dem i filtret en efter en.  
   * *Etablera externt konto*
   * *Arbetardata: Arbetare*
   * *Arbetardata: Rapporter för offentliga arbetare*
   * *Persondata: Kontaktinformation för arbete*
   * *Arbetardata: Alla befattningar*
   * *Arbetardata: Aktuell bemanningsinformation*
   * *Arbetardata: Företagstitel i arbetarprofilen*
   * *Arbetsdag konton*
   
     ![Principer för domänsäkerhet](./media/workday-inbound-tutorial/wd_isu_07.png "Principer för domänsäkerhet")  

     ![Principer för domänsäkerhet](./media/workday-inbound-tutorial/wd_isu_08.png "Principer för domänsäkerhet") 

     Klicka på **OK**.

3. I rapporten som visas väljer du ellipsen (...) som visas **bredvid Extern kontoetablering** och klickar på menyalternativet **Domän -> Redigera säkerhetsprincipbehörigheter**

    ![Principer för domänsäkerhet](./media/workday-inbound-tutorial/wd_isu_09.png "Principer för domänsäkerhet")  

4. På sidan **Behörigheter för redigera domänsäkerhetsprinciper bläddrar** du ned till avsnittet **Integrationsbehörigheter**. Klicka på tecknet "+" för att lägga till integrationssystemgruppen i listan över säkerhetsgrupper med **behörigheter för Få** och **placera** integrering.

    ![Redigera behörighet](./media/workday-inbound-tutorial/wd_isu_10.png "Redigera behörighet")  

5. Klicka på tecknet "+" för att lägga till integrationssystemgruppen i listan över säkerhetsgrupper med **behörigheter för Få** och **placera** integrering.

    ![Redigera behörighet](./media/workday-inbound-tutorial/wd_isu_11.png "Redigera behörighet")  

6. Upprepa steg 3-5 ovan för var och en av dessa återstående säkerhetsprinciper:

   | Åtgärd | Domänsäkerhetsprincip |
   | ---------- | ---------- |
   | Få och sätta | Arbetardata: Rapporter för offentliga arbetare |
   | Få och sätta | Persondata: Kontaktinformation för arbete |
   | Hämta | Arbetardata: Arbetare |
   | Hämta | Arbetardata: Alla befattningar |
   | Hämta | Arbetardata: Aktuell bemanningsinformation |
   | Hämta | Arbetardata: Företagstitel i arbetarprofilen |
   | Få och sätta | Arbetsdag konton |

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurera säkerhetsprincipbehörigheter för affärsprocesser

I det här steget ska du bevilja principbehörigheter för "affärsprocesssäkerhet" för arbetardata till säkerhetsgruppen. Det här steget krävs för att konfigurera appappen Workday.

**Så här konfigurerar du säkerhetsprincipbehörigheter för affärsprocesser:**

1. Ange **affärsprocessprincip** i sökrutan och klicka sedan på länken Redigera säkerhetsprincip för **affärsprocesser.**  

    ![Säkerhetsprinciper för affärsprocesser](./media/workday-inbound-tutorial/wd_isu_12.png "Säkerhetsprinciper för affärsprocesser")  

2. Sök efter *Kontakt* i textrutan **Affärsprocesstyp** och välj **Kontaktändringsaffärsprocess** och klicka på **OK**.

    ![Säkerhetsprinciper för affärsprocesser](./media/workday-inbound-tutorial/wd_isu_13.png "Säkerhetsprinciper för affärsprocesser")  

3. Bläddra till avsnittet **Underhåll kontaktinformation (webbtjänst)** på sidan Redigera säkerhetsprincip för **affärsprocesser.**

    ![Säkerhetsprinciper för affärsprocesser](./media/workday-inbound-tutorial/wd_isu_14.png "Säkerhetsprinciper för affärsprocesser")  

4. Välj och lägg till den nya säkerhetsgruppen för integrationssystem i listan över säkerhetsgrupper som kan initiera begäran om webbtjänster. Klicka på **Klar**. 

    ![Säkerhetsprinciper för affärsprocesser](./media/workday-inbound-tutorial/wd_isu_15.png "Säkerhetsprinciper för affärsprocesser")  

### <a name="activating-security-policy-changes"></a>Aktivera säkerhetsprincipändringar

**Så här aktiverar du ändringar av säkerhetsprinciper:**

1. Ange aktivera i sökrutan och klicka sedan på länken **Aktivera väntande säkerhetsprincipändringar**.

    ![Aktivera](./media/workday-inbound-tutorial/wd_isu_16.png "Aktivera")

1. Starta aktiviteten Aktivera väntande säkerhetsprincipändringar genom att ange en kommentar för granskning och klicka sedan på **OK**.
1. Slutför uppgiften på nästa skärm genom att markera kryssrutan **Bekräfta**och klicka sedan på **OK**.

    ![Aktivera väntande säkerhet](./media/workday-inbound-tutorial/wd_isu_18.png "Aktivera väntande säkerhet")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurera användaretablering från arbetsdag till Active Directory

I det här avsnittet finns steg för etablering av användarkonton från Workday till varje Active Directory-domän inom ramen för integreringen.

* [Lägg till etableringsappen och hämta etableringsagenten](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Installera och konfigurera lokala etableringsagenter](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurera anslutning till Arbetsdag och Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Konfigurera attributmappningar](#part-4-configure-attribute-mappings)
* [Aktivera och starta etablering av användare](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Del 1: Lägg till etableringsappen och hämta etableringsagenten

**Så här konfigurerar du Workday till Active Directory-etablering:**

1. Gå till <https://portal.azure.com>.

2. Sök efter och välj **Azure Active Directory**i Azure-portalen .

3. Välj **Företagsprogram**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj kategorin **Alla.**

5. Sök efter **arbetsdagsetablering i Active Directory**och lägg till appen från galleriet.

6. När appen har lagts till och skärmen för appinformation visas väljer du **Etablering**.

7. Ändra **etableringsläget** **Mode** till **Automatiskt**.

8. Klicka på informationsbanderollen som visas för att hämta etableringsagenten. 

   ![Ladda ner Agent](./media/workday-inbound-tutorial/pa-download-agent.png "Skärmen Ladda ner agent")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Del 2: Installera och konfigurera lokala etableringsagenter

Om du vill etablera den lokala Active Directory måste etableringsagenten installeras på en server med .NET 4.7.1+ Framework och nätverksåtkomst till önskade Active Directory-domäner.

> [!TIP]
> Du kan kontrollera versionen av .NET-ramverket på servern med hjälp av instruktionerna [här](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Om servern inte har .NET 4.7.1 eller högre installerat kan du hämta den [härifrån](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Överför den nedladdade agentinstallationsprogrammet till servervärden och följ stegen nedan för att slutföra agentkonfigurationen.

1. Logga in på Windows Server där du vill installera den nya agenten.

1. Starta installationsprogrammet för etableringsagenten, godkänn villkoren och klicka på knappen **Installera.**

   ![Installera skärm](./media/workday-inbound-tutorial/pa_install_screen_1.png "Installera skärm")
   
1. När installationen är klar startar guiden och du ser skärmen **Anslut Azure AD.** Klicka på knappen **Autentisera** för att ansluta till din Azure AD-instans.

   ![Ansluta till Azure Active Directory](./media/workday-inbound-tutorial/pa_install_screen_2.png "Ansluta till Azure Active Directory")
   
1. Autentisera till din Azure AD-instans med hjälp av globala administratörsautentiseringsuppgifter.

   ![Admin Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Admin Auth")

   > [!NOTE]
   > Azure AD-administratörsautentiseringsuppgifterna används endast för att ansluta till din Azure AD-klientorganisation. Agenten lagrar inte autentiseringsuppgifterna lokalt på servern.

1. När autentiseringen med Azure AD har slutförts visas skärmen **Anslut Active Directory.** I det här steget anger du ditt AD-domännamn och klickar på knappen **Lägg till katalog.**

   ![Lägg till katalog](./media/workday-inbound-tutorial/pa_install_screen_4.png "Lägg till katalog")
  
1. Du uppmanas nu att ange de autentiseringsuppgifter som krävs för att ansluta till AD-domänen. På samma skärm kan du använda **prioriteten Välj domänkontrollant** för att ange domänkontrollanter som agenten ska använda för att skicka etableringsbegäranden.

   ![Domänautentiseringsuppgifter](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. När du har konfigurerat domänen visar installationsprogrammet en lista över konfigurerade domäner. På den här skärmen kan du upprepa steg #5 och #6 lägga till fler domäner eller klicka på **Nästa** för att gå vidare till agentregistrering.

   ![Konfigurerade domäner](./media/workday-inbound-tutorial/pa_install_screen_6.png "Konfigurerade domäner")

   > [!NOTE]
   > Om du har flera AD-domäner (t.ex. na.contoso.com, emea.contoso.com) lägger du till varje domän individuellt i listan.
   > Det räcker inte att bara lägga till den överordnade domänen (t.ex. contoso.com). Du måste registrera varje underordnad domän hos agenten.
   
1. Granska konfigurationsinformationen och klicka på **Bekräfta** för att registrera agenten.
  
   ![Bekräfta skärm](./media/workday-inbound-tutorial/pa_install_screen_7.png "Bekräfta skärm")
   
1. Konfigurationsguiden visar förloppet för agentregistreringen.
  
   ![Registrering av ombud](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registrering av ombud")
   
1. När agentregistreringen har slutförts kan du klicka på **Avsluta** för att avsluta guiden.
  
   ![Avsluta skärm](./media/workday-inbound-tutorial/pa_install_screen_9.png "Avsluta skärm")
   
1. Verifiera installationen av agenten och se till att den körs genom att öppna snapin-modulen "Tjänster" och leta efter tjänsten "Microsoft Azure AD Connect Provisioning Agent"
  
   ![Tjänster](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Del 3: Konfigurera anslutningen till Workday och Active Directory i etableringsappen
I det här steget upprättar vi anslutning med Workday och Active Directory i Azure-portalen. 

1. Gå tillbaka till workday-till Active Directory-användaretableringsappen som skapats i [del 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) i Azure-portalen

1. Fyll i avsnittet **Administratörsautentiseringsuppgifter** på följande sätt:

   * **Användarnamn för arbetsdag** – Ange användarnamnet för systemkontot för workday-integreringssystem, med klientdomännamnet bifogat. Det bör se ut ungefär som: **användarnamn\@tenant_name**

   * **Arbetsdag lösenord -** Ange lösenordet för systemkontot för system för arbetsdagsintegrering

   * **API-URL för workday-webbtjänster –** Ange URL:en till slutpunkten för webbtjänster arbetsdag för din klientorganisation. Det här värdet https://wd3-impl-services1.workday.com/ccx/service/contoso4ska se ut så här: , där *contoso4* ersätts med rätt klientnamn och *wd3-impl* ersätts med rätt miljösträng.

     > [!NOTE]
     > Som standard använder appen Workday Web Services v21.1 om ingen versionsinformation anges i URL:en. Om du vill använda en specifik API-version för Workday Web Services använder du URL-formatet:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Exempel: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0

   * **Active Directory Skog -** Namnet på din Active Directory-domän, som registrerats hos agenten. Använd listrutan för att välja måldomänen för etablering. Det här värdet är vanligtvis en sträng som: *contoso.com*

   * **Active Directory-behållare -** Ange behållaren DN där agenten ska skapa användarkonton som standard.
        Exempel: *OU=Standardanvändare,OU=Användare,DC=contoso,DC=test*
        
     > [!NOTE]
     > Den här inställningen spelar bara in för att skapa användarkonton om *attributet parentDistinguishedName* inte har konfigurerats i attributmappningarna. Den här inställningen används inte för användarsökning eller uppdateringsåtgärder. Hela domänunderträdet faller i sökåtgärdens omfattning.

   * **E-postmeddelande –** Ange din e-postadress och markera kryssrutan "skicka e-post om fel inträffar" .

     > [!NOTE]
     > Azure AD-etableringstjänsten skickar e-postmeddelande om etableringsjobbet hamnar i [karantäntillstånd.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Klicka på knappen **Testa anslutning.** Om anslutningstestet lyckas klickar du på knappen **Spara** högst upp. Om det misslyckas dubbelkollar du att Workday-autentiseringsuppgifterna och AD-autentiseringsuppgifterna som konfigurerats för agentinställningarna är giltiga.

     ![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

   * När autentiseringsuppgifterna har **sparats** visas **standardmappningen Synkronisera workday-arbetare till Lokal Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>Del 4: Konfigurera attributmappningar

I det här avsnittet ska du konfigurera hur användardata flödar från Arbetsdag till Active Directory.

1. Klicka på Synkronisera arbetare till **Lokal Active Directory**på fliken Etablering under **Mappningar.**

1. I fältet **Källobjektomfattning** kan du välja vilka uppsättningar användare i Workday som ska vara i omfång för etablering till AD genom att definiera en uppsättning attributbaserade filter. Standardomfånget är "alla användare i Workday". Exempelfilter:

   * Exempel: Omfattning för användare med arbetar-ID mellan 1000000 och 2000000 (exklusive 2000000)

      * Attribut: WorkerID

      * Operatör: REGEX Match

      * Värde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exempel: Endast anställda och inte villkorade arbetstagare

      * Attribut: EmployeeID

      * Operator: ÄR INTE NULL

   > [!TIP]
   > När du konfigurerar etableringsappen för första gången måste du testa och verifiera attributmappningar och uttryck för att se till att den ger dig önskat resultat. Microsoft rekommenderar att du använder omfångsfiltren under **Källobjektomfattning** för att testa mappningarna med några testanvändare från Workday. När du har verifierat att mappningarna fungerar kan du antingen ta bort filtret eller gradvis expandera det till att omfatta fler användare.

   > [!CAUTION] 
   > Standardbeteendet för etableringsmotorn är att inaktivera/ta bort användare som inte omfattas. Detta kanske inte är önskvärt i din arbetsdag till AD-integrering. Om du vill åsidosätta det här standardbeteendet finns i artikeln [Hoppa över borttagning av användarkonton som inte omfattas](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. I fältet **Målobjektåtgärder** kan du filtrera vilka åtgärder som utförs i Active Directory globalt. **Skapa** och **uppdatera** är vanligast.

1. I avsnittet **Attributmappningar** kan du definiera hur enskilda Workday-attribut mappas till Active Directory-attribut.

1. Klicka på en befintlig attributmappning för att uppdatera den, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En enskild attributmappning stöder följande egenskaper:

      * **Mappningstyp**

         * **Direkt** – Skriver värdet för attributet Workday till AD-attributet, utan ändringar

         * **Constant** - Skriv ett statiskt, konstant strängvärde till AD-attributet

         * **Uttryck** – Gör att du kan skriva ett anpassat värde till AD-attributet, baserat på ett eller flera Workday-attribut. [Mer information finns i den här artikeln om uttryck](../app-provisioning/functions-for-customizing-application-data.md).

      * **Källattribut** - Användarattributet från Workday. Om attributet du letar efter inte finns läser du [Anpassa listan över användarattribut för Arbetsdag](#customizing-the-list-of-workday-user-attributes).

      * **Standardvärde** – Valfritt. Om källattributet har ett tomt värde skrivs det här värdet i stället.
            Vanligaste konfigurationen är att lämna detta tomt.

      * **Målattribut** – Användarattributet i Active Directory.

      * **Matcha objekt med det här attributet** – Om den här mappningen ska användas för att unikt identifiera användare mellan Workday och Active Directory. Det här värdet anges vanligtvis i fältet Arbetar-ID för Workday, som vanligtvis mappas till ett av medarbetar-ID-attributen i Active Directory.

      * **Matchande prioritet** – Flera matchande attribut kan ställas in. När det finns flera utvärderas de i den ordning som definieras av det här fältet. Så snart en matchning hittas utvärderas inga ytterligare matchande attribut.

      * **Använd den här mappningen**

         * **Alltid** – Använd den här mappningen på både åtgärder för att skapa användare och uppdatera

         * **Endast under skapandet** - Använd den här mappningen endast på åtgärder för att skapa användare

1. Om du vill spara mappningarna klickar du på **Spara** högst upp i avsnittet Attributmappning.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Nedan följer några exempel attributmappningar mellan Workday och Active Directory, med några vanliga uttryck

* Uttrycket som mappar till *attributet parentDistinguishedName* används för att etablera en användare till olika data baserat på ett eller flera workday-källattribut. Det här exemplet placerar användare i olika ru:er baserat på vilken stad de befinner sig i.

* *Attributet userPrincipalName* i Active Directory genereras med hjälp av dupliceringsfunktionen [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) som kontrollerar att det finns ett genererat värde i AD-området för mål och bara anger det om det är unikt.  

* [Det finns dokumentation om att skriva uttryck här](../app-provisioning/functions-for-customizing-application-data.md). Det här avsnittet innehåller exempel på hur du tar bort specialtecken.

| ATTRIBUT FÖR ARBETSDAG | ACTIVE DIRECTORY-ATTRIBUT |  MATCHANDE ID? | SKAPA/ UPPDATERA |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID (ArbetarID)**  |  EmployeeID | **Ja** | Skrivet på skapa endast |
| **PreferredNameData**    |  Cn    |   |   Skrivet på skapa endast |
| **SelectUniqueValue(\@Join(" ", \[Join(".", FirstName\], \[\]LastName ),\@"contoso.com"), Join(" ",\]Join("."contoso.com contoso.com." \[\]\@\[\] \[\]\[**   | userPrincipalName     |     | Skrivet på skapa endast 
| **Ersätt(Mitten(Ersätt(\[\]UserID ,\[\\\\/\\\\\\\\\\\\\[\\\\"(\]\\:\\;\\ \\\\ \\\|\\\\=\\\\,\\\\+\\\\\*\\\\? \\\\\\)", "", , ), 1, 20), "([ .) \\ &lt; \\ \\ &gt; \] \*](file:///\\ \$.) *$)", , "", , )**      |    sAMAccountName            |     |         Skrivet på skapa endast |
| **Switch(\[\]Aktiv , "0", "Sant", "1", "Falskt")** |  kontoDisabled      |     | Skapa + uppdatering |
| **Förnamn**   | förnamn       |     |    Skapa + uppdatering |
| **Efternamn**   |   sn   |     |  Skapa + uppdatering |
| **PreferredNameData**  |  displayName |     |   Skapa + uppdatering |
| **Företag**         | company   |     |  Skapa + uppdatering |
| **TillsynOrganisering**  | avdelning  |     |  Skapa + uppdatering |
| **ChefReferens**   | manager  |     |  Skapa + uppdatering |
| **FöretagTitle**   |  title     |     |  Skapa + uppdatering | 
| **AddressLineData**    |  streetAddress (streetAddress)  |     |   Skapa + uppdatering |
| **Kommun**   |   l   |     | Skapa + uppdatering |
| **CountryReferenceTwoLetter**      |   Co |     |   Skapa + uppdatering |
| **CountryReferenceTwoLetter**    |  c  |     |         Skapa + uppdatering |
| **LandRegionReference** |  st     |     | Skapa + uppdatering |
| **ArbetsområdeReference** | fysisktDeliveryOfficeName    |     |  Skapa + uppdatering |
| **Postnummer**  |   Postnummer  |     | Skapa + uppdatering |
| **PrimaryWorkTelephone**  |  telefonAntal   |     | Skapa + uppdatering |
| **Fax**      | faksimilTelephoneNumber     |     |    Skapa + uppdatering |
| **Mobil**  |    mobil       |     |       Skapa + uppdatering |
| **Lokalreferens** |  preferredLanguage  |     |  Skapa + uppdatering |                                               
| **Switch(\[\]Kommun , "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", "London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=DC=DC=DC=DC=DC=DC=COM")**  | parentDistinguishedName     |     |  Skapa + uppdatering |

När konfigurationen för attributmappning är klar kan du nu [aktivera och starta tjänsten för användaretablering](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Konfigurera användaretablering till Azure AD

I följande avsnitt beskrivs steg för att konfigurera användaretablering från Workday till Azure AD för distributioner endast i molnet.

* [Lägga till Azure AD-etableringsappen och skapa anslutningen till Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurera mappningar av Workday- och Azure AD-attribut](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Aktivera och starta etablering av användare](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Följ endast proceduren nedan om du har molnanvändare som behöver etableras till Azure AD och inte lokalt Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Del 1: Lägga till azure AD-etableringsappen och skapa anslutningen till Workday

**Så här konfigurerar du Workday till Azure Active Directory-etablering för användare som endast är molnet:**

1. Gå till <https://portal.azure.com>.

2. Sök efter och välj **Azure Active Directory**i Azure-portalen .

3. Välj **Företagsprogram**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj sedan kategorin **Alla.**

5. Sök efter **Workday till Azure AD-etablering**och lägg till appen från galleriet.

6. När appen har lagts till och skärmen för appinformation visas väljer du **Etablering**.

7. Ändra **etableringsläget** **Mode** till **Automatiskt**.

8. Fyll i avsnittet **Administratörsautentiseringsuppgifter** på följande sätt:

   * **Användarnamn för arbetsdag** – Ange användarnamnet för systemkontot för workday-integreringssystem, med klientdomännamnet bifogat. Bör se ut ungefär som:username@contoso4

   * **Arbetsdag lösenord -** Ange lösenordet för systemkontot för system för arbetsdagsintegrering

   * **API-URL för workday-webbtjänster –** Ange URL:en till slutpunkten för webbtjänster arbetsdag för din klientorganisation. Det här värdet https://wd3-impl-services1.workday.com/ccx/service/contoso4ska se ut så här: , där *contoso4* ersätts med rätt klientnamn och *wd3-impl* ersätts med rätt miljösträng. Om den här webbadressen inte är känd kan du samarbeta med din workday-integrationspartner eller supportrepresentant för att fastställa rätt webbadress som ska användas.

     > [!NOTE]
     > Som standard använder appen Workday Web Services v21.1 om ingen versionsinformation anges i URL:en. Om du vill använda en specifik API-version för Workday Web Services använder du URL-formatet:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Exempel: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0


   * **E-postmeddelande –** Ange din e-postadress och markera kryssrutan "skicka e-post om fel inträffar" .

   * Klicka på knappen **Testa anslutning.**

   * Om anslutningstestet lyckas klickar du på knappen **Spara** högst upp. Om det misslyckas dubbelkollar du att arbetsdags-URL:en och autentiseringsuppgifterna är giltiga i Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Del 2: Konfigurera mappningar av Workday- och Azure AD-attribut

I det här avsnittet konfigurerar du hur användardata flödar från Arbetsdag till Azure Active Directory för molnanvändare.

1. Klicka på Synkronisera arbetare med **Azure AD**på fliken Etablering under **Mappningar.**

2. I fältet **Källobjektomfattning** kan du välja vilka uppsättningar användare i Workday som ska vara i omfång för etablering till Azure AD, genom att definiera en uppsättning attributbaserade filter. Standardomfånget är "alla användare i Workday". Exempelfilter:

   * Exempel: Omfattning för användare med arbetar-ID mellan 1000000 och 2000000

      * Attribut: WorkerID

      * Operatör: REGEX Match

      * Värde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exempel: Endast betingade arbetstagare och inte ordinarie personal

      * Attribut: VillkoratID

      * Operator: ÄR INTE NULL

3. I fältet **Målobjektåtgärder** kan du globalt filtrera vilka åtgärder som utförs på Azure AD. **Skapa** och **uppdatera** är vanligast.

4. I avsnittet **Attributmappningar** kan du definiera hur enskilda Workday-attribut mappas till Active Directory-attribut.

5. Klicka på en befintlig attributmappning för att uppdatera den, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En enskild attributmappning stöder följande egenskaper:

   * **Mappningstyp**

      * **Direkt** – Skriver värdet för attributet Workday till AD-attributet, utan ändringar

      * **Constant** - Skriv ett statiskt, konstant strängvärde till AD-attributet

      * **Uttryck** – Gör att du kan skriva ett anpassat värde till AD-attributet, baserat på ett eller flera Workday-attribut. [Mer information finns i den här artikeln om uttryck](../app-provisioning/functions-for-customizing-application-data.md).

   * **Källattribut** - Användarattributet från Workday. Om attributet du letar efter inte finns läser du [Anpassa listan över användarattribut för Arbetsdag](#customizing-the-list-of-workday-user-attributes).

   * **Standardvärde** – Valfritt. Om källattributet har ett tomt värde skrivs det här värdet i stället.
            Vanligaste konfigurationen är att lämna detta tomt.

   * **Målattribut** – Användarattributet i Azure AD.

   * **Matcha objekt med det här attributet** – Om det här attributet ska användas för att unikt identifiera användare mellan Workday och Azure AD. Det här värdet anges vanligtvis i fältet Arbetar-ID för Workday, som vanligtvis mappas till attributet Employee ID (new) eller ett tilläggsattribut i Azure AD.

   * **Matchande prioritet** – Flera matchande attribut kan ställas in. När det finns flera utvärderas de i den ordning som definieras av det här fältet. Så snart en matchning hittas utvärderas inga ytterligare matchande attribut.

   * **Använd den här mappningen**

     * **Alltid** – Använd den här mappningen på både åtgärder för att skapa användare och uppdatera

     * **Endast under skapandet** - Använd den här mappningen endast på åtgärder för att skapa användare

6. Om du vill spara mappningarna klickar du på **Spara** högst upp i avsnittet Attributmappning.

När konfigurationen för attributmappning är klar kan du nu [aktivera och starta tjänsten för användaretablering](#enable-and-launch-user-provisioning).

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Konfigurera tillbakaskrivning av Azure AD-attribut till Workday

Följ dessa instruktioner för att konfigurera tillbakaskrivning av e-postadresser och användarnamn från Azure Active Directory till Workday.

* [Lägga till appen för skrivbacksappen och skapa anslutningen till Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurera mappningar för tillbakaskrivningsattribut](#part-2-configure-writeback-attribute-mappings)
* [Aktivera och starta etablering av användare](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Del 1: Lägga till appen för skrivbackad och skapa anslutningen till Workday

**Så här konfigurerar du lösningslänk för arbetsdag:**

1. Gå till <https://portal.azure.com>.

2. Sök efter och välj **Azure Active Directory**i Azure-portalen .

3. Välj **Företagsprogram**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj sedan kategorin **Alla.**

5. Sök efter **tillbakaskrivning på arbetsdag**och lägg till appen från galleriet.

6. När appen har lagts till och skärmen för appinformation visas väljer du **Etablering**.

7. Ändra **etableringsläget** **Mode** till **Automatiskt**.

8. Fyll i avsnittet **Administratörsautentiseringsuppgifter** på följande sätt:

   * **Administratörsanvändarnamn** – Ange användarnamnet för systemkontot för workday-integrering, med klientdomännamnet bifogat. Bör se ut ungefär som: *användarnamn\@contoso4*

   * **Admin lösenord -** Ange lösenordet för systemkontot för system för arbetsdagsintegrering

   * **Url till klient –** Ange URL:en till slutpunkten för webbtjänster arbetsdag för din klientorganisation. Det här värdet https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resourcesska se ut så här: , där *contoso4* ersätts med rätt klientnamn och *wd3-impl* ersätts med rätt miljösträng (om det behövs).

   * **E-postmeddelande –** Ange din e-postadress och markera kryssrutan "skicka e-post om fel inträffar" .

   * Klicka på knappen **Testa anslutning.** Om anslutningstestet lyckas klickar du på knappen **Spara** högst upp. Om det misslyckas dubbelkollar du att arbetsdags-URL:en och autentiseringsuppgifterna är giltiga i Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Del 2: Konfigurera mappningar av tillbakaskrivningsattribut

I det här avsnittet konfigurerar du hur tillbakaskrivningsattribut flödar från Azure AD till Workday. För närvarande stöder anslutningen endast tillbakaskrivning av e-postadress och användarnamn till Workday.

1. Klicka på Synkronisera Azure Active **Directory-användare till arbetsdag**på fliken Etablering under **Mappningar.**

2. I fältet **Källobjektomfattning** kan du också filtrera, vilka uppsättningar användare i Azure Active Directory ska ha sina e-postadresser skrivna tillbaka till Workday. Standardomfattningen är "alla användare i Azure AD".

3. I avsnittet **Attributmappningar** uppdaterar du matchande ID för att ange attributet i Azure Active Directory där arbets-ID för arbetsdag eller medarbetar-ID lagras. En populär matchningsmetod är att synkronisera Workday worker ID eller anställd ID till extensionAttribute1-15 i Azure AD och sedan använda det här attributet i Azure AD för att matcha användare tillbaka i Workday.

4. Vanligtvis mappar du attributet Azure AD *userPrincipalName* till Workday *UserID-attribut* och mappar attributet Azure *AD-e-post* till attributet Workday *EmailAddress.* Om du vill spara mappningarna klickar du på **Spara** högst upp i avsnittet Attributmappning.

När konfigurationen för attributmappning är klar kan du nu [aktivera och starta tjänsten för användaretablering](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Aktivera och starta etablering av användare

När konfigurationerna för Workday-etableringsappar har slutförts kan du aktivera etableringstjänsten i Azure-portalen.

> [!TIP]
> Som standard när du aktiverar etableringstjänsten initieras etableringsåtgärder för alla användare i omfånget. Om det finns fel i mappnings- eller arbetsdagsdataproblemen kan etableringsjobbet misslyckas och gå in i karantäntillståndet. För att undvika detta, som en bästa praxis, rekommenderar vi att konfigurera **källobjektomfattningsfilter** och testa attributmappningarna med några testanvändare innan du startar fullständig synkronisering för alla användare. När du har kontrollerat att mappningarna fungerar och ger dig önskat resultat kan du antingen ta bort filtret eller gradvis expandera det till att omfatta fler användare.

1. På fliken **Etablering** anger du **etableringsstatusen** **till På**.

2. Klicka på **Spara**.

3. Den här åtgärden startar den första synkroniseringen, vilket kan ta ett varierande antal timmar beroende på hur många användare som finns i Workday-klienten. 

4. Kontrollera när som helst fliken **Granskningsloggar** i Azure-portalen för att se vilka åtgärder etableringstjänsten har utfört. Granskningsloggarna visar alla enskilda synkroniseringshändelser som utförs av etableringstjänsten, till exempel vilka användare som läses ut från Workday och sedan läggs till eller uppdateras i Active Directory. I avsnittet Felsökning finns instruktioner om hur du granskar granskningsloggarna och åtgärdar etableringsfel.

5. När den första synkroniseringen är klar skrivs en granskningssammanfattningsrapport på fliken **Etablering,** som visas nedan.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar

* **Frågor om lösningsfunktioner**
  * [Hur anger lösningen lösenordet för det nya användarkontot i Active Directory när du bearbetar en ny hyra från Workday?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Har lösningen stöd för att skicka e-postmeddelanden efter etableringsoperationer slutförd?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Hur hanterar jag leverans av lösenord för nyanställda och ger säkert en mekanism för att återställa deras lösenord?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Cachelagrar lösningscachen Workday-användarprofiler i Azure AD-molnet eller på etableringsagentlagret?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Stöder lösningen tilldelning av lokala AD-grupper till användaren?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Vilka Workday-API:er används lösningen för att fråga och uppdatera Workday-arbetsprofiler?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Kan jag konfigurera min Workday HCM-klient med två Azure AD-klienter?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Varför användaretableringsappen "Workday to Azure AD" stöds inte om vi har distribuerat Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Hur föreslår jag förbättringar eller begär nya funktioner relaterade till Workday- och Azure AD-integrering?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Frågor om etableringsagent**
  * [Vad är GA-versionen av etableringsagenten?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Hur känner jag till versionen av min etableringsagent?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Skickar Microsoft automatiskt etableringsagentuppdateringar?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Kan jag installera etableringsagenten på samma server som kör Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Hur konfigurerar jag etableringsagenten så att den använder en proxyserver för utgående HTTP-kommunikation?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Hur säkerställer jag att etableringsagenten kan kommunicera med Azure AD-klienten och att inga brandväggar blockerar portar som krävs av agenten?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Hur avregistrerar jag domänen som är associerad med min etableringsagent?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Hur avinstallerar jag etableringsagenten?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Arbetsdag till AD-attributmappning och konfigurationsfrågor**
  * [Hur säkerhetskopierar eller exporterar jag en fungerande kopia av min mappning av attributmappning och schema för arbetsdagsetablering?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Jag har anpassade attribut i Workday och Active Directory. Hur konfigurerar jag lösningen så att den fungerar med mina anpassade attribut?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Kan jag etablera användarens foto från Workday till Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Hur synkroniserar jag mobilnummer från Workday baserat på användarens medgivande för offentlig användning?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Hur formaterar jag visningsnamn i AD baserat på användarens attribut för avdelning/land/stad och hanterar regionala avvikelser?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Hur kan jag använda SelectUniqueValue för att generera unika värden för attributet samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Hur tar jag bort tecken med diakritiska tecken och konverterar dem till vanliga engelska alfabet?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Frågor om lösningsfunktioner

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Hur anger lösningen lösenordet för det nya användarkontot i Active Directory när du bearbetar en ny hyra från Workday?

När den lokala etableringsagenten får en begäran om att skapa ett nytt AD-konto genererar den automatiskt ett komplext slumpmässigt lösenord som utformats för att uppfylla kraven på lösenordskomplexitet som definieras av AD-servern och anger detta på användarobjektet. Det här lösenordet loggas inte någonstans.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Har lösningen stöd för att skicka e-postmeddelanden efter etableringsoperationer slutförd?

Nej, det går inte att skicka e-postmeddelanden efter att ha slutfört etableringsåtgärder i den aktuella versionen.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Hur hanterar jag leverans av lösenord för nyanställda och ger säkert en mekanism för att återställa deras lösenord?

Ett av de sista stegen som ingår i nya AD-kontoetablering är leveransen av det tillfälliga lösenord som tilldelats användarens AD-konto. Många företag använder fortfarande den traditionella metoden att leverera det tillfälliga lösenordet till användarens chef, som sedan lämnar över lösenordet till den nya hyra / kontingenten arbetstagare. Den här processen har ett inneboende säkerhetsfel och det finns ett alternativ för att implementera en bättre metod med Azure AD-funktioner.

Som en del av anställningsprocessen, HR-team brukar köra en bakgrundskontroll och granska mobilnummer av den nya hyra. Med integreringen mellan workday to AD-användare kan du bygga ovanpå detta och distribuera en självbetjäningsanställning av lösenord för användaren dag 1. Detta åstadkoms genom att sprida attributet "Mobilnummer" för den nya hyran från Arbetsdag till AD och sedan från AD till Azure AD med Azure AD Connect. När "Mobilnummer" finns i Azure AD kan du aktivera [SSPR (Self-Service Password Reset)](../authentication/howto-sspr-authenticationdata.md) för användarens konto, så att en nyanställd på dag 1 kan använda det registrerade och verifierade mobilnumret för autentisering.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Cachelagrar lösningscachen Workday-användarprofiler i Azure AD-molnet eller på etableringsagentlagret?

Nej, lösningen har ingen cache med användarprofiler. Azure AD-etableringstjänsten fungerar helt enkelt som en databehandlare, läser data från Arbetsdag och skriver till målet Active Directory eller Azure AD. Se avsnittet [Hantera personuppgifter](#managing-personal-data) för information om användarnas integritet och datalagring.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Stöder lösningen tilldelning av lokala AD-grupper till användaren?

Den här funktionen stöds inte för närvarande. Rekommenderad lösning är att distribuera ett PowerShell-skript som frågar microsoft graph API-slutpunkten för [granskningsloggdata](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta) och använda den för att utlösa scenarier som grupptilldelning. Det här PowerShell-skriptet kan kopplas till en schemaläggare och distribueras på samma ruta som kör etableringsagenten.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Vilka Workday-API:er används lösningen för att fråga och uppdatera Workday-arbetsprofiler?

Lösningen använder för närvarande följande Workday API:er:

* **API-formatet för arbetsdagar för webbtjänster** som används i avsnittet **Administratörsautentiseringsuppgifter** avgör vilken API-version som används för Get_Workers
  * Om URL-formatet är: https://\#\#\#\#\.arbetsdag\.com/ccx/service/tenantName används API v21.1. 
  * Om URL-formatet är: https://\#\#\#\#\.arbetsdag\.com/ccx/service/tenantName/Human\_Resources används API v21.1 
  * Om URL-formatet är:\#\#\#\#\.https://\.\_arbetsdag com/ccx/service/tenantName/Human\# \# \. \# Resources/v används den angivna API-versionen. (Exempel: om v34.0 anges används den.)  
   
* Funktionen för tillbakaskrivning av e-post på arbetsdag använder Maintain_Contact_Information (v26.1) 
* Funktionen för tillbakaskrivning av användarnamn arbetsdag använder Update_Workday_Account (v31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Kan jag konfigurera min Workday HCM-klient med två Azure AD-klienter?

Ja, den här konfigurationen stöds. Här är stegen på hög nivå för att konfigurera det här scenariot:

* Distribuera etableringsagent #1 och registrera den med Azure AD-klientorganisation #1.
* Distribuera etableringsagent #2 och registrera den med Azure AD-klientorganisation #2.
* Baserat på de "underordnade domäner" som varje etableringsagent ska hantera konfigurerar du varje agent med domänerna. En agent kan hantera flera domäner.
* I Azure-portalen konfigurerar du Workday to AD User Provisioning App i varje klient och konfigurerar den med respektive domäner.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Varför användaretableringsappen "Workday to Azure AD" stöds inte om vi har distribuerat Azure AD Connect?

När Azure AD används i hybridläge (där det innehåller en blandning av moln + lokala användare) är det viktigt att ha en tydlig definition av "auktoritetskälla". Vanligtvis kräver hybridscenarier distribution av Azure AD Connect. När Azure AD Connect distribueras är lokalt AD auktoritetskällan. Att introducera Workday to Azure AD-kopplingen i mixen kan leda till en situation där Workday-attributvärden potentiellt kan skriva över de värden som angetts av Azure AD Connect. Därför stöds inte användning av etableringsappen "Arbetsdag till Azure AD" när Azure AD Connect är aktiverat. I sådana situationer rekommenderar vi att du använder etableringsappen "Arbetsdag till AD-användare" för att få användare till lokala AD och sedan synkronisera dem till Azure AD med Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Hur föreslår jag förbättringar eller begär nya funktioner relaterade till Workday- och Azure AD-integrering?

Din feedback värderas högt eftersom det hjälper oss att ange riktning för framtida utgåvor och förbättringar. Vi välkomnar all feedback och uppmuntrar dig att skicka in din idé eller förbättringsförslag i [feedbackforumet](https://feedback.azure.com/forums/169401-azure-active-directory)i Azure AD . För specifik feedback relaterad till Workday-integreringen väljer du kategorin *SaaS-program* och söker med nyckelorden *Arbetsdag* för att hitta befintlig feedback relaterad till arbetsdagen.

![AnvändareVoice SaaS-appar](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![AnvändareVoice arbetsdag](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

När du föreslår en ny idé, kontrollera om någon annan redan har föreslagit en liknande funktion. I så fall kan du upp rösta funktionen eller förbättring begäran. Du kan också lämna en kommentar om ditt specifika användningsfall för att visa ditt stöd för idén och visa hur funktionen kommer att vara värdefull för dig också.

### <a name="provisioning-agent-questions"></a>Frågor om etableringsagent

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Vad är GA-versionen av etableringsagenten?

* GA-versionen av etableringsagenten är 1.1.30 och högre.
* Om agentversionen är mindre än 1.1.30 kör du den offentliga förhandsversionen och den uppdateras automatiskt till GA-versionen om servern som är värd för agenten har .NET 4.7.1-körningen.
  * Du kan [kontrollera .NET-versionen](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) som är installerad på servern. Om servern inte kör .NET 4.7.1 kan du [hämta och installera .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). Etableringsagenten uppdateras automatiskt till GA-versionen när du har installerat .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Hur känner jag till versionen av min etableringsagent?

* Logga in på Windows-servern där etableringsagenten är installerad.
* Gå till**Menyn Avinstallera eller ändra en programmeny på** **Kontrollpanelen** -> 
* Leta efter den version som motsvarar posten **Microsoft Azure AD Connect Provisioning Agent**

  ![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Skickar Microsoft automatiskt etableringsagentuppdateringar?

Ja, Microsoft uppdaterar automatiskt etableringsagenten. Du kan inaktivera automatiska uppdateringar genom att stoppa Windows-tjänsten **Microsoft Azure AD Connect Agent Updater**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Kan jag installera etableringsagenten på samma server som kör Azure AD Connect?

Ja, du kan installera etableringsagenten på samma server som kör Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Vid tidpunkten för konfigurationen frågar etableringsagenten efter Azure AD-administratörsautentiseringsuppgifter. Lagrar agenten autentiseringsuppgifterna lokalt på servern?

Under konfigurationen frågar etableringsagenten endast för Azure AD-administratörsautentiseringsuppgifter för att ansluta till din Azure AD-klientorganisation. Autentiseringsuppgifterna lagras inte lokalt på servern. Men det behåller de autentiseringsuppgifter som används för att ansluta till den *lokala Active Directory-domänen* i ett lokalt Windows-lösenordsvalv.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Hur konfigurerar jag etableringsagenten så att den använder en proxyserver för utgående HTTP-kommunikation?

Etableringsagenten stöder användning av utgående proxy. Du kan konfigurera den genom att redigera agentkonfigurationsfilen **C:\Program\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Lägg till följande rader i den, mot slutet `</configuration>` av filen strax före den avslutande taggen.
Ersätt variablerna [proxy-server] och [proxyport] med proxyserverns namn och portvärden.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Hur säkerställer jag att etableringsagenten kan kommunicera med Azure AD-klienten och att inga brandväggar blockerar portar som krävs av agenten?

Du kan också kontrollera om du har alla nödvändiga portar öppna genom att öppna [testverktyget för anslutningsportar](https://aadap-portcheck.connectorporttest.msappproxy.net/) från det lokala nätverket. Mer gröna bockar innebär större återhämtning.

Se till att verktyget ger dig rätt resultat:

* Öppna verktyget i en webbläsare från servern där du har installerat etableringsagenten.
* Se till att alla proxyservrar eller brandväggar som gäller för din etableringsagent också tillämpas på den här sidan. Detta kan göras i Internet Explorer genom att gå till **Inställningar -> Internet-alternativ -> anslutningar -> LAN-inställningar**. På den här sidan visas fältet "Använd en proxyserver för ditt LAN". Markera den här rutan och placera proxyadressen i fältet "Adress".

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Kan en etableringsagent konfigureras för att etablera flera AD-domäner?

Ja, en etableringsagent kan konfigureras för att hantera flera AD-domäner så länge agenten har siktlinje till respektive domänkontrollanter. Microsoft rekommenderar att du konfigurerar en grupp med tre etableringsagenter som betjänar samma uppsättning AD-domäner för att säkerställa hög tillgänglighet och ge stöd för felväxling.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Hur avregistrerar jag domänen som är associerad med min etableringsagent?

* Hämta *klient-ID:et* för din Azure AD-klient från Azure-portalen.
* Logga in på Windows-servern som kör etableringsagenten.
* Öppna PowerShell som Windows-administratör.
* Ändra till katalogen som innehåller registreringsskripten och \[kör\] följande kommandon som ersätter parametern klient-ID med värdet för ditt klient-ID.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Kopiera värdet för fältet från den resurs `id` vars *resursNamn är* lika med ad-domännamnet i listan över agenter som visas.
* Klistra in ID-värdet i det här kommandot och kör kommandot i PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Kör konfigurationsguiden för agenten igen.
* Alla andra agenter som tidigare har tilldelats den här domänen måste konfigureras om.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Hur avinstallerar jag etableringsagenten?

* Logga in på Windows-servern där etableringsagenten är installerad.
* Gå till**Menyn Avinstallera eller ändra en programmeny på** **Kontrollpanelen** -> 
* Avinstallera följande program:
  * Etableringsagent för Microsoft Azure AD Connect
  * Uppdatering av Microsoft Azure AD Connect-agent
  * Etableringsagentpaket för Microsoft Azure AD Connect

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Arbetsdag till AD-attributmappning och konfigurationsfrågor

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Hur säkerhetskopierar eller exporterar jag en fungerande kopia av min mappning av attributmappning och schema för arbetsdagsetablering?

Du kan använda Microsoft Graph API för att exportera konfigurationen för etablering av arbetsdag. Mer information finns i avsnittet Exportera och importera konfigurationen för användares etablering av [arbetsdagsanvändare.](#exporting-and-importing-your-configuration)

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Jag har anpassade attribut i Workday och Active Directory. Hur konfigurerar jag lösningen så att den fungerar med mina anpassade attribut?

Lösningen stöder anpassade Workday- och Active Directory-attribut. Om du vill lägga till anpassade attribut i mappningsschemat öppnar du bladet **Attributmappning** och rullar nedåt för att expandera avsnittet **Visa avancerade alternativ**. 

![Redigera attributlista](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Om du vill lägga till dina anpassade Workday-attribut markerar du alternativet *Redigera attributlista för Workday* och för att lägga till dina anpassade AD-attribut väljer du alternativet *Redigera attributlista för Lokal Active Directory*.

Se även:

* [Anpassa listan över användarattribut för Arbetsdag](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Hur konfigurerar jag lösningen för att bara uppdatera attribut i AD baserat på Workday-ändringar och inte skapa några nya AD-konton?

Den här konfigurationen kan uppnås genom att ställa in **målobjektåtgärder** i bladet **Attributmappningar** enligt nedan:

![Uppdatera åtgärd](./media/workday-inbound-tutorial/wd_target_update_only.png)

Markera kryssrutan "Uppdatera" för endast uppdateringsåtgärder som ska flöda från arbetsdag till AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Kan jag etablera användarens foto från Workday till Active Directory?

Lösningen stöder för närvarande inte att ange binära attribut som *thumbnailPhoto* och *jpegPhoto* i Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Hur synkroniserar jag mobilnummer från Workday baserat på användarens medgivande för offentlig användning?

* Gå till bladet "Etablering" i din workday-etableringsapp.
* Klicka på attributmappningarna 
* Under **Mappningar**väljer du **Synkronisera workday arbetare till Lokal Active Directory** (eller Synkronisera **workday arbetare till Azure AD**).
* På sidan Attributmappningar bläddrar du nedåt och markerar rutan "Visa avancerade alternativ".  Klicka på **Redigera attributlista för Workday**
* I bladet som öppnas letar du reda på attributet "Mobil" och klickar på raden så att du kan redigera **API Expression** ![Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Ersätt **API-uttrycket** med följande nya uttryck, som hämtar arbetsmobilnumret endast om "Flaggan för offentlig användning" är inställd på "True" i Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Spara attributlistan.
* Spara attributmappningen.
* Rensa aktuellt tillstånd och starta om den fullständiga synkroniseringen.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Hur formaterar jag visningsnamn i AD baserat på användarens attribut för avdelning/land/stad och hanterar regionala avvikelser?

Det är ett vanligt krav att konfigurera *attributet displayName* i AD så att det också ger information om användarens avdelning och land/region. För t.ex. om John Smith arbetar på marknadsavdelningen i USA, kanske du vill att hans *displayName* ska visas som *Smith, John (Marketing-US)*.

Så här kan du hantera sådana krav för att konstruera *CN* eller *displayName* så att attribut som företag, affärsenhet, ort eller land/region.

* Varje Workday-attribut hämtas med ett underliggande XPATH API-uttryck, som kan konfigureras i **attributmappning -> Avancerat avsnitt -> Redigera attributlista för Workday**. Här är standard-XPATH API-uttryck för Workday *PreferredFirstName*, *PreferredLastName*, *Företag* och *SupervisoryOrganization* attribut.

     | Attribut för arbetsdag | API XPATH-uttryck |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Företag | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | TillsynOrganisering | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |
  
   Bekräfta med ditt Workday-team att API-uttrycket ovan är giltigt för din Workday-klientkonfiguration. Om det behövs kan du redigera dem enligt beskrivningen i avsnittet [Anpassa listan över användarattribut för Arbetsdag](#customizing-the-list-of-workday-user-attributes).

* På samma sätt hämtas landsinformationen i Workday med följande XPATH: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Det finns fem landsrelaterade attribut som är tillgängliga i avsnittet Workday-attributlista.

     | Attribut för arbetsdag | API XPATH-uttryck |
     | ----------------- | -------------------- |
     | Landsreferens | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceVänligt | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | LandRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Bekräfta med ditt Workday-team att API-uttrycken ovan är giltiga för din Workday-klientkonfiguration. Om det behövs kan du redigera dem enligt beskrivningen i avsnittet [Anpassa listan över användarattribut för Arbetsdag](#customizing-the-list-of-workday-user-attributes).

* Om du vill skapa rätt attributmappningsuttryck identifierar du vilket Workday-attribut "auktoritärt" som representerar användarens förnamn, efternamn, land/region och avdelning. Anta att attributen är *PreferredFirstName,* *PreferredLastName*, *CountryReferenceTwoLetter* respektive *SupervisoryOrganization.* Du kan använda detta för att skapa ett uttryck för ATTRIBUTEt AD *displayName* på följande sätt för att få ett visningsnamn som *Smith, John (Marketing-US).*

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    När du har rätt uttryck redigerar du tabellen Attributmappningar och ändrar *attributmappningen displayName* enligt nedan: ![DisplayName Mapping](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Utöka ovanstående exempel, låt oss säga att du vill konvertera stadsnamn som kommer från Workday till stenografi värden och sedan använda den för att bygga visningsnamn som *Smith, John (CHI)* eller *Doe, Jane (NYC)*, då detta resultat kan uppnås med hjälp av en Switch uttryck med Workday *Kommun* attribut som avgörande variabel.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Se även:
  * [Syntax för växla funktion](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Syntax för funktionen Koppla](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Syntax för lägg till funktion](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Hur kan jag använda SelectUniqueValue för att generera unika värden för attributet samAccountName?

Anta att du vill generera unika värden för *attributet samAccountName* med hjälp av en kombination av attribut *för förnamn* och *efternamn* från Workday. Nedan anges ett uttryck som du kan börja med:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Hur ovanstående uttryck fungerar: Om användaren är John Smith, försöker det först att generera JSmith, om JSmith redan finns, då det genererar JoSmith, om det finns, genererar det JohSmith. Uttrycket säkerställer också att det värde som genereras uppfyller längdbegränsningen och begränsningen för specialtecken som är associerad med *samAccountName*.

Se även:

* [Syntax för mellanfunktion](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Syntax för ersätta funktionen](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [Syntax för funktionen Väljoutvärde](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Hur tar jag bort tecken med diakritiska tecken och konverterar dem till vanliga engelska alfabet?

Använd funktionen [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) för att ta bort specialtecken i användarens för- och efternamn, samtidigt som e-postadressen eller CN-värdet för användaren skapas.

## <a name="troubleshooting-tips"></a>Felsökningstips

Det här avsnittet innehåller särskilda riktlinjer för hur du felsöker etableringsproblem med din Workday-integrering med hjälp av Azure AD-granskningsloggarna och Loggarna för Windows Server Loggboken. Den bygger på de allmänna felsökningsstegen och begreppen som samlas in i [självstudien: Rapportering om automatisk etablering](../app-provisioning/check-status-user-account-provisioning.md) av användarkonton

Det här avsnittet innehåller följande aspekter av felsökning:

* [Konfigurera Windows Loggboken för agentfelsökning](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Konfigurera Granskningsloggar för Azure Portal Audit för felsökning av tjänster](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Förstå loggar för AD-användarkonto skapa åtgärder](#understanding-logs-for-ad-user-account-create-operations)
* [Förstå loggar för hanterarens uppdateringsåtgärder](#understanding-logs-for-manager-update-operations)
* [Lösa vanliga fel påträffades](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Konfigurera Windows Loggboken för agentfelsökning

* Logga in på Windows Server-datorn där etableringsagenten distribueras
* Öppna skrivbordsappen **Windows Server Loggboken.**
* Välj **Windows-loggar > program**.
* Använd **den aktuella filterloggen...** för att visa alla händelser som loggas under källan **AAD. Connect.ProvisioningAgent** och utesluta händelser med händelse-ID "5", genom att ange filtret "-5" som visas nedan.

  ![Loggboken för Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Klicka på **OK** och sortera resultatvyn efter **datum- och tidskolumn.**

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Konfigurera Granskningsloggar för Azure Portal Audit för felsökning av tjänster

* Starta [Azure-portalen](https://portal.azure.com)och navigera till avsnittet **Granskningsloggar** i ditt workday-etableringsprogram.
* Använd knappen **Kolumner** på sidan Granskningsloggar om du bara vill visa följande kolumner i vyn (Datum, Aktivitet, Status, Statusorsak). Den här konfigurationen säkerställer att du bara fokuserar på data som är relevanta för felsökning.

  ![Kolumner för granskningslogg](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Använd frågeparametrerna **Mål** **och datumintervall** för att filtrera vyn. 
  * Ange parametern **Målfråga** till arbetar-ID:t "Worker ID" eller "Employee ID" för workday-arbetarobjektet.
  * Ange **datumintervallet** till en lämplig tidsperiod som du vill undersöka för fel eller problem med etableringen.

  ![Filter för granskningslogg](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Förstå loggar för AD-användarkonto skapa åtgärder

När en ny anställning i Workday identifieras (låt oss säga med Employee ID *21023*) försöker Azure AD-etableringstjänsten skapa ett nytt AD-användarkonto för arbetaren och skapar i processen 4 granskningsloggposter enligt beskrivningen nedan:

  [![Granskningslogg skapar ops](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

När du klickar på någon av granskningsloggposterna öppnas sidan **Aktivitetsinformation.** Här är vad sidan **Aktivitetsinformation** visar för varje loggposttyp.

* **Post för import av arbetsdag:** Den här loggposten visar arbetarinformationen som hämtats från Workday. Använd information i avsnittet *Ytterligare information* i loggposten för att felsöka problem med att hämta data från Workday. En exempelpost visas nedan tillsammans med pekare om hur varje fält ska tolkas.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **AD Import** post: Den här loggposten visar information om kontot som hämtats från AD. Precis som när det först skapades ett AD-konto anger *aktivitetsstatusorsaken* att inget konto med attributet Matchning ID hittades i Active Directory. Använd information i avsnittet *Ytterligare information* i loggposten för att felsöka problem med att hämta data från Workday. En exempelpost visas nedan tillsammans med pekare om hur varje fält ska tolkas.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Om du vill söka efter etableringsagentloggposter som motsvarar den här AD-importåtgärden öppnar du Loggarna för Windows Loggboken och använder **sök...** menyalternativ för att hitta loggposter som innehåller attributvärdet Matchning id/anslutningsegenskaper (i det här fallet *21023*).

  ![Hitta](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Leta efter posten med *Händelse-ID = 9*, som ger dig LDAP-sökfiltret som används av agenten för att hämta AD-kontot. Du kan kontrollera om det här är rätt sökfilter för att hämta unika användarposter.

  ![LDAP-sökning](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Posten som omedelbart följer den med *händelse-ID = 2* fångar resultatet av sökåtgärden och om den returnerade några resultat.

  ![LDAP-resultat](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Åtgärdspost för synkroniseringsregel:** Den här loggposten visar resultatet av attributmappningsreglerna och konfigurerade omfångsfilter tillsammans med den etableringsåtgärd som ska vidtas för att bearbeta den inkommande workday-händelsen. Använd information i avsnittet *Ytterligare information* i loggposten för att felsöka problem med synkroniseringsåtgärden. En exempelpost visas nedan tillsammans med pekare om hur varje fält ska tolkas.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Om det finns problem med attributmappningsuttrycken eller om inkommande Workday-data har problem (till exempel: tomt eller null-värde för obligatoriska attribut), kommer du att observera ett fel i det här skedet med ErrorCode som anger information om felet.

* **AD-exportpost:** Den här loggposten visar resultatet av ad-kontoskapande åtgärd tillsammans med de attributvärden som angavs i processen. Använd information i avsnittet *Ytterligare information* i loggposten för att felsöka problem med åtgärden för att skapa konton. En exempelpost visas nedan tillsammans med pekare om hur varje fält ska tolkas. I avsnittet "Ytterligare information" är "EventName" inställt på "EntryExportAdd", "JoiningProperty" är inställt på värdet för attributet Matching ID, "SourceAnchor" är inställt på WorkdayID (WID) som är associerad med posten och "TargetAnchor" är inställt på värdet för AD "ObjectGuid"-attributet för den nyskapade användaren. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Om du vill söka efter etableringsagentloggposter som motsvarar den här AD-exportåtgärden öppnar du Loggarna för Windows Loggboken och använder **sök...** menyalternativ för att hitta loggposter som innehåller attributvärdet Matchning id/anslutningsegenskaper (i det här fallet *21023*).  

  Leta efter en HTTP POST-post som motsvarar tidsstämpeln för exportåtgärden med *händelse-ID = 2*. Den här posten innehåller attributvärdena som skickas av etableringstjänsten till etableringsagenten.

  [![SCIM Lägg till](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Omedelbart efter händelsen ovan bör det finnas en annan händelse som fångar svaret från åtgärden skapa AD-konto. Den här händelsen returnerar det nya objektetGuid som skapats i AD och det anges som TargetAnchor-attributet i etableringstjänsten.

  [![SCIM Lägg till](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Förstå loggar för uppdateringar av chefer

Manager-attributet är ett referensattribut i AD. Etableringstjänsten anger inte förvaltarattributet som en del av åtgärden för att skapa användare. Hanterarens attribut anges snarare som en del av en *uppdateringsåtgärd* när AD-kontot har skapats för användaren. Utöka exemplet ovan, låt oss säga att en nyanställd med employee-ID "21451" aktiveras i Workday och den nyanställdas chef (*21023*) redan har ett AD-konto. I det här fallet söker granskningsloggar för användare 21451 visar upp 5 poster.

  [![Uppdatering av chef](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

De första 4 posterna är som de vi utforskade som en del av användaren skapa operation. Den 5:e posten är exporten som är associerad med uppdateringen av förvaltarattribut. Loggposten visar resultatet av AD-kontohanterarens uppdateringsåtgärd, som utförs med hjälp av chefens *objectGuid-attribut.*

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Lösa vanliga fel påträffades

Det här avsnittet innehåller vanliga fel med workday-användaretablering och hur du löser det. Felen grupperas på följande sätt:

* [Etableringsagentfel](#provisioning-agent-errors)
* [Anslutningsfel](#connectivity-errors)
* [AD-användarkonton skapar fel](#ad-user-account-creation-errors)
* [AD-användarkontouppdateringsfel](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Etableringsagentfel

|#|Felscenario |Sannolika orsaker|Rekommenderad upplösning|
|--|---|---|---|
|1.| Det gick inte att installera etableringsagenten med felmeddelande: *Tjänsten "Microsoft Azure AD Connect Provisioning Agent" (AADConnectProvisioningAgent) kunde inte startas. Kontrollera att du har tillräcklig behörighet för att starta systemet.* | Det här felet visas vanligtvis om du försöker installera etableringsagenten på en domänkontrollant och grupprincipen hindrar tjänsten från att starta.  Det ses också om du har en tidigare version av agenten igång och du inte har avinstallerat den innan du startar en ny installation.| Installera etableringsagenten på en icke-DC-server. Kontrollera att tidigare versioner av agenten avinstalleras innan du installerar den nya agenten.|
|2.| Windows-tjänsten Microsoft Azure AD Connect-etableringsagenten är i *startläge* och växlar inte till *körtillstånd.* | Som en del av installationen skapar agentguiden ett lokalt konto **(NT-tjänst\\AADConnectProvisioningAgent**) på servern och det här är inloggningskontot som används för att starta tjänsten. Om en säkerhetsprincip på Windows-servern hindrar lokala konton från att köra tjänsterna uppstår det här felet. | Öppna *konsolen Tjänster*. Högerklicka på Windows-tjänsten "Microsoft Azure AD Connect Provisioning Agent" och ange kontot för en domänadministratör för att köra tjänsten på inloggningsfliken. Starta om tjänsten. |
|3.| När du konfigurerar etableringsagenten med AD-domänen i steget *Anslut Active Directory*tar guiden lång tid på att försöka läsa in AD-schemat och så småningom time out. | Det här felet visas vanligtvis om det inte går att kontakta AD-domänkontrollantservern på grund av problem med brandväggen. | På skärmen *Anslut Active Directory* finns det ett alternativ som kallas *Välj domänkontrollantprioritet*. Använd det här alternativet om du vill välja en domänkontrollant som finns på samma plats som agentservern och se till att det inte finns några brandväggsregler som blockerar kommunikationen. |

#### <a name="connectivity-errors"></a>Anslutningsfel

Om etableringstjänsten inte kan ansluta till Workday eller Active Directory kan etableringen hamna i ett karantäntillstånd. Använd tabellen nedan för att felsöka anslutningsproblem.

|#|Felscenario |Sannolika orsaker|Rekommenderad upplösning|
|--|---|---|---|
|1.| När du klickar på **Testa anslutning**visas felmeddelandet: *Det uppstod ett fel när du anslöt till Active Directory. Kontrollera att den lokala etableringsagenten körs och att den är konfigurerad med rätt Active Directory-domän.* | Det här felet visas vanligtvis om etableringsagenten inte körs eller om det finns en brandvägg som blockerar kommunikationen mellan Azure AD och etableringsagenten. Det här felet kan också visas om domänen inte är konfigurerad i agentguiden. | Öppna *tjänstekonsolen* på Windows-servern för att bekräfta att agenten körs. Öppna etableringsagentguiden och bekräfta att rätt domän är registrerad hos agenten.  |
|2.| Etableringsjobbet går i karantäntillstånd under helgerna (fre-lör) och vi får ett e-postmeddelande om att det finns ett fel med synkroniseringen. | En av de vanligaste orsakerna till felet är planerade Workday-driftstopp. Om du använder en klient för implementering av Workday ska du notera att Workday har schemalagt driftstopp för sina implementeringsklienter under helger (vanligtvis från fredag kväll till lördag morgon). Under den perioden kan Workday-etableringsappar försättas i karantäntillstånd eftersom det inte går att ansluta till Workday. Workday återfår sitt normala tillstånd när Workday-implementeringsklienten är online igen. I sällsynta fall kan du också se det här felet om lösenordet för integreringssystemanvändaren har ändrats på grund av uppdatering av klienten eller om kontot är låst eller har upphört att gälla. | Kontakta din Workday-administratör eller integreringspartner för att höra efter när Workday schemalägger driftstopp. Sedan kan du ignorera varningsmeddelanden under driftstoppsperioden och få en bekräftelse om tillgänglighet när Workday-instansen är online igen.  |


#### <a name="ad-user-account-creation-errors"></a>AD-användarkonton skapar fel

|#|Felscenario |Sannolika orsaker|Rekommenderad upplösning|
|--|---|---|---|
|1.| Exportåtgärder misslyckades i granskningsloggen med meddelandet *Fel: OperationsError-SvcErr: Ett åtgärdsfel uppstod. Ingen överlägsen referens har konfigurerats för katalogtjänsten. Katalogtjänsten kan därför inte utfärda värvningar till objekt utanför den här skogen.* | Det här felet visas vanligtvis om *Active Directory-behållarens* organisationsenhet inte är korrekt inställd eller om det finns problem med uttrycksmappningen som används för *parentDistinguishedName*. | Kontrollera om det finns stavfel i active *directory-behållarens* ou-parameter. Om du använder *parentDistinguishedName* i attributmappningen kontrollerar du att det alltid utvärderas till en känd container i AD-domänen. Kontrollera *exporthändelsen* i granskningsloggarna för att se det genererade värdet. |
|2.| Exportåtgärdsfel i granskningsloggen med felkod: *SystemForCrossDomainIdentityManagementBadResponse* och *meddelandefel: ConstraintViolation-AtrErr: Ett värde i begäran är ogiltigt. Ett värde för attributet fanns inte i det acceptabla värdeintervallet. \nError Detaljer: CONSTRAINT_ATT_TYPE - företag*. | Även om det här *company* felet är specifikt för företagsattributet kan det här felet visas även för andra attribut som *CN.* Det här felet visas på grund av AD-tillämpad schemabegränsning. Som standard har attribut som *företag* och *CN* i AD en övre gräns på 64 tecken. Om värdet som kommer från Workday är fler än 64 tecken visas det här felmeddelandet. | Kontrollera *exporthändelsen* i granskningsloggarna för att se värdet för attributet som rapporteras i felmeddelandet. Överväg att trunkera värdet som kommer från Workday med funktionen [Mitt](../app-provisioning/functions-for-customizing-application-data.md#mid) eller ändra mappningarna till ett AD-attribut som inte har liknande längdbegränsningar.  |

#### <a name="ad-user-account-update-errors"></a>AD-användarkontouppdateringsfel

Under AD-uppdateringsprocessen för användarkonto läser etableringstjänsten information från både Workday och AD, kör attributmappningsreglerna och avgör om någon ändring behöver börja gälla. Följaktligen utlöses en uppdateringshändelse. Om något av dessa steg stöter på ett fel loggas det i granskningsloggarna. Använd tabellen nedan för att felsöka vanliga uppdateringsfel.

|#|Felscenario |Sannolika orsaker|Rekommenderad upplösning|
|--|---|---|---|
|1.| Åtgärdsfel för synkroniseringsregel i granskningsloggen med meddelandet *EventName = EntrySynchronizationError och ErrorCode = EndpointUnavailable*. | Det här felet visas om etableringstjänsten inte kan hämta användarprofildata från Active Directory på grund av ett bearbetningsfel som påträffades av den lokala etableringsagenten. | Kontrollera loggerna för etableringsagentens loggbok för felhändelser som anger problem med läsåtgärden (Filter efter händelse-ID #2). |
|2.| Manager-attributet i AD uppdateras inte för vissa användare i AD. | Den troligaste orsaken till det här felet är om du använder omfångsregler och användarens chef inte ingår i omfånget. Du kan också stöta på det här problemet om chefens matchande ID-attribut (t.ex. EmployeeID) inte hittas i AD-planens måldomän eller inte är inställt på rätt värde. | Granska omfångsfiltret och lägg till hanterarens användare i omfånget. Kontrollera chefens profil i AD för att se till att det finns ett värde för det matchande ID-attributet. |

## <a name="managing-your-configuration"></a>Hantera konfigurationen

I det här avsnittet beskrivs hur du kan utöka, anpassa och hantera konfigurationen för arbetsdagsdriven användaretablering. Den täcker följande ämnen:

* [Anpassa listan över användarattribut för Arbetsdag](#customizing-the-list-of-workday-user-attributes)  
* [Exportera och importera konfigurationen](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Anpassa listan över användarattribut för Arbetsdag

Arbetsdagsetableringsapparna för Active Directory och Azure AD innehåller båda en standardlista över användarattribut för Arbetsdag som du kan välja mellan. Dessa förteckningar är dock inte heltäckande. Workday stöder många hundratals möjliga användarattribut, som antingen kan vara standard eller unika för din Workday-klient.

Azure AD-etableringstjänsten stöder möjligheten att anpassa din lista eller Workday-attribut för att inkludera alla attribut som exponeras i [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) driften av PERSONAL-API:et.

För att göra den här ändringen måste du använda [Workday Studio](https://community.workday.com/studio-download) för att extrahera XPath-uttrycken som representerar de attribut du vill använda och sedan lägga till dem i etableringskonfigurationen med hjälp av den avancerade attributredigeraren i Azure-portalen.

**Så här hämtar du ett XPath-uttryck för ett Workday-användarattribut:**

1. Ladda ner och installera [Workday Studio](https://community.workday.com/studio-download). Du behöver ett Community-konto på Workday för att komma åt installationsprogrammet.

2. Ladda ner workday-filen Human_Resources WSDL från den här webbadressen:https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Starta Workday Studio.

4. Välj alternativet **Arbetsdag > Testa webbtjänst i Test i Testfältet.**

5. Välj **Extern**och välj den Human_Resources WSDL-fil som du hämtade i steg 2.

    ![Arbetsdag Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Ange **fältet** Plats `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`till , men ersätter "IMPL-CC" med din faktiska instanstyp och "TENANT" med ditt riktiga klientnamn.

7. Ange **åtgärd** till **Get_Workers**

8.  Klicka på den lilla **konfigurera länken** under fönstret Begäran/svar för att ange dina Workday-autentiseringsuppgifter. Kontrollera **autentisering**och ange sedan användarnamn och lösenord för ditt systemkonto för workday-integrering. Var noga med att formatera\@användarnamnet som namnklient och lämna alternativet **WS-Security UsernameToken** markerat.

    ![Arbetsdag Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Välj **OK**.

10. I fönstret **Begär** klistrar du in XML-koden nedan och anger **Employee_ID** till en riktig användares medarbetar-ID i din Workday-klient. Välj en användare som har attributet fyllt i som du vill extrahera.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Klicka på **skicka begäran** (grön pil) för att köra kommandot. Om det lyckas ska svaret visas i **fönstret Svar.** Kontrollera svaret för att se till att det har data för användar-ID du angav, och inte ett fel.

12. Om det lyckas kopierar du XML-koden från **svarsfönstret** och sparar den som en XML-fil.

13. I kommandofältet i Workday Studio väljer du **Arkiv > Öppna fil...** och öppnar XML-filen som du sparade. Den här åtgärden öppnar filen i WORKday Studio XML-redigeraren.

    ![Arbetsdag Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. I filträdet navigerar du genom **/env: Kuvert > vv: Brödtext > wd:Get_Workers_Response > wd:Response_Data > wd: Arbetaren** för att hitta användarens data.

15. Under **wd: Worker**hittar du det attribut som du vill lägga till och väljer det.

16. Kopiera XPath-uttrycket för det markerade attributet i fältet **Dokumentsökväg.**

17. Ta bort **prefixet /env:Kuvert/v:Brödtext/wd:Get_Workers_Response/wd:Response_Data/** från det kopierade uttrycket.

18. Om det sista objektet i det kopierade uttrycket är en nod (exempel: "/wd: Birth_Date" läggs du till **/text()** i slutet av uttrycket. Detta är inte nödvändigt om det sista objektet/@wd: är ett attribut (exempel: "typ").

19. Resultatet bör vara `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`något i stil med . Det här värdet är vad du kopierar till Azure-portalen.

**Så här lägger du till ditt anpassade Workday-användarattribut i etableringskonfigurationen:**

1. Starta [Azure-portalen](https://portal.azure.com)och navigera till etableringsavsnittet i ditt Workday-etableringsprogram, som beskrivs tidigare i den här självstudien.

2. Ange **etableringsstatus** till **Av**och välj **Spara**. Det här steget hjälper dig att se till att ändringarna träder i kraft först när du är redo.

3. Under **Mappningar**väljer du **Synkronisera workday arbetare till Lokal Active Directory** (eller Synkronisera **workday arbetare till Azure AD**).

4. Bläddra längst ned på nästa skärm och välj **Visa avancerade alternativ**.

5. Välj **Redigera attributlista för Workday**.

    ![Arbetsdag Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Bläddra längst ned i attributlistan till den plats där inmatningsfälten finns.

7. För **Namn**anger du ett visningsnamn för attributet.

8. För **Typ**väljer du typ som motsvarar attributet **(Sträng** är vanligast).

9. För **API-uttryck**anger du det XPath-uttryck som du kopierade från Workday Studio. Exempel: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Välj **Lägg till attribut**.

    ![Arbetsdag Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Välj **Spara** ovan och sedan **Ja** i dialogrutan. Stäng skärmen Attributmappning om den fortfarande är öppen.

12. Tillbaka på **huvudanableringsfliken** väljer du **Synkronisera workday arbetare till Lokal Active Directory** (eller Synkronisera arbetare till Azure **AD**) igen.

13. Välj **Lägg till ny mappning**.

14. Det nya attributet ska nu visas i **attributlistan Källa.**

15. Lägg till en mappning för det nya attributet som du vill.

16. Kom ihåg att ange **etableringsstatus** igen till **På** och spara.

### <a name="exporting-and-importing-your-configuration"></a>Exportera och importera konfigurationen

Se artikeln [Exportera och importera etableringskonfiguration](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Hantera personliga data

Lösning för arbetsdagsetablering för Active Directory kräver att en etableringsagent installeras på en lokal Windows-server, och den här agenten skapar loggar i Windows-händelseloggen som kan innehålla personuppgifter beroende på din arbetsdag till AD-attributmappningar. För att uppfylla användarnas sekretesskrav kan du se till att inga data lagras i händelseloggarna efter 48 timmar genom att konfigurera en schemalagd Windows-aktivitet för att rensa händelseloggen.

Azure AD-etableringstjänsten hör till **dataprocessorkategorin** för GDPR-klassificering. Som dataprocessorpipeline tillhandahåller tjänsten databehandlingstjänster till viktiga partner och slutkonsumenter. Azure AD-etableringstjänsten genererar inte användardata och har ingen oberoende kontroll över vilka personuppgifter som samlas in och hur de används. Datahämtning, aggregering, analys och rapportering i Azure AD-etableringstjänst baseras på befintliga företagsdata.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

När det gäller datalagring genererar azure AD-etableringstjänsten inte rapporter, utför analyser eller ger insikter efter 30 dagar. Därför lagrar, bearbetar eller behåller azure AD-etableringstjänsten inte data efter 30 dagar. Den här designen är kompatibel med GDPR-reglerna, Microsofts sekretessefterlevnadsregler och Azure AD-datalagringsprinciper.

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan Workday och Azure Active Directory](workday-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)
* [Lär dig hur du använder Microsoft Graph API:er för att hantera etableringskonfigurationer](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
