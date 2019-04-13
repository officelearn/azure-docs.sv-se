---
title: 'Självstudier: Konfigurera Workday för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Workday.
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
ms.date: 01/19/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: d34bd9d7f80f72b3c6c0821ad48e6be1fd260be9
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524641"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Workday för automatisk användaretablering

Målet med den här självstudien är att visa steg du måste utföra för att importera worker profiler från Workday till både Active Directory och Azure Active Directory, med valfritt tillbakaskrivning av e-postadress till Workday.

## <a name="overview"></a>Översikt

Den [tjänst Azure Active Directory för användaretablering](../manage-apps/user-provisioning.md) kan integreras med den [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) för att etablera användarkonton. Azure AD använder den här anslutningen för att aktivera etablering arbetsflöden följande användare:

* **Etablera användare i Active Directory** -etablera valda uppsättningar med användare från Workday till en eller flera Active Directory-domäner.

* **Etablera endast molnbaserade användare till Azure Active Directory** – i scenarier där lokala Active Directory inte används, användare kan etableras direkt från Workday till Azure Active Directory med Azure AD-tjänst för användaretablering.

* **Skriva baksidan av e-postadresser till Workday** -Azure AD-tjänst för användaretablering kan skriva e-postadresserna för Azure AD-användare tillbaka till Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Vilka personalfrågor scenarier täcker den?

Workday användaren etablering arbetsflöden stöds av etableringstjänsten för Azure AD-användare aktivera automatisering av följande personal och scenarier för hantering av identitet livscykel:

* **Anställa nyanställda** – när en ny medarbetare har lagts till i Workday, skapas automatiskt ett användarkonto i Active Directory, Azure Active Directory och du kan också Office 365 och [andra SaaS-program som stöds av Azure AD](../manage-apps/user-provisioning.md), med återskrivning e-postadress till Workday.

* **Medarbetaren attribut och profilen uppdateringar** – när en medarbetarpost uppdateras i Workday (till exempel deras namn, avdelning eller manager), användarkontot uppdateras automatiskt i Active Directory, Azure Active Directory och du kan också Office 365 och [andra SaaS-program som stöds av Azure AD](../manage-apps/user-provisioning.md).

* **Medarbetaren uppsägningar** – när en anställd avslutas i Workday, användarkontot inaktiveras automatiskt i Active Directory, Azure Active Directory och du kan också Office 365 och [andra SaaS-program som stöds av Azure AD](../manage-apps/user-provisioning.md).

* **Medarbetare rehires** – när en anställd rehired i Workday, sina gamla kontot kan automatiskt återaktiverats eller etableras igen (beroende på dina preferenser) till Active Directory, Azure Active Directory, och du kan också Office 365 och [andra SaaS-program som stöds av Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här användaren etablering lösning som bäst passar för?

Den här lösningen för Workday av användaretablering är idealisk för:

* Organisationer som vill ha en färdiga, Microsofts molnbaserade lösning för Workday användaretablering

* Organisationer som kräver direkta användarförsörjning från Workday till Active Directory eller Azure Active Directory

* Organisationer som kräver att användare som ska etableras med hjälp av data som hämtas från Workday HCM-modul (se [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationer som behöver ansluta till, flytta, och låter användare att synkroniseras till en eller flera Active Directory-skogar, domäner och organisationsenheter baseras bara på Ändra användarinformation i Workday HCM-modulen (se [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationer som använder Office 365 för e-post

## <a name="solution-architecture"></a>Lösningsarkitektur

Det här avsnittet beskrivs slutpunkt till slutpunkt-arkitektur för vanliga hybridmiljöer för användaretablering. Det finns två relaterade flöden:

* **Flöde för auktoritativ HR Data – från Workday till en lokal Active Directory:** I det här flödet worker (till exempel nyanställda, dataöverföringar, uppsägningar) först sker i molnet Workday HR-klient och sedan av data flödar till den lokala Active Directory via Azure AD och agenten etablering. Beroende på händelsen, kan det leda till skapa/uppdatera/aktivera/inaktivera operations i AD.
* **Flöde för e-tillbakaskrivning – från en lokal Active Directory till Workday:** När kontot datafabriken har skapats i Active Directory, den är synkroniserad med Azure AD via Azure AD Connect och e-postadressattribut som hämtas från Active Directory kan skrivas tillbaka till Workday.

![Översikt](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Dataflöde för slutpunkt till slutpunkt-användare

1. HR-teamet utför worker-transaktioner (trädkopplingar/flyttfirman/förtid uppgår eller nya anställningar/överföringar/uppsägningar) i Workday HCM-system
2. Azure AD Provisioning-tjänsten körs schemalagda synkroniseringar identiteter från Workday HR och identifierar ändringar som måste bearbetas för synkronisering med lokala Active Directory.
3. Azure AD Provisioning-tjänsten anropar den lokala AAD Connect etablering agenten med en nyttolast för begäran som innehåller AD-konto, skapa/uppdatera/aktivera/inaktivera åtgärder.
4. Azure AD Connect etablering agenten använder ett tjänstkonto för att lägga till/uppdatera AD-kontodata.
5. Azure AD Connect / AD-Synkroniseringsmotorn körs Deltasynkronisering för att hämta uppdateringar i AD.
6. Active Directory-uppdateringarna har synkroniserats med Azure Active Directory.
7. Om anslutningen för tillbakaskrivning av Workday har konfigurerats är det skrivning hakar e-attributet till Workday, baserat på det matchande attribut som används.

## <a name="planning-your-deployment"></a>Planera distributionen

Innan du börjar din Workday-integrering, kontrollera krav nedan och Läs följande riktlinjer för hur som motsvarar dina nuvarande Active Directory-arkitekturen och krav för användaretablering med lösningar som tillhandahålls av Azure Active Directory. En omfattande [distributionsplan](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) med kalkylblad för planering kan även hjälpa dig att samarbeta med din partner för Workday-integrering och HR intressenter.

Det här avsnittet beskriver följande aspekter av Planering:

* [Förutsättningar](#prerequisites)
* [Att välja etablering connector-appar som kan distribueras](#selecting-provisioning-connector-apps-to-deploy)
* [Planera distribution av Azure AD Connect etablering agenten](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integrera med flera Active Directory-domäner](#integrating-with-multiple-active-directory-domains)
* [Planera Workday attributmappning för Active Directory-användare och omvandlingar](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Nödvändiga komponenter

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En giltig Azure AD Premium P1 eller högre prenumeration med global administratör för användaråtkomst
* En klient för implementering av Workday för testning och integration
* Administratörsbehörighet i Workday att skapa en systemanvändare integrering och göra ändringar till medarbetare testdata för testning
* För användaretablering till Active Directory, krävs en server som kör Windows Server 2012 eller senare med .NET 4.7.1+ runtime till värden i [lokala etableringsagenten](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) för att synkronisera användare mellan Active Directory och Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Att välja etablering connector-appar som kan distribueras

För att underlätta etablering av arbetsflöden mellan Workday och Active Directory, tillhandahåller Azure AD flera etablering connector-appar som du kan lägga till från appgalleriet för Azure AD:

![AAD-Appgalleriet](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday till Active Directory användare etableras** – den här appen förenklar konto användarförsörjning från Workday till en enda Active Directory-domän. Om du har flera domäner, kan du lägga till en instans av den här appen från Azure AD-appgalleriet för varje Active Directory-domän som du måste distribuera till.

* **Workday till Azure AD-användare etablering** – AAD Connect är verktyget som ska användas för att synkronisera Active Directory användare till Azure Active Directory, den här appen kan användas för att underlätta etablering av endast molnbaserade användare från Workday till en enda Azure Active Directory-klient.

* **Workday tillbakaskrivning av** -den här appen förenklar tillbakaskrivningen av användarens e-postadresser från Azure Active Directory till Workday.

> [!TIP]
> Appen regelbundna ”Workday” används för att konfigurera enkel inloggning mellan Workday och Azure Active Directory.

Använd beslut flow diagrammet nedan för att identifiera vilka etablering Workday-appar som är relevanta för scenariot.
    ![Beslut flödesschema](./media/workday-inbound-tutorial/wday_app_flowchart.png "beslut flödesschema")

Använda innehållsförteckningen för att gå till relevanta avsnitt i självstudien.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Planera distribution av Azure AD Connect etablering agenten

> [!NOTE]
> Det här avsnittet gäller endast om du planerar att distribuera starttid för Active Directory användaren etablering appen. Du kan hoppa över det här om du distribuerar tillbakaskrivning av Workday eller Workday till Azure AD-användare etablering App.

Workday etableringen av AD-användare lösning kräver distribution av en eller flera etablering agenter på servrar som kör Windows 2012 R2 eller senare med minst 4 GB RAM-minne och .NET 4.7.1+ runtime. Följande överväganden måste beaktas innan du installerar agenten etablering:

* Se till att värdservern kör agenten etablering har nätverksåtkomst till måldomänen AD
* Guiden för konfiguration av etablering agenten registrerar agenten med Azure AD-klienten och registreringen kräver åtkomst till *. msappproxy.net via SSL-port 443. Kontrollera att utgående brandväggsregler är som att den här kommunikationen. Har stöd för agenten [utgående HTTPS-proxykonfigurationen](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* Etablering agenten använder ett tjänstkonto för att kommunicera med lokalt AD-domäner. Du rekommenderas att du skapar ett tjänstkonto med administratörsbehörigheter och ett lösenord som inte upphör att gälla före installationen av agenten.  
* Vid etablering agentkonfiguration väljer du domänkontrollanter som ska hantera etablering begäranden. Om du har flera geografiskt utspridda domänkontrollanter kan installera agenten etablering på samma plats som din prioriterade domänkontrollanter att förbättra tillförlitlighet och prestanda för slutpunkt till slutpunkt-lösning
* För hög tillgänglighet som du kan distribuera flera etablering agenten och registrera den för att hantera samma uppsättning lokala AD-domäner.

> [!IMPORTANT]
> Microsoft rekommenderar att du har minst 3 etablering agenter som konfigurerats med Azure AD-klienten för hög tillgänglighet i produktionsmiljöer.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integrera med flera Active Directory-domäner

> [!NOTE]
> Det här avsnittet gäller endast om du planerar att distribuera starttid för Active Directory användaren etablering appen. Du kan hoppa över det här om du distribuerar tillbakaskrivning av Workday eller Workday till Azure AD-användare etablering App.

Beroende på din Active Directory-topologi måste du bestämma antalet appar som användare Connector etablering och antalet etablering agenter för att konfigurera. Nedan visas några av de vanliga mönster för distribution som du kan referera till när du planerar din distribution.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Distributionsscenariot #1: Single Workday Tenant -> Single AD domain

Du har en Workday-klient i det här scenariot, och du vill etablera användare i ett enda mål AD-domän. Här är den rekommendera produktionskonfigurationen för den här distributionen.

|   |   |
| - | - |
| Nej. för att etablera agenter att distribuera en lokal | 3 (för hög tillgänglighet och redundansväxling) |
| Nej. av Workday till AD användaren etablering appar för att konfigurera i Azure portal | 1 |

  ![Scenario 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Distributionsscenariot #2: Den enda Workday klient -> flera underordnade AD-domäner

Det här scenariet inbegriper att etablera användare från Workday till flera mål AD underordnade domäner i en skog. Här är den rekommendera produktionskonfigurationen för den här distributionen.

|   |   |
| - | - |
| Nej. för att etablera agenter att distribuera en lokal | 3 (för hög tillgänglighet och redundansväxling) |
| Nej. av Workday till AD användaren etablering appar för att konfigurera i Azure portal | en app per underordnad domän |

  ![Scenario 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Distributionsscenariot #3: Den enda Workday klient -> åtskilda AD-skogar

Det här scenariet inbegriper att etablera användare från Workday till domäner i åtskilda AD-skogar. Här är den rekommendera produktionskonfigurationen för den här distributionen.

|   |   |
| - | - |
| Nej. för att etablera agenter att distribuera en lokal | 3 per åtskilda AD-skog |
| Nej. av Workday till AD användaren etablering appar för att konfigurera i Azure portal | en app per underordnad domän |

  ![Scenario 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Planera Workday attributmappning för Active Directory-användare och omvandlingar

> [!NOTE]
> Det här avsnittet gäller endast om du planerar att distribuera starttid för Active Directory användaren etablering appen. Du kan hoppa över det här om du distribuerar tillbakaskrivning av Workday eller Workday till Azure AD-användare etablering App.

Beakta följande frågor innan du konfigurerar användaretablering för en Active Directory-domän. Svaren på dessa frågor avgör hur din Omfångsfilter och attributmappningar måste anges.

* **Vad användare i Workday måste etableras för den här Active Directory-skog?**

  * *Exempel: Användare där Workday ”företagets” attributet innehåller värdet ”Contoso” och ”Worker_Type”-attributet innehåller ”vanliga”*

* **Hur dirigeras användare till olika organisationsenheter (OU)?**

  * *Exempel: Användarna dirigeras till organisationsenheter som motsvarar en arbetsplats, enligt definitionen i Workday ”kommuner” och ”Country_Region_Reference”-attribut*

* **Hur ska du fylls följande attribut i Active Directory?**

  * Nätverksnamn (cn)
    * *Exempel: Använd Workday User_ID-värdet som anges av personalfrågor*

  * Anställnings-ID (employeeId)
    * *Exempel: Använd Workday Worker_ID värde*

  * SAM-kontonamn (sAMAccountName)
    * *Exempel: Använd värdet Workday User_ID filtreras via en Azure AD etablering uttryck att ta bort ogiltiga tecken*

  * Användarens huvudnamn (userPrincipalName)
    * *Exempel: Använda Workday User_ID-värde med en Azure AD etablering uttryck för att lägga till ett domännamn*

* **Hur ska användarna matchas mellan Workday och Active Directory?**

  * *Exempel: Användare med en arbetsdag ”Worker_ID” värde matchas med Active Directory-användare där ”employeeID” har samma värde. Om Worker_ID-värdet inte finns i Active Directory, skapar du en ny användare.*
  
* **Innehåller Active Directory-skogen redan användar-ID krävs för den matchande logiken att fungera?**

  * *Exempel: Om den här installationen är en ny Workday-distribution, rekommenderas att Active Directory är förifyllda med rätt Workday Worker_ID värden (eller unika ID-värdet med) att hålla den matchande logiken som är så enkelt som möjligt.*

Hur du skapar och konfigurerar apparna särskilda connector etablering är föremål för de återstående avsnitten i den här självstudien. Vilka appar som du väljer att konfigurera beror på vilka system som du behöver etablera till, och hur många Active Directory-domäner och Azure AD klienter finns i din miljö.

## <a name="configure-integration-system-user-in-workday"></a>Konfigurera integration systemanvändare i Workday

Ett vanligt krav alla starttid etablering kopplingar är att de kräver autentiseringsuppgifter för en Workday-integrering systemanvändare att ansluta till API: et för Workday Human Resources. Det här avsnittet beskriver hur du skapar en användare i ett integrerat system i Workday och innehåller följande avsnitt:

* [Skapa en användare i ett integrerat system](#creating-an-integration-system-user)
* [Skapa en säkerhetsgrupp för integrering](#creating-an-integration-security-group)
* [Konfigurera principen för domänen säkerhetsbehörigheter](#configuring-domain-security-policy-permissions)
* [Konfigurera business process säkerhetsbehörigheter princip](#configuring-business-process-security-policy-permissions)
* [Aktivera säkerhetsprincipsändringar](#activating-security-policy-changes)

> [!NOTE]
> Det är möjligt att kringgå den här proceduren och i stället använda ett globalt administratörskonto för Workday som integrationskontot system. Detta fungerar bra för demonstrationer, men det rekommenderas inte för Produktionsdistribution.

### <a name="creating-an-integration-system-user"></a>Skapa en användare i ett integrerat system

**Skapa en användare i ett integrerat system:**

1. Logga in på din Workday-klient med ett administratörskonto. I den **Workday programmet**, ange skapa användare i sökrutan och klicka sedan på **skapa Integration systemanvändare**.

    ![Skapa användare](./media/workday-inbound-tutorial/wd_isu_01.png "skapa användare")
2. Slutför den **skapa Integration systemanvändare** uppgift genom att ange ett användarnamn och lösenord för en ny Integration systemanvändare.  
  
* Lämna den **kräver nya lösenord vid nästa inloggning** alternativet är avmarkerat, eftersom den här användaren loggar in via programmering.
* Lämna den **minuter för sessionens Timeout** med standardvärdet 0, vilket förhindrar att användarens sessioner timeout för tidigt.
* Välj alternativet **gör inte tillåta Användargränssnittet sessioner** eftersom det ger dig ett extra säkerhetslager som förhindrar att en användare med lösenordet för integration-system som loggar in på Workday.

    ![Skapa Integration systemanvändare](./media/workday-inbound-tutorial/wd_isu_02.png "skapa Integration systemanvändare")

### <a name="creating-an-integration-security-group"></a>Skapa en säkerhetsgrupp för integrering

I det här steget ska du skapa en säkerhetsgrupp för integrering med obegränsad eller begränsad system i Workday och tilldela integration systemanvändaren som skapades i föregående steg i den här gruppen.

**Skapa en säkerhetsgrupp:**

1. Ange skapa säkerhetsgrupp i sökrutan och klicka sedan på **skapa säkerhetsgrupp**.

    ![CreateSecurity grupp](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity grupp")
2. Slutför den **skapa säkerhetsgrupp** uppgift. 

   * Det finns två typer av säkerhetsgrupper i Workday:
     * **Obegränsad:** Alla medlemmar i gruppen kan komma åt alla data-instanser som skyddas av gruppen.
     * **Begränsad:** Alla medlemmar i gruppen säkerhet har sammanhangsberoende åtkomst till en delmängd av data-instanser (rader) som gruppen har åtkomst till.
   * Kontakta din partner för integrering av Workday att välja lämpliga säkerhetsgruppstypen för integrering.
   * När du vet vilken grupp, Välj **integrering Systemsäkerhetsgrupp (obegränsat)** eller **integrering Systemsäkerhetsgrupp (Constrained)** från den **typen Backups säkerhetsgrupp**  listrutan.

     ![CreateSecurity grupp](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity grupp")

3. När den säkerhetsgruppen har skapats, visas en sida där du kan tilldela medlemmar till gruppen. Lägg till ny integration systemanvändaren som skapades i föregående steg i den här säkerhetsgruppen. Om du använder *begränsad* säkerhetsgruppen, du måste också välja lämplig organisationens omfång.

    ![Redigera säkerhetsgrupp](./media/workday-inbound-tutorial/wd_isu_05.png "redigera grupp")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurera principen för domänen säkerhetsbehörigheter

I det här steget ska du ge ”domain security” principbehörigheter för worker-data i gruppen.

**Du konfigurerar principen för domänbehörigheter:**

1. Ange **domän säkerhetskonfiguration** i sökrutan och sedan klicka på länken **Domain Security konfigurationsrapport**.  

    ![Domän-säkerhetsprinciper](./media/workday-inbound-tutorial/wd_isu_06.png "domän säkerhetsprinciper")  
2. I den **domän** textrutan, Sök efter följande domäner och lägga till dem i filtret en i taget.  
   * *Extern etablering*
   * *Worker Data: Offentliga Worker rapporter*
   * *Personliga Data: Arbeta kontaktinformation*
   * *Worker Data: Alla positioner*
   * *Worker Data: Aktuell Personal Information*
   * *Worker Data: Företag titel på Worker profil*

     ![Domän-säkerhetsprinciper](./media/workday-inbound-tutorial/wd_isu_07.png "domän säkerhetsprinciper")  

     ![Domän-säkerhetsprinciper](./media/workday-inbound-tutorial/wd_isu_08.png "domän säkerhetsprinciper") 

     Klicka på **OK**.

3. I den rapport som visas, väljer du ellipsen (...) visas bredvid **externa Kontoetablering** och klicka på menyalternativet **domän -> Redigera principen säkerhetsbehörigheter**

    ![Domän-säkerhetsprinciper](./media/workday-inbound-tutorial/wd_isu_09.png "domän säkerhetsprinciper")  

4. På den **redigera domän princip säkerhetsbehörigheter** sida, rulla ned till avsnittet **integrering behörigheter**. Klicka på tecknet ”+” för att lägga till gruppen integration system i listan över säkerhetsgrupper med **hämta** och **placera** integrering behörigheter.

    ![Redigera](./media/workday-inbound-tutorial/wd_isu_10.png "redigera behörighet")  

5. Klicka på tecknet ”+” för att lägga till gruppen integration system i listan över säkerhetsgrupper med **hämta** och **placera** integrering behörigheter.

    ![Redigera](./media/workday-inbound-tutorial/wd_isu_11.png "redigera behörighet")  

6. Upprepa steg 3 – 5 ovan för var och en av dessa återstående säkerhetsprinciper:

   | Åtgärd | Säkerhetsprincip för domän |
   | ---------- | ---------- |
   | Hämta och skicka | Worker Data: Offentliga Worker rapporter |
   | Hämta och skicka | Personliga Data: Arbeta kontaktinformation |
   | Hämta | Worker Data: Alla positioner |
   | Hämta | Worker Data: Aktuell Personal Information |
   | Hämta | Worker Data: Företag titel på Worker profil |

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurera business process säkerhetsbehörigheter princip

I det här steget ska du ge ”företag processen security” principbehörigheter för worker-data i gruppen. Det här steget krävs för att konfigurera tillbakaskrivning av Workday app connector.

**Du konfigurerar säkerhetsbehörigheter för principen på företag processen:**

1. Ange **processen företagsprincip** i sökrutan och sedan klicka på länken **redigera säkerhetsprincip för företag processen** uppgift.  

    ![Säkerhetsprinciper för företag-processen](./media/workday-inbound-tutorial/wd_isu_12.png "säkerhetsprinciper för företag-processen")  

2. I den **företag processtypen** textrutan, Sök efter *Kontakta* och välj **kontakta ändra** affärsprocesser och klicka på **OK**.

    ![Säkerhetsprinciper för företag-processen](./media/workday-inbound-tutorial/wd_isu_13.png "säkerhetsprinciper för företag-processen")  

3. På den **redigera säkerhetsprincip för företag processen** , bläddrar du till den **underhålla kontaktinformation (webbtjänst)** avsnittet.

    ![Säkerhetsprinciper för företag-processen](./media/workday-inbound-tutorial/wd_isu_14.png "säkerhetsprinciper för företag-processen")  

4. Välj och Lägg till den nya säkerhetsgruppen för integration-system i listan över säkerhetsgrupper som kan starta web services-begäran. Klicka på **klar**. 

    ![Säkerhetsprinciper för företag-processen](./media/workday-inbound-tutorial/wd_isu_15.png "säkerhetsprinciper för företag-processen")  

### <a name="activating-security-policy-changes"></a>Aktivera säkerhetsprincipsändringar

**Aktivera säkerhetsprincipsändringar:**

1. Ange aktivera i sökrutan och sedan klicka på länken **aktivera väntande Säkerhetsprincipsändringar**.

    ![Aktivera](./media/workday-inbound-tutorial/wd_isu_16.png "aktivera")

1. Starta Aktivera väntande Säkerhetsprincipsändringar aktiviteten genom att ange en kommentar i granskningssyfte och klicka sedan på **OK**.
1. Slutföra uppgiften på nästa skärm genom att markera kryssrutan **Bekräfta**, och klicka sedan på **OK**.

    ![Aktivera väntande Security](./media/workday-inbound-tutorial/wd_isu_18.png "aktivera väntande säkerhet")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurera användaretablering från Workday till Active Directory

Det här avsnittet innehåller steg för etablering av användarkonto från Workday till varje Active Directory-domän inom omfånget för din integrering.

* [Installera och konfigurera den lokala etablering agenter](#part-1-install-and-configure-on-premises-provisioning-agents)
* [Att lägga till appen etablering connector och skapa anslutning till Workday](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurera attributmappningar](#part-3-configure-attribute-mappings)
* [Aktivera och starta etableringen av användare](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Del 1: Installera och konfigurera den lokala etablering agenter

Om du vill distribuera till Active Directory lokalt, måste en agent installeras på en server som har .NET 4.7.1+ ramverk och nätverk som har åtkomst till den önskade Active Directory-domäner.

> [!TIP]
> Du kan kontrollera version av .NET framework på servern med hjälp av anvisningarna [här](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Om servern inte har .NET 4.7.1 eller senare installerat, du kan ladda ned det från [här](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

När du har distribuerat .NET 4.7.1+ kan du ladda ned den **[lokala etableringsagenten här](https://go.microsoft.com/fwlink/?linkid=847801)** och följ anvisningarna nedan för att slutföra agentkonfigurationen.

1. Logga in på Windows Server där du vill installera den nya agenten.
2. Starta installationsprogrammet för etablering agenten, Godkänn villkoren och klicka på den **installera** knappen.

   ![Installera skärmen](./media/workday-inbound-tutorial/pa_install_screen_1.png "installera skärmen")
3. När installationen är klar, startar guiden och du ser den **Anslut Azure AD** skärmen. Klicka på den **autentisera** knapp för att ansluta till din Azure AD-instans.

   ![Ansluter Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "ansluter Azure AD")
1. Autentisera till din Azure AD-instans med hjälp av autentiseringsuppgifter som Global administratör.

   ![Administratören Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Admin Auth")

> [!NOTE]
> Azure AD-administratörsautentiseringsuppgifter används bara för att ansluta till Azure AD-klienten. Agenten lagrar inte autentiseringsuppgifterna lokalt på servern.

1. Efter en lyckad autentisering med Azure AD, visas den **Anslut Active Directory** skärmen. I det här steget anger du ditt AD-domännamn och klicka på den **Lägg till katalog** knappen.

   ![Lägg till katalog](./media/workday-inbound-tutorial/pa_install_screen_4.png "Lägg till katalog")
  
1. Nu uppmanas du att ange de autentiseringsuppgifter som krävs för att ansluta till AD-domänen. På samma skärm, kan du använda den **Välj domain controller prioritet** ange domänkontrollanter som agenten ska använda för att skicka etableringsbegäranden.

   ![Domain Credentials](./media/workday-inbound-tutorial/pa_install_screen_5.png)
1. När du har konfigurerat domänen visar installationsprogrammet en lista över konfigurerade domäner. På den här skärmen kan du upprepa steg #5 och 6 för # att lägga till fler domäner eller klicka på **nästa** att fortsätta till registreringen.

   ![Konfigurerade domäner](./media/workday-inbound-tutorial/pa_install_screen_6.png "konfigurerats domäner")

   > [!NOTE]
   > Om du har flera AD-domäner (t.ex. na.contoso.com, emea.contoso.com) och sedan Lägg till varje domän individuellt i listan. Det räcker inte att bara att lägga till den överordnade domänen (t.ex. contoso.com). Du måste registrera varje underordnad domän med agenten.
1. Granska konfigurationsinformationen och klicka på **Bekräfta** att registrera agenten.
  
   ![Bekräfta skärmen](./media/workday-inbound-tutorial/pa_install_screen_7.png "bekräfta skärmen")
1. Guiden visar förloppet för registreringen av.
  
   ![Agentregistreringen](./media/workday-inbound-tutorial/pa_install_screen_8.png "Agentregistreringen")
1. När agentregistreringen är klar, kan du klicka på **avsluta** vill avsluta guiden.
  
   ![Avsluta skärmen](./media/workday-inbound-tutorial/pa_install_screen_9.png "avsluta skärmen")
1. Verifiera installationen av agenten och kontrollera att den körs genom att öppna snapin-modulen ”Tjänster” och leta efter tjänsten med namnet ”Microsoft Azure AD Connect etablering Agent”
  
   ![Tjänster](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Del 2: Att lägga till appen etablering connector och skapa anslutning till Workday

**Konfigurera Workday till Active Directory-etablering:**

1. Gå till <https://portal.azure.com>

2. I det vänstra navigeringsfältet väljer **Azure Active Directory**

3. Välj **företagsprogram**, sedan **alla program**.

4. Välj **lägga till ett program**, och välj den **alla** kategori.

5. Sök efter **Workday Provisioning för Active Directory**, och Lägg till appen från galleriet.

6. När appen har lagts till och appen informationsskärmen är visas, väljer **etablering**

7. Ändra den **etablering** **läge** till **automatisk**

8. Slutför den **administratörsautentiseringsuppgifter** avsnittet på följande sätt:

   * **Administratören Username** – ange användarnamnet för system-kontot Workday-integrering med klientens domännamn tillagt. Det bör se ut ungefär så: **användarnamn\@tenant_name**

   * **Adminlösenord –** anger du lösenordet för kontot system Workday-integrering

   * **Klient-URL –** anger du Webbadressen till Workday web services-slutpunkt för din klient. Det här värdet bör se ut: https://wd3-impl-services1.workday.com/ccx/service/contoso4, där *contoso4* ersätts med rätt klient-namn och *wd3 impl* ersätts med rätt miljö-sträng.

   * **Active Directory-skog -** ”namn” på din Active Directory-domän, eftersom registrerad med agenten. Använd listrutan för att välja måldomänen för etablering. Det här värdet är vanligtvis en sträng som: *contoso.com*

   * **Active Directory-behållare –** ange behållaren DN där agenten ska skapa användarkonton som standard.
        Exempel: *OU = standardanvändare, OU = Users, DC = contoso, DC = test*
     > [!NOTE]
     > Den här inställningen kommer endast betydelse för användaren kontoskapningar om den *parentDistinguishedName* attributet har inte konfigurerats på attributmappningarna. Den här inställningen används inte för användarsökning eller uppdaterar åtgärder. Hela domänen sub trädet ingår i omfånget för search-åtgärd.

   * **E-postmeddelande –** ange din e-postadress och markera kryssrutan ”Skicka e-post om fel uppstår”.

> [!NOTE]
> Azure AD Provisioning-tjänsten skickar e-postmeddelande om Etableringsjobbet hamnar i en [karantän](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) tillstånd.

   * Klicka på den **Testanslutningen** knappen. Om Anslutningstestet lyckas klickar du på den **spara** längst upp. Om det misslyckas kontrollerar du att Workday-autentiseringsuppgifter och AD konfigurerat på agentinställningen är giltiga.

     ![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

   * När autentiseringsuppgifterna har sparats, den **mappningar** avsnittet visar standardmappningen **synkronisera Workday arbetare till på lokala Active Directory**

### <a name="part-3-configure-attribute-mappings"></a>Del 3: Konfigurera attributmappningar

I det här avsnittet konfigurerar du hur informationen flödar från Workday till Active Directory.

1. På fliken etablering under **mappningar**, klickar du på **synkronisera Workday arbetare till på lokala Active Directory**.

2. I den **omfång för källobjekt** fältet, kan du välja vilka uppsättningar med användare i Workday ska vara i omfånget för etablering i AD, genom att definiera en uppsättning attributbaserade filter. Standardvärde är ”alla användare i Workday”. Exempel filter:

   * Exempel: Omfång för användare med Worker-ID: N mellan 1000000 och 2000000 (exklusive 2000000)

      * Attribut: WorkerID

      * Operator: REGEX-matchning

      * Värde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exempel: Endast anställda och inte tillfällig arbetare

      * Attribut: EmployeeID

      * Operator: INTE ÄR NULL

> [!TIP]
> När du konfigurerar appen etablering för första gången behöver du testa och verifiera dina attributmappningar och uttryck för att se till att den ger du det önskade resultatet. Microsoft rekommenderar att du använder omfånget filtrerar **omfång för källobjekt** att testa din mappningar med några få testa användare från Workday. När du har verifierat att mappningarna fungerar och du kan ta bort filtret eller gradvis expanderas för att inkludera fler användare.

1. I den **åtgärder för målobjekt** fältet du globalt filtrera vilka åtgärder som utförs på Active Directory. **Skapa** och **uppdatering** är de vanligaste.

1. I den **attributmappningar** avsnittet, kan du definiera hur enskilda Workday attribut mappar till Active Directory-attribut.

1. Klicka på en befintlig attributmappning att uppdatera den, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En enskild attributmappning har stöd för dessa egenskaper:

      * **Mappningstyp**

         * **Direkt** – skriver värdet för attributet Workday till attributet AD utan ändringar

         * **Konstant** -skriva en statisk, konstant strängvärde till AD-attribut

         * **Uttrycket** – kan du skriva ett anpassat värde till attributet AD baserat på en eller flera Workday-attribut. [Mer information finns i den här artikeln på uttryck](../manage-apps/functions-for-customizing-application-data.md).

      * **Källattributet** -användarattributet från Workday. Om attributet som du letar efter inte finns, se [anpassning av listan över Workday-användarattribut](#customizing-the-list-of-workday-user-attributes).

      * **Standardvärde** – valfritt. Om källattributet har ett tomt värde, skriver mappningen du det här värdet i stället.
            De vanligaste konfiguration är att lämna fältet tomt.

      * **Målattribut** – användarattribut i Active Directory.

      * **Matcha objekt med hjälp av det här attributet** – oavsett om den här mappningen ska användas för att unikt identifiera användare mellan Workday och Active Directory. Det här värdet anges normalt på Worker-ID-fältet för Workday, vilket vanligtvis är mappad till ett anställnings-ID-attribut i Active Directory.

      * **Matchningsprioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som anges av det här fältet. När en matchning hittas, matchar ingen ytterligare attribut utvärderas.

      * **Tillämpa den här mappningen**

         * **Alltid** – tillämpa den här mappningen för både skapa användare och uppdatera åtgärder

         * **Endast när skapas** -tillämpa den här mappningen endast på åtgärder för skapande av användare

1. Om du vill spara din mappningar, klickar du på **spara** överst i avsnittet attribut mappar.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Nedan visas några exempel attributmappningar Workday och Active Directory, med vissa vanliga uttryck

* Det uttryck som mappar till den *parentDistinguishedName* attributet används för att etablera en användare till olika organisationsenheter baserat på en eller flera Workday källa attribut. Det här exemplet här placerar användare i olika organisationsenheter baserat på vilken stad de finns i.

* Den *userPrincipalName* attribut i Active Directory genereras med hjälp av funktionen avduplicering [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) som kontrollerar om ett genererat värde i måldomänen AD finns endast och Anger det om det är unikt.  

* [Det finns dokumentation om hur du skriver uttryck här](../manage-apps/functions-for-customizing-application-data.md). Det här avsnittet innehåller exempel på hur du tar bort specialtecken.

| WORKDAY ATTRIBUT | ACTIVE DIRECTORY-ATTRIBUT |  MATCHANDE ID? | SKAPA / UPPDATERA |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ja** | Skrivna på enbart skapa |
| **PreferredNameData**    |  CN    |   |   Skrivna på enbart skapa |
| **SelectUniqueValue (ansluta till (”\@”, ansluta till (””., \[FirstName\], \[LastName\]), ”contoso.com”), ansluta till (”\@”, ansluta till (””., Mid (\[FirstName\], 1, 1 (), \[LastName\]), ”contoso.com”), ansluta till (”\@”, ansluta till (””., Mid (\[FirstName\], 1, 2), \[LastName\]), ”contoso.com”))**   | userPrincipalName     |     | Skrivna på enbart skapa 
| **Ersätt(Mid(Ersätt(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\ \\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Skrivna på enbart skapa |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | Skapa och uppdatera |
| **Förnamn**   | givenName       |     |    Skapa och uppdatera |
| **Efternamn**   |   SN   |     |  Skapa och uppdatera |
| **PreferredNameData**  |  displayName |     |   Skapa och uppdatera |
| **Företagets**         | company   |     |  Skapa och uppdatera |
| **SupervisoryOrganization**  | avdelning  |     |  Skapa och uppdatera |
| **ManagerReference**   | ansvarig  |     |  Skapa och uppdatera |
| **BusinessTitle**   |  title     |     |  Skapa och uppdatera | 
| **AddressLineData**    |  streetAddress  |     |   Skapa och uppdatera |
| **Kommuner**   |   L   |     | Skapa och uppdatera |
| **CountryReferenceTwoLetter**      |   CO |     |   Skapa och uppdatera |
| **CountryReferenceTwoLetter**    |  c  |     |         Skapa och uppdatera |
| **CountryRegionReference** |  St     |     | Skapa och uppdatera |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Skapa och uppdatera |
| **PostalCode**  |   Postnummer  |     | Skapa och uppdatera |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Skapa och uppdatera |
| **Fax**      | facsimileTelephoneNumber     |     |    Skapa och uppdatera |
| **Mobile**  |    mobila       |     |       Skapa och uppdatera |
| **LocalReference** |  preferredLanguage  |     |  Skapa och uppdatera |                                               
| **Växel (\[kommuner\]”, OU = standardanvändare, OU = användare, OU = standard, OU = platser, DC = contoso, DC = com”, ”Dallas” ”, OU = standardanvändare, OU = användare, OU = Dallas, OU = platser, DC = contoso, DC = com”, ”Austin” ”, OU = standardanvändare, OU = Användare, OU = Austin, OU = platser, DC = contoso, DC = com ”,” Seattle ””, OU = standardanvändare, OU = användare, OU = Seattle, OU = platser, DC = contoso, DC = com ”,” London ””, OU = standardanvändare, OU = användare, OU = London, OU = platser, DC = contoso, DC = com ”)**  | parentDistinguishedName     |     |  Skapa och uppdatera |

När attributet mappningskonfigurationen är klar kan du nu [aktivera och starta tjänst för användaretablering](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Konfigurera användaretablering till Azure AD

I följande avsnitt beskrivs stegen för att konfigurera användaretablering från Workday till Azure AD för endast molnbaserade distributioner.

* [Att lägga till Azure AD-etablering connector-app och skapa anslutning till Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurera Workday och Azure AD attributmappningar](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Aktivera och starta etableringen av användare](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Endast följer du anvisningarna nedan om du har endast molnbaserade användare som behöver etableras till Azure AD och inte en lokal Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Del 1: Att lägga till Azure AD-etablering connector-app och skapa anslutning till Workday

**Konfigurera Workday till Azure Active Directory-etablering för molnexklusiva användare:**

1. Gå till <https://portal.azure.com>.

2. I det vänstra navigeringsfältet väljer **Azure Active Directory**

3. Välj **företagsprogram**, sedan **alla program**.

4. Välj **lägga till ett program**, och välj sedan den **alla** kategori.

5. Sök efter **Workday till Azure AD-etablering**, och Lägg till appen från galleriet.

6. När appen har lagts till och appen informationsskärmen är visas, väljer **etablering**

7. Ändra den **etablering** **läge** till **automatisk**

8. Slutför den **administratörsautentiseringsuppgifter** avsnittet på följande sätt:

   * **Administratören Username** – ange användarnamnet för system-kontot Workday-integrering med klientens domännamn tillagt. Bör se ut ungefär som: username@contoso4

   * **Adminlösenord –** anger du lösenordet för kontot system Workday-integrering

   * **Klient-URL –** anger du Webbadressen till Workday web services-slutpunkt för din klient. Det här värdet bör se ut: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, där *contoso4* ersätts med rätt klient-namn och *wd3 impl* ersätts med rätt miljö-sträng. Om den här URL: en inte är känd, arbeta med din Workday-integrering-partner eller support representant för att fastställa rätt Webbadress för användning.

   * **E-postmeddelande –** ange din e-postadress och markera kryssrutan ”Skicka e-post om fel uppstår”.

   * Klicka på den **Testanslutningen** knappen.

   * Om Anslutningstestet lyckas klickar du på den **spara** längst upp. Om det misslyckas kontrollerar du att Workday-URL och autentiseringsuppgifter är giltiga i Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Del 2: Konfigurera Workday och Azure AD attributmappningar

I det här avsnittet konfigurerar du hur informationen flödar från Workday till Azure Active Directory för molnexklusiva användare.

1. På fliken etablering under **mappningar**, klickar du på **synkronisera arbetare till Azure AD**.

2. I den **omfång för källobjekt** fältet, kan du välja vilka uppsättningar med användare i Workday ska vara i omfånget för etablering till Azure AD genom att definiera en uppsättning attributbaserade filter. Standardvärde är ”alla användare i Workday”. Exempel filter:

   * Exempel: Omfång för användare med Worker-ID: N mellan 1000000 och 2000000

      * Attribut: WorkerID

      * Operator: REGEX-matchning

      * Värde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exempel: Endast tillfällig arbetare och inte fast anställda

      * Attribut: ContingentID

      * Operator: INTE ÄR NULL

3. I den **åtgärder för målobjekt** fältet du globalt filtrera vilka åtgärder som utförs på Azure AD. **Skapa** och **uppdatering** är de vanligaste.

4. I den **attributmappningar** avsnittet, kan du definiera hur enskilda Workday attribut mappar till Active Directory-attribut.

5. Klicka på en befintlig attributmappning att uppdatera den, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En enskild attributmappning har stöd för dessa egenskaper:

   * **Mappningstyp**

      * **Direkt** – skriver värdet för attributet Workday till attributet AD utan ändringar

      * **Konstant** -skriva en statisk, konstant strängvärde till AD-attribut

      * **Uttrycket** – kan du skriva ett anpassat värde till attributet AD baserat på en eller flera Workday-attribut. [Mer information finns i den här artikeln på uttryck](../manage-apps/functions-for-customizing-application-data.md).

   * **Källattributet** -användarattributet från Workday. Om attributet som du letar efter inte finns, se [anpassning av listan över Workday-användarattribut](#customizing-the-list-of-workday-user-attributes).

   * **Standardvärde** – valfritt. Om källattributet har ett tomt värde, skriver mappningen du det här värdet i stället.
            De vanligaste konfiguration är att lämna fältet tomt.

   * **Målattribut** – användarattribut i Azure AD.

   * **Matcha objekt med hjälp av det här attributet** – oavsett om det här attributet som ska användas för att unikt identifiera användare mellan Workday och Azure AD. Det här värdet anges normalt på Worker-ID-fältet för Workday, vilket vanligtvis är mappad till attributet anställnings-ID (nytt) eller ett tilläggsattribut i Azure AD.

   * **Matchningsprioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som anges av det här fältet. När en matchning hittas, matchar ingen ytterligare attribut utvärderas.

   * **Tillämpa den här mappningen**

     * **Alltid** – tillämpa den här mappningen för både skapa användare och uppdatera åtgärder

     * **Endast när skapas** -tillämpa den här mappningen endast på åtgärder för skapande av användare

6. Om du vill spara din mappningar, klickar du på **spara** överst i avsnittet attribut mappar.

När attributet mappningskonfigurationen är klar kan du nu [aktivera och starta tjänst för användaretablering](#enable-and-launch-user-provisioning).

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Konfigurera tillbakaskrivning av e-postadresser till Workday

Följ dessa instruktioner för att konfigurera tillbakaskrivning av användare e-postadresser från Azure Active Directory till Workday.

* [Att lägga till appen tillbakaskrivning av anslutningen och skapa anslutning till Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurera tillbakaskrivning av attributmappningar](#part-2-configure-writeback-attribute-mappings)
* [Aktivera och starta etableringen av användare](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Del 1: Att lägga till appen tillbakaskrivning av anslutningen och skapa anslutning till Workday

**Att konfigurera tillbakaskrivning av Workday-anslutningen:**

1. Gå till <https://portal.azure.com>

2. I det vänstra navigeringsfältet väljer **Azure Active Directory**

3. Välj **företagsprogram**, sedan **alla program**.

4. Välj **lägga till ett program**och välj sedan den **alla** kategori.

5. Sök efter **Workday tillbakaskrivning av**, och Lägg till appen från galleriet.

6. När appen har lagts till och appen informationsskärmen är visas, väljer **etablering**

7. Ändra den **etablering** **läge** till **automatisk**

8. Slutför den **administratörsautentiseringsuppgifter** avsnittet på följande sätt:

   * **Administratören Username** – ange användarnamnet för system-kontot Workday-integrering med klientens domännamn tillagt. Bör likna: *användarnamn\@contoso4*

   * **Adminlösenord –** anger du lösenordet för kontot system Workday-integrering

   * **Klient-URL –** anger du Webbadressen till Workday web services-slutpunkt för din klient. Det här värdet bör se ut: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, där *contoso4* ersätts med rätt klient-namn och *wd3 impl* ersätts med rätt miljö-sträng (vid behov).

   * **E-postmeddelande –** ange din e-postadress och markera kryssrutan ”Skicka e-post om fel uppstår”.

   * Klicka på den **Testanslutningen** knappen. Om Anslutningstestet lyckas klickar du på den **spara** längst upp. Om det misslyckas kontrollerar du att Workday-URL och autentiseringsuppgifter är giltiga i Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Del 2: Konfigurera tillbakaskrivning av attributmappningar

I det här avsnittet konfigurerar du hur tillbakaskrivning av attribut som flödar från Azure AD till Workday.

1. På fliken etablering under **mappningar**, klickar du på **synkronisera Azure Active Directory-användare till Workday**.

2. I den **omfång för källobjekt** fält, du kan också filtrera, som nu uppsättningar med användare i Azure Active Directory bör ha sina e-postadresser skrivas tillbaka till Workday. Standardvärde är ”alla användare i Azure AD”.

3. I den **attributmappningar** avsnittet, uppdatera matchande-ID för att ange attributet i Azure Active Directory där Workday arbetar-ID eller anställnings-ID lagras. En populär matchande metod är att synkronisera Workday arbetar-ID eller anställnings-ID till extensionAttribute1-15 i Azure AD och sedan använda det här attributet i Azure AD för att matcha användare tillbaka i Workday.

4. Om du vill spara din mappningar, klickar du på **spara** överst i avsnittet attribut mappar.

När attributet mappningskonfigurationen är klar kan du nu [aktivera och starta tjänst för användaretablering](#enable-and-launch-user-provisioning). 

## <a name="enable-and-launch-user-provisioning"></a>Aktivera och starta etableringen av användare

När appkonfigurationer för Workday-etablering har slutförts, kan du aktivera etableringstjänsten i Azure-portalen.

> [!TIP]
> Som standard när du aktiverar etableringstjänsten, initierar den etablering åtgärder för alla användare inom området. Om det uppstår fel i dataproblem mappning eller Workday och sedan etablering jobbet kan misslyckas och i karantän tillstånd. Om du vill undvika detta, som bästa praxis, rekommenderar vi konfigurera **omfång för källobjekt** filter och testa din attributmappningar med några testanvändare innan du startar den fullständiga synkroniseringen för alla användare. När du har kontrollerat att mappningarna fungerar och är du får önskat resultat, och sedan kan du antingen ta bort filtret eller gradvis Expandera den och lägga till fler användare.

1. I den **etablering** fliken genom att ange den **Etableringsstatus** till **på**.

2. Klicka på **Spara**.

3. Den här åtgärden startar den första synkroniseringen, vilket kan ta varierande flera timmar beroende på hur många användare finns i Workday-klient. 

4. När som helst kontrollera den **granskningsloggar** flik i Azure portal för att se vilka åtgärder som etableringstjänsten har utförts. Granskningsloggarna innehåller alla enskilda synkroniseringar händelser utförs av etableringstjänsten, till exempel vilka användare som ska läsa från Workday och sedan därefter läggs till eller uppdateras till Active Directory. Se avsnittet om felsökning för instruktioner om hur du granskningsloggarna och åtgärda etablering fel.

5. När den inledande synkroniseringen är klar skrivs en sammanfattning av granskningsrapporten den **etablering** fliken enligt nedan.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor (FAQ)

* **Lösningen kapaciteten för frågor**
  * [Vid bearbetning av en ny nyanställda från Workday, hur lösningen anger lösenordet för det nya användarkontot i Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Stöder lösningen skicka e-postaviseringar när du har etablerat åtgärder som är klar?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Hur jag för att hantera leverans av lösenord för nyanställda och på ett säkert sätt tillhandahåller en mekanism för att återställa sina lösenord?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Lösningen cachelagra Workday användarprofiler i Azure AD-molnet eller på etablering agent-nivå?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Stöd lösning tilldela lokala AD-grupper för användaren?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Vilka Workday-API: er använder lösningen fråge- och Workday worker-profiler?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Kan jag konfigurera min Workday HCM-klient med två Azure AD-klienter?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Varför stöds inte ”Workday till Azure AD” användaretablering app om vi har distribuerat Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Hur jag för att föreslå förbättringar eller efterfråga nya funktioner som är relaterade till Workday och Azure AD-integration?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Etablering av agenten frågor**
  * [Vad är GA-versionen av agenten etablering?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Hur vet jag för min etablering-agentens version?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Microsoft push etablering agentuppdateringar automatiskt?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Kan jag installera agenten etablering på samma server som kör AAD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect)
  * [Hur konfigurerar jag att etablering agenten ska använda en proxyserver för utgående HTTP-kommunikation?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Hur undviker jag att att etablera-agenten kan kommunicera med Azure AD-klient och inga brandväggar blockerar portar som krävs av agenten?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Hur registrerar jag domänen som är associerade med min etablering Agent ta bort?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Hur avinstallerar jag etablering agenten?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Workday på AD-attributet mappnings- och -frågor**
  * [Hur jag för att säkerhetskopiera eller exportera en fungerande kopia av min Workday etablering attributmappning och schemat?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Jag har anpassade attribut i Workday och Active Directory. Hur konfigurerar jag lösningen att arbeta med min anpassade attribut?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Kan jag etablera användarens foto från Workday till Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Hur synkroniserar jag mobila nummer från Workday baserat på användarens medgivande för användning av offentlig?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Hur formaterar namn som visas i AD baserat på användarens avdelning/land/stad attribut och referensen regionala avvikelser?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Hur kan jag använda SelectUniqueValue för att generera unika värden för samAccountName-attribut](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Hur jag för att ta bort tecken med diakritiska tecken och konvertera dem till normal engelska bokstäver?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Lösningen kapaciteten för frågor

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Vid bearbetning av en ny nyanställda från Workday, hur lösningen anger lösenordet för det nya användarkontot i Active Directory?

När den lokala etableringsagenten hämtar en begäran om att skapa ett nytt AD-konto, det automatiskt genererar ett komplext slumpmässiga lösenord som utformats för att uppfylla kraven på lösenordskomplexitet definieras av AD-servern och det här användarobjektet. Det här lösenordet loggas inte.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Stöder lösningen skicka e-postaviseringar när du har etablerat åtgärder som är klar?

Nej, skicka e-postaviseringar när du har slutfört etablering åtgärder inte stöds i den aktuella versionen.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Hur jag för att hantera leverans av lösenord för nyanställda och på ett säkert sätt tillhandahåller en mekanism för att återställa sina lösenord?

En av de sista stegen som ingår i nya AD etablering innebär att det tillfälliga lösenordet som tilldelats till användarens AD-konto. Många företag fortfarande använda den traditionella metoden för att leverera det tillfälliga lösenordet till användarens chef, som lämnar sedan över lösenordet med nya anställa/tillfällig arbetsrollen. Den här processen har en inbyggd säkerhet fel och det finns ett alternativ för att implementera en bättre metod med hjälp av Azure AD-funktioner.

Som en del av den anställningsstrategi HR team vanligtvis Kontrollera bakgrund och se mobiltelefonnumret till den nya anställa. Du kan byggas ovanpå detta faktum med Workday till AD-användare etablering integration och återställningsfunktion för distribution av lösenord för användaren på dag 1. Detta åstadkoms genom sprider attributet ”mobiltelefonnummer” för den nya nyanställda från Workday till AD och sedan använda AAD Connect från AD till Azure AD. När ”mobiltelefonnummer” finns i Azure AD kan du aktivera den [Self-Service lösenord återställer (SSPR)](../authentication/howto-sspr-authenticationdata.md) för användarens konto så att på dag 1, en ny anställa kan använda registrerade och verifierade mobiltelefonnumret för autentisering.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Lösningen cachelagra Workday användarprofiler i Azure AD-molnet eller på etablering agent-nivå?

Nej, lösningen inte har ett cacheminne med användarprofiler. Azure AD-etableringstjänsten fungerar bara som en läsning av data från Workday och skriva till målet Active Directory eller Azure AD. Se avsnittet [hantera personliga data](#managing-personal-data) information som rör användaren sekretess och datalagring.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Stöd lösning tilldela lokala AD-grupper för användaren?

Den här funktionen stöds inte för närvarande. Rekommenderad lösning är att distribuera ett PowerShell-skript som frågar Azure AD Graph API-slutpunkt för granskningsdata och använda den för att utlösa scenarier, till exempel grupptilldelning. Det här PowerShell-skriptet kan bifogas en Schemaläggaren och distribueras på samma rutan Kör etableringsagenten.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Vilka Workday-API: er använder lösningen fråge- och Workday worker-profiler?

Lösningen använder för närvarande följande Workday API: er:

* Get_Workers (v21.1) för att hämta information om arbetare
* Maintain_Contact_Information (v26.1) för funktionen för tillbakaskrivning av arbets-e-post

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Kan jag konfigurera min Workday HCM-klient med två Azure AD-klienter?

Ja, den här konfigurationen stöds. Här är de viktigaste stegen för att konfigurera det här scenariot:

* Distribuera etableringsagenten #1 och registrera den med Azure AD-klient #1.
* Distribuera etableringsagenten #2 och registrera den med Azure AD-klient #2.
* Baserat på de ”underordnade domänerna” som varje etablering Agent kommer att hantera, konfigurera varje agent med domänerna. En agent kan hantera flera domäner.
* Konfigurera Workday till AD-användare etablering App i varje klient i Azure-portalen och konfigurera den med respektive domäner.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Varför stöds inte ”Workday till Azure AD” användaretablering app om vi har distribuerat Azure AD Connect?

När Azure AD används i hybridläge (där den innehåller en blandning av cloud + lokala användare), är det viktigt att ha en tydlig definition av ”auktoritetskälla”. Normalt kräver hybridscenarier distribution av Azure AD Connect. När Azure AD Connect har distribuerats kan lokala AD är källan för utfärdare. Introduktion till arbetsdagen till Azure AD-koppling till vilken blandning kan leda till en situation där Workday attributvärden gick kan skrivas över de värden som anges av Azure AD Connect. Därför stöds inte användning av ”Workday till Azure AD” etablering appen när Azure AD Connect är aktiverad. I sådana situationer bör du använda ”Workday till AD-användare” etablering app för att hämta användare till lokala AD och synkronisera dem till Azure AD med Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Hur jag för att föreslå förbättringar eller efterfråga nya funktioner som är relaterade till Workday och Azure AD-integration?

Din feedback är mycket värdefull eftersom det hjälper oss att ange riktning för framtida versioner och förbättringar. Vi välkomnar alla feedback och uppmuntrar dig att skicka in din idé eller förbättring förslag på den [feedback-forum för Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). För viss feedback som rör Workday-integrering, väljer du problemkategori *SaaS-program* och Sök med nyckelord *Workday* att hitta befintliga feedback som rör arbetsdagen.

![UserVoice SaaS Apps](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Kontrollera om du vill se om någon annan redan har föreslagits en liknande funktion när föreslå en ny idé. Du kan i så fall upp rösta funktion eller förbättring av begäran. Du kan även lämna en kommentar om din specifika användningsfall för att visa upp som stöd för tanken och demonstrera hur funktionen är värdefullt för dig för.

### <a name="provisioning-agent-questions"></a>Etablering av agenten frågor

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Vad är GA-versionen av agenten etablering?

* GA-versionen av agenten etablering är 1.1.30 och senare.
* Om din agentversionen är mindre än 1.1.30, kör du den offentliga förhandsversionen och den uppdateras automatiskt till GA-versionen om servern som är värd för agenten har .NET 4.7.1 runtime.
  * Du kan [Kontrollera vilken version av .NET](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) installerat på servern. Om servern inte kör .NET 4.7.1, kan du [ladda ned och installera .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). Din etableringsagenten uppdateras automatiskt till GA-versionen när du har installerat .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Hur vet jag för min etablering-agentens version?

* Logga in på Windows-server där etablering-agenten är installerad.
* Gå till **Kontrollpanelen** -> **avinstallera eller ändra ett Program** menyn
* Leta efter version som motsvarar posten **Microsoft Azure AD Connect etablering Agent**

  ![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft push etablering agentuppdateringar automatiskt?

Ja, kan Microsoft automatiskt uppdaterar etableringsagenten. Du kan inaktivera automatiska uppdateringar genom att stoppa tjänsten Windows **Microsoft Azure AD Connect-agenten Updater**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect"></a>Kan jag installera agenten etablering på samma server som kör AAD Connect?

Ja, du kan installera agenten etablering på samma server som kör AAD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Vid tidpunkten för konfiguration frågar etablering agenten efter autentiseringsuppgifter för Azure AD-administratör. Agenten lagrar autentiseringsuppgifterna lokalt på servern?

Under konfigurationen frågar etablering agenten efter autentiseringsuppgifter för Azure AD-administratören bara att ansluta till Azure AD-klienten. Den lagrar inte autentiseringsuppgifterna lokalt på servern. Men det behåller de autentiseringsuppgifter som används för att ansluta till den *lokala Active Directory-domän* i ett lokalt Windows-lösenord valv.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Hur konfigurerar jag att etablering agenten ska använda en proxyserver för utgående HTTP-kommunikation?

Etablering-Agent stöder användning av utgående proxy. Du kan konfigurera det genom att redigera konfigurationsfilen agenten **C:\Program Files\Microsoft Azure AD Connect etablering Agent\AADConnectProvisioningAgent.exe.config**. Lägg till följande rader i den genom att fram till slutet av filen precis före avslutande `</configuration>` tagg.
Ersätt variabler [proxyserver] och [Proxyport] med din Proxyservernamn och port värden.

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

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Hur undviker jag att att etablera-agenten kan kommunicera med Azure AD-klient och inga brandväggar blockerar portar som krävs av agenten?

Du kan också kontrollera om du har alla nödvändiga portar öppna genom att öppna den [anslutningsverktyget portar Test](https://aadap-portcheck.connectorporttest.msappproxy.net/) från ditt lokala nätverk. Mer gröna bockmarkeringar innebär större flexibilitet.

Om du vill se till att verktyget ger rätt resultat, måste du kontrollera att:

* Öppna verktyget i en webbläsare från servern där du har installerat agenten etablering.
* Se till att alla proxyservrar eller brandväggar som gäller för din etablering Agent används också till den här sidan. Detta kan göras i Internet Explorer genom att gå till **Inställningar -> Internet-alternativ -> anslutningar LAN-inställningar ->**. På den här sidan kan se du fältet ”Använd en Proxy Server för ditt LAN”. Välj den här rutan och placera Proxyadressen i fältet ”adress”.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Kan en Agent för etablering konfigureras för att etablera flera AD-domäner?

Ja, en etablering Agent kan konfigureras för att hantera flera AD-domäner som agenten har åtkomst till respektive domänkontrollanterna. Microsoft rekommenderar att konfigurera en grupp med 3 etablering agenter som betjänar samma uppsättning AD-domäner för att garantera hög tillgänglighet och ge support misslyckas.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Hur registrerar jag domänen som är associerade med min etablering Agent ta bort?

* Från Azure-portalen får de *klient-ID* av Azure AD-klienten.
* Logga in på den Windows-server som kör agenten etablering.
* Öppna powershell som Windows-administratör.
* Ändra till den katalog som innehåller registrerings-skript och kör följande kommandon ersätter den \[klient-ID\] parametern med värdet för din klient-ID.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* I listan med agenter som visas – Kopiera värdet för ”id” fältet från den här resursen vars *resourceName* är lika med ditt AD-domännamn.
* Klistra in ID: t i det här kommandot och kör det i Powershell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Kör guiden för konfiguration av agenten.
* Andra agenter som tidigare tilldelats till den här domänen måste konfigureras.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Hur avinstallerar jag etablering agenten?

* Logga in på Windows-server där etablering-agenten är installerad.
* Gå till **Kontrollpanelen** -> **avinstallera eller ändra ett Program** menyn
* Avinstallera följande program:
  * Microsoft Azure AD Connect Etableringsagenten
  * Microsoft Azure AD Connect agenten Updater
  * Microsoft Azure AD Connect agenten Etableringspaket

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Workday på AD-attributet mappnings- och -frågor

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Hur jag för att säkerhetskopiera eller exportera en fungerande kopia av min Workday etablering attributmappning och schemat?

Du kan använda Microsoft Graph API för att exportera konfigurationen Workday Användaretablering. Följ stegen i avsnittet [exportera och importera konfigurationen Workday etablering attributet användarmappning](#exporting-and-importing-your-configuration) mer information.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Jag har anpassade attribut i Workday och Active Directory. Hur konfigurerar jag lösningen att arbeta med min anpassade attribut?

Lösningen har stöd för anpassade attribut för Workday och Active Directory. Om du vill lägga till din anpassade attribut i mappningsschemat, öppna den **attributmappning** bladet och rulla ned till Expandera avsnittet **visa avancerade alternativ**. 

![Redigera attributlistan](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Om du vill lägga till din anpassade Workday-attribut, väljer du alternativet *redigera attributlista för Workday* och välja alternativet för att lägga till dina egna AD-attribut, *redigera attributlista för på lokala Active Directory*.

Se även:

* [Anpassning av listan över Workday-användarattribut](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Hur konfigurerar jag lösning för att endast uppdatera attribut i AD-baserad på Workday ändringar och skapa inte nya AD-konton?

Den här konfigurationen kan uppnås genom att ange den **åtgärder för målobjekt** i den **attributmappningar** bladet som visas nedan:

![Uppdatera åtgärd](./media/workday-inbound-tutorial/wd_target_update_only.png)

Markera kryssrutan ”Uppdatera” för uppdateringsåtgärder att flöda från Workday till AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Kan jag etablera användarens foto från Workday till Active Directory?

Lösningen för närvarande stöder inte binära attribut som *thumbnailPhoto* och *jpegPhoto* i Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Hur synkroniserar jag mobila nummer från Workday baserat på användarens medgivande för användning av offentlig?

* Gå bladet ”etablering” av appens etablering från Workday.
* Klicka på Attributmappningarna 
* Under **mappningar**väljer **synkronisera Workday arbetare till på lokala Active Directory** (eller **synkronisera Workday arbetare till Azure AD**).
* På sidan attributmappningar Bläddra nedåt och markera kryssrutan ”Visa avancerade alternativ”.  Klicka på **redigera attributlista för Workday**
* Leta upp attributet ”mobil” i bladet som öppnas, och klicka på raden så att du kan redigera den **API-uttryck** ![Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Ersätt den **API-uttryck** med följande nya uttryck som hämtar mobiltelefonnumret fungerar bara om ”offentliga användning flaggan” har värdet ”true” i Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Spara attributlistan.
* Spara attribut mappar.
* Avmarkera aktuell status och starta om den fullständiga synkroniseringen.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Hur formaterar namn som visas i AD baserat på användarens avdelning/land/stad attribut och referensen regionala avvikelser?

Det är ett vanligt krav du konfigurerar den *displayName* attribut i AD så att den innehåller även information om användarens avdelning och land. För t.ex. Om John Smith fungerar på marknadsföringsavdelningen i USA kan du sin *displayName* visas som *Smith, John (marknadsföring-US)*.

Här är hur du kan hantera sådana krav för att konstruera *CN* eller *displayName* att inkludera attribut, till exempel företag, affärsenhet, ort eller land.

* Varje attribut för Workday hämtas med hjälp av en underliggande API XPATH-uttryck, som kan konfigureras i **attributmappning -> Avancerad -> Redigera attributlistan för Workday**. Här är standard API XPATH-uttrycket för Workday *PreferredFirstName*, *PreferredLastName*, *företagets* och *SupervisoryOrganization* attribut.

     [!div class="mx-tdCol2BreakAll"]
     | Workday attribut | API-XPATH-uttryck |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Företag | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | WD:Worker / wd:Worker_Data / wd:Organization_Data / wd:Worker_Organization_Data / wd:Organization_Data [wd:Organization_Type_Reference / wd:ID [@wd:type= 'Organization_Type_ID ”] = 'Arbetsledning'] /wd:Organization_Name/text() |
  
   Bekräfta med Workday-teamet att API-uttryck ovan är giltig för dina Workday-klientkonfiguration. Om nödvändigt, du kan redigera dem enligt beskrivningen i avsnittet [anpassning av listan över Workday-användarattribut](#customizing-the-list-of-workday-user-attributes).

* På samma sätt landsinformationen i Workday hämtas med hjälp av följande XPATH: *wd:Worker / wd:Worker_Data / wd:Employment_Data / wd:Position_Data / wd:Business_Site_Summary_Data / wd:Address_Data / wd:Country_Reference*

     Det finns 5 land-relaterade attribut som är tillgängliga i avsnittet Workday attributet lista.

     | Workday attribut | API-XPATH-uttryck |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Bekräfta med Workday-teamet att API-uttryck ovanför är giltiga för dina Workday-klientkonfiguration. Om nödvändigt, du kan redigera dem enligt beskrivningen i avsnittet [anpassning av listan över Workday-användarattribut](#customizing-the-list-of-workday-user-attributes).

* Att skapa rätt attributet mappning uttryck, identifiera vilka Workday-attributet ”auktoritativt” representerar användarens förnamn, senaste namn, land och avdelning. Anta att attributen är *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* och *SupervisoryOrganization* respektive. Du kan använda detta för att skapa ett uttryck för AD *displayName* attributet på följande sätt för att få ett visningsnamn som *Smith, John (marknadsföring-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    När du har det högra uttrycket kan redigera tabellen attributmappningar och ändra den *displayName* attributmappning enligt nedan:   ![DisplayName mappning](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Utöka exemplet ovan ska vi say som du vill konvertera stadsnamn som kommer från Workday till snabbformat värden och sedan använda den för att skapa visningsnamn som *Smith, John (CHI)* eller *Doe, Jane (NYC)*, sedan detta kan åstadkommas med hjälp av ett Switch-uttryck med arbetsdagen *kommuner* attribut som den avgörande variabeln.

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
  * [Växla funktionens Syntax](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Ansluta till funktionens Syntax](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Lägg till funktionens Syntax](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Hur kan jag använda SelectUniqueValue för att generera unika värden för samAccountName-attribut

Anta att du vill generera unika värden för *samAccountName* attributet med hjälp av en kombination av *FirstName* och *LastName* attribut från Workday. Nedan är ett uttryck som du kan börja med:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
)
```

Så här fungerar det ovanstående uttrycket: Om användaren är John Smith, den först försöker generera JSmith, om JSmith redan finns, och sedan JoSmith, genereras om som finns genereras JohSmith. Uttrycket innebär också att det värde som genereras uppfyller begränsning av lösenordslängd och specialtecken begränsning som är associerade med *samAccountName*.

Se även:

* [Mid funktionens Syntax](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Ersätt funktionens Syntax](../manage-apps/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue Function Syntax](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Hur jag för att ta bort tecken med diakritiska tecken och konvertera dem till normal engelska bokstäver?

Använd funktionen [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics) för att ta bort specialtecken i förnamn och efternamn för användaren när e-postadress eller CN-värde för användaren.

## <a name="troubleshooting-tips"></a>Felsökningstips

Det här avsnittet innehåller specifika anvisningar om hur du felsöker etablering problem med dina Workday-integrering med Azure AD-granskningsloggar och loggarna i Loggboken i Windows Server. Det bygger på allmänna anvisningarna och begrepp som hämtats i den [självstudien: Rapportering om automatisk användarens kontoetablering](../manage-apps/check-status-user-account-provisioning.md)

Det här avsnittet beskriver följande aspekter av felsökning:

* [Konfigurera Windows Loggboken för agenten felsökning](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Konfigurera Azure-portalen granskningsloggar för felsökning av tjänsten](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Förstå loggar för AD-användarkonto skapa-åtgärder](#understanding-logs-for-ad-user-account-create-operations)
* [Förstå loggar för Manager uppdateringsåtgärder](#understanding-logs-for-manager-update-operations)
* [Lösa vanliga påträffade fel](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Konfigurera Windows Loggboken för agenten felsökning

* Logga in på Windows Server-datorn där agenten etablering har distribuerats
* Öppna **Loggboken i Windows Server** skrivbordsapp.
* Välj **Windows loggar > program**.
* Använd den **Filtrera aktuell logg...** alternativ för att visa alla händelser som loggats under källan **AAD. Connect.ProvisioningAgent** och utelämna händelser med händelse-ID ”5”, genom att ange filter ”-5” enligt nedan.

  ![Windows Loggboken](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Klicka på **OK** och sortera resultatvyn av **datum och tid** kolumn.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Konfigurera Azure-portalen granskningsloggar för felsökning av tjänsten

* Starta den [Azure-portalen](https://portal.azure.com), och navigera till den **granskningsloggar** avsnittet arbetsdagen etablering program.
* Använd den **kolumner** på sidan granskningsloggar för att visa endast följande kolumner i vyn (datum, aktivitet, Status, statusorsak). Den här konfigurationen garanterar att du koncentrera sig på data som är relevant för felsökning.

  ![Granska loggen kolumner](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Använd den **Target** och **datumintervall** frågeparametrar kan filtrera vyn. 
  * Ange den **Target** frågeparameter till ”Worker-ID” eller ”anställnings-ID” i Workday worker-objektet.
  * Ange den **datumintervall** till en lämplig tidsperiod för vilken du vill undersöka för fel eller problem med etablering.

  ![Granska loggen filter](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Förstå loggar för AD-användarkonto skapa-åtgärder

När en ny anställa i Workday har identifierats (vi antar att med anställnings-ID *21023*), Azure AD provisioning service-försök att skapa ett nytt AD-användarkonto för arbetaren och i processen skapar 4 granskningsloggposter enligt beskrivningen nedan:

  [![Granskningslogg skapa ops](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

När du klickar på någon av loggen granskningsposter i **aktivitetsinformation** sida öppnas. Här är vad den **aktivitetsinformation** visar sidan för varje posttyp för loggen.

* **Workday Import** post: Den här loggosten visar worker-information som hämtas från Workday. Använd informationen i den *ytterligare information* delen av loggpost att felsöka problem med data hämtas från Workday. En exempelpost visas nedan tillsammans med tips på hur du tolkar varje fält.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **AD-Import** post: Den här loggosten visar information för det konto som hämtats från AD. Eftersom det finns inget AD-konto under den inledande användargenereringen den *aktivitet statusorsak* indikerar att inget konto med attributvärdet matchar ID hittades i Active Directory. Använd informationen i den *ytterligare information* delen av loggpost att felsöka problem med data hämtas från Workday. En exempelpost visas nedan tillsammans med tips på hur du tolkar varje fält.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Öppna Windows Loggboken för att hitta etablering agenten loggposter som motsvarar den här importåtgärden AD, och Använd den **hitta...** menyalternativet ta loggposter som innehåller matchande ID/ansluta attributet egenskapsvärdet (i det här fallet *21023*).

  ![Hitta](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Söker efter posten med *händelse-ID = 9*, vilket ger du LDAP söka filter som används av agenten för att hämta AD-konto. Du kan kontrollera om det här är rätt sökfilter för att hämta unika användarposter.

  ![LDAP-sökning](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Den post som följer direkt efter den med *händelse-ID = 2* fångar resultatet av sökåtgärden och om det returnerade inga resultat.

  ![LDAP-resultat](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **Synkroniseringsregelåtgärd** post: Den här loggosten visar resultatet av regler för mappning av attribut och konfigurerad Omfångsfilter tillsammans med etablering åtgärden som ska vidtas för att bearbeta inkommande Workday-händelsen. Använd informationen i den *ytterligare information* delen av loggpost att felsöka problem med synkroniseringsåtgärd. En exempelpost visas nedan tillsammans med tips på hur du tolkar varje fält.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Om det finns problem med din attributet mappning uttryck eller inkommande data i Workday har problem (till exempel: tomt eller null-värde för obligatoriska attribut), och du kommer se ett fel i det här skedet med den felkod som tillhandahåller information om felet.

* **AD-Export** post: Den här loggosten visar resultatet av AD-konto skapas igen tillsammans med de attributvärden som ställts in i processen. Använd informationen i den *ytterligare information* delen av loggpost att felsöka problem med kontot för att skapa. En exempelpost visas nedan tillsammans med tips på hur du tolkar varje fält. I avsnittet ”Mer information”, ”EventName” anges till ”EntryExportAdd”, ”JoiningProperty” anges till värdet för attributet matchar ID, ”SourceAnchor” anges till den WorkdayID (WID) som är associerade med posten och ”TargetAnchor” är inställt på den värdet för AD ”ObjectGuid” attributet för den nyligen skapade användaren. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Öppna Windows Loggboken för att hitta etablering agenten loggposter som motsvarar den här exportåtgärden AD, och Använd den **hitta...** menyalternativet ta loggposter som innehåller matchande ID/ansluta attributet egenskapsvärdet (i det här fallet *21023*).  

  Leta efter en HTTP POST-post som motsvarar en tidsstämpel för exportåtgärden med *händelse-ID = 2*. Den här posten kommer att innehålla de attributvärden som skickas av etableringstjänsten till etableringsagenten.

  [![SCIM Add](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Omedelbart efter händelsen ovan bör det finnas en annan händelse som registrerar svaret för att skapa AD-kontot igen. Den här händelsen returnerar den nya objectGuid som skapats i AD och det är angivet som attributet TargetAnchor i etableringstjänsten.

  [![SCIM Add](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Förstå loggar för manager uppdateringsåtgärder

Attributet manager är ett referensattribut i AD. Etableringstjänsten ställer inte in attributet manager som en del av åtgärden för att skapa användaren. I stället manager attributet anges som en del av en *uppdatera* igen när AD-konto har skapats för användaren. Expandera i exemplet ovan, vi antar att en ny anställa med anställnings-ID ”21451” är aktiverad i Workday och den nya anställa manager (*21023*) har redan ett AD-konto. I det här scenariot visas söka granskningsloggarna för användaren 21451 5 poster.

  [![Manager-uppdatering](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

De första 4 posterna är som de som vi utforskat som en del av användaren för att skapa. 5-post är exporten som är associerade med manager attributet-uppdatering. Loggposten visas resultatet av AD-konto manager uppdateringsåtgärden, vilket utförs med hjälp av manager *objectGuid* attribut.

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

### <a name="resolving-commonly-encountered-errors"></a>Lösa vanliga påträffade fel

Det här avsnittet beskrivs ofta upptäckta fel med Workday användaretablering och hur det ska lösas. Felen grupperas enligt följande:

* [Etablering agentfel](#provisioning-agent-errors)
* [Anslutningsfel](#connectivity-errors)
* [AD användarfel konto skapas](#ad-user-account-creation-errors)
* [AD användarfel konto update](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Etablering agentfel

|#|Error Scenario |Troliga orsaker|Rekommenderad upplösning|
|--|---|---|---|
|1.| Det gick inte att installera etableringsagenten med felmeddelandet:  *Tjänsten ”Microsoft Azure AD Connect etablering Agent” (AADConnectProvisioningAgent) kunde inte starta. Kontrollera att du har behörighet att starta systemet.* | Det här felet visas vanligtvis om du försöker installera etableringsagenten på en domänkontrollant och grupprinciper som förhindrar att tjänsten startar.  Den visas också om du har en tidigare version av agenten som körs och du inte har avinstallerat den innan du startar en ny installation.| Installera etableringsagenten på en icke-DC-servern. Se till att tidigare versioner av agenten avinstalleras innan du installerar den nya agenten.|
|2.| Windows-tjänsten ”Microsoft Azure AD Connect etablering Agent” är i *startar* tillstånd och växlar inte till *kör* tillstånd. | Som en del av installationen, agenten guiden skapar ett lokalt konto (**NT-tjänst\\AADConnectProvisioningAgent**) på servern och detta är den **logga in** konto som används för att starta den tjänsten. Om en säkerhetsprincip på din Windows server förhindrar att lokala konton som kör tjänsterna, kommer du får det här felet. | Öppna den *tjänstekonsolen*. Högerklicka på Windows-tjänsten ”Microsoft Azure AD Connect etablering Agent' och fliken inloggning ange kontot för en domänadministratör att köra tjänsten. Starta om tjänsten. |
|3.| När du konfigurerar etableringsagenten med din AD-domän i steget *Anslut Active Directory*, guiden tar lång tid vid inläsning av AD-schemat och slutligen når sin tidsgräns. | Det här felet visas vanligtvis om det inte går att kontakta AD-domänkontrollantservern på grund av problem med brandväggen. | På den *Anslut Active Directory* guiden kan du få tillgång till autentiseringsuppgifterna för din AD-domän, det finns ett alternativ som heter *Välj domain controller prioritet*. Använd det här alternativet för att välja en domänkontrollant som är på samma plats som agent-servern och se till att det finns inga brandväggsregler som blockerar kommunikationen. |

#### <a name="connectivity-errors"></a>Anslutningsfel

Om etableringstjänsten kan inte ansluta till Workday eller Active Directory, kan det orsaka etablering för att gå till tillståndet har satts i karantän. Använd tabellen nedan för att felsöka anslutningsproblem.

|#|Error Scenario |Troliga orsaker|Rekommenderad upplösning|
|--|---|---|---|
|1.| När du klickar på **Testanslutningen**, du får felmeddelandet: *Det uppstod ett fel vid anslutning till Active Directory. Kontrollera att den lokala etablering agenten körs och den är konfigurerad med rätt Active Directory-domän.* | Det här felet vanligtvis visas om etableringen agent inte körs eller om det finns en brandvägg som blockerar kommunikation mellan Azure AD och etableringsagenten. Du kan också se det här felet om domänen inte har konfigurerats i guiden Agent. | Öppna den *Services* konsolen på Windows-server för att kontrollera att agenten körs. Öppna guiden etablering agent och bekräfta att rätt domän är registrerat med agenten.  |
|2.| Etableringsjobbet för den försätts i karantän tillstånd under helger (fre Sat) och vi får ett e-postmeddelande som att det finns ett fel med synkroniseringen. | En av de vanligaste orsakerna till felet är planerade Workday-driftstopp. Om du använder en klient för implementering av Workday ska du notera att Workday har schemalagt driftstopp för sina implementeringsklienter under helger (vanligtvis från fredag kväll till lördag morgon). Under den perioden kan Workday-etableringsappar försättas i karantäntillstånd eftersom det inte går att ansluta till Workday. Workday återfår sitt normala tillstånd när Workday-implementeringsklienten är online igen. I sällsynta fall kan du också se det här felet om lösenordet för integreringssystemanvändaren har ändrats på grund av uppdatering av klienten eller om kontot är låst eller har upphört att gälla. | Kontakta din Workday-administratör eller integreringspartner för att höra efter när Workday schemalägger driftstopp. Sedan kan du ignorera varningsmeddelanden under driftstoppsperioden och få en bekräftelse om tillgänglighet när Workday-instansen är online igen.  |


#### <a name="ad-user-account-creation-errors"></a>AD användarfel konto skapas

|#|Error Scenario |Troliga orsaker|Rekommenderad upplösning|
|--|---|---|---|
|1.| Exportera åtgärden fel i granskningsloggen med meddelandet *fel: OperationsError-SvcErr: Ett åtgärdsfel inträffade. Ingen överordnad referens har konfigurerats för katalogtjänsten. Katalogtjänsten är därför det gick inte att utfärda hänvisningar till objekt utanför den här skogen.* | Det här felet visas vanligtvis om den *Active Directory-behållare* Organisationsenheten är inte korrekt eller om det finns problem med mappning av uttryck som används för *parentDistinguishedName*. | Kontrollera den *Active Directory-behållare* OU-parametern för stavfel. Om du använder *parentDistinguishedName* i attributmappningen kontrollerar du att det alltid utvärderas till en känd container i AD-domänen. Kontrollera den *exportera* händelse i granskningen loggar finns i det genererade värdet. |
|2.| Exportera åtgärden fel i granskningsloggen med felkoden: *SystemForCrossDomainIdentityManagementBadResponse* och meddelandet *fel: ConstraintViolation-AtrErr: Ett värde i begäran är ogiltig. Ett värde för attributet var inte inom det godkända intervallet för värden. \nError information: CONSTRAINT_ATT_TYPE - företagets*. | Även om det här felet är specifik för den *företagets* attribut, kanske du ser detta fel för andra attribut som *CN* samt. Det här felet visas på grund av AD tvingande schema-begränsning. Som standard attribut som *företagets* och *CN* i AD har en övre gräns på 64 tecken. Om värdet kommer från Workday är fler än 64 tecken, ser du det här felmeddelandet. | Kontrollera den *exportera* händelsen i granskningsloggarna vill visa värdet för attributet som rapporterats i felmeddelandet. Överväg att trunkera värdet kommer från Workday med hjälp av den [Mid](../manage-apps/functions-for-customizing-application-data.md#mid) funktionen eller ändra mappningarna till ett AD-attribut som inte har liknande längdkravet.  |

#### <a name="ad-user-account-update-errors"></a>AD användarfel konto update

Under uppdateringen för AD user-konto läser information från både Workday och AD etableringstjänsten, kör attributet mappningsregler och avgör om någon ändra behov ska börja gälla. Därefter utlöses en update-händelse. Om de här stegen påträffar ett fel loggas det i granskningsloggarna. Använd tabellen nedan för att felsöka vanliga fel med uppdateringen.

|#|Error Scenario |Troliga orsaker|Rekommenderad upplösning|
|--|---|---|---|
|1.| Synkroniseringsfel regeln åtgärd i granskningsloggen med meddelandet *EventName = EntrySynchronizationError och ErrorCode = EndpointUnavailable*. | Det här felet visas om etableringstjänsten kan inte hämta data för användarprofiler från Active Directory på grund av ett fel påträffades av lokalt etableringsagenten. | Kontrollera loggarna i Loggboken för etablering agenten efter fel-händelser som indikerar problem med Läsåtgärden (filtrera efter händelse-ID #2). |
|2.| Chefsattribut i AD uppdateras inte för vissa användare i AD. | Den mest troliga orsaken till felet är om du använder reglerna och användarens chef ingår inte i omfånget. Du kan också köra i det här problemet om chefens matchande ID-attribut (t.ex. EmployeeID) hittades inte i mål-AD-domän eller inte har angetts till rätt värde. | Granska Omfångsfilter och Lägg till användaren i omfånget. Kontrollera den manager-profil i AD för att se till att det finns ett värde för attributet matchande ID. |

## <a name="managing-your-configuration"></a>Hantera din konfiguration

Det här avsnittet beskrivs hur du kan ytterligare utöka, anpassa och hantera dina Workday-drivna användaretablering konfiguration. Det innehåller följande avsnitt:

* [Anpassning av listan över Workday-användarattribut](#customizing-the-list-of-workday-user-attributes)  
* [Exportera och importera konfigurationen](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Anpassning av listan över Workday-användarattribut

Workday tillhandahållning av appar för Active Directory och Azure AD som båda innehåller en standardlistan över Workday-användarattribut som du kan välja mellan. De här listorna är dock inte omfattande. Workday har stöd för flera hundra möjliga användarattribut som kan vara antingen standard eller unikt för din Workday-klient.

Den Azure AD-etableringstjänsten stöder möjligheten att anpassa din lista eller Workday-attributet för att inkludera alla attribut som visas i den [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) driften av personal-API.

Om du vill göra den här ändringen, måste du använda [Workday Studio](https://community.workday.com/studio-download) att extrahera XPath-uttryck som representerar de attribut som du vill använda och Lägg sedan till dem i din allokering konfiguration med hjälp av avancerade Attributredigeraren i Azure portal .

**Att hämta ett XPath-uttryck för ett Workday-användarattribut:**

1. Ladda ned och installera [Workday Studio](https://community.workday.com/studio-download). Du behöver en Workday community-konto för åtkomst till installationsprogrammet.

2. Hämta Workday Human_Resources WSDL-filen från denna URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Starta Workday Studio.

4. I kommandofältet, Välj den **Workday > Test-webbtjänsten i Tester** alternativet.

5. Välj **externa**, och välj Human_Resources WSDL-filen som du hämtade i steg 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Ange den **plats** fältet `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, men ersätt ”IMPL-CC” med den faktiska instanstyp och ”KLIENTORGANISATION” med ditt verkliga klientnamn.

7. Ange **åtgärden** till **Get_Workers**

8.  Klicka på lilla **konfigurera** länkarna begäran/svar-fönster för att ställa in dina Workday-autentiseringsuppgifter. Kontrollera **autentisering**, och ange användarnamn och lösenord för system för Workday integrationskontot. Se till att formatera användarnamnet som namn\@klient- och lämna den **WS-Security UsernameToken** alternativ som valts.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Välj **OK**.

10. I den **begära** fönstret klistra in XML-filen nedan och ange **Employee_ID** till anställnings-ID för en verklig användare i din Workday-klient. Välj en användare som har attributet fylls i som du vill extrahera.

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

11. Klicka på den **skicka förfrågan** (grön pil) för att köra kommandot. Om autentiseringen lyckas kan svaret ska visas i den **svar** fönstret. Kontrollera svaret för att se till att den har data av användar-ID som du angav och inte ett fel.

12. Om detta lyckas, kopiera XML-filen från den **svar** fönstret och spara det som en XML-fil.

13. I kommando-fältet i Studio för Workday Välj **fil > Öppna fil...**  och öppna XML-filen som du sparade. Den här åtgärden kommer att öppna filen i Workday Studio XML-redigerare.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. I trädet filen navigerar   **/ENV: Kuvert > env: Brödtext > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** att hitta dina användares data.

15. Under **wd: Worker**, hitta det attribut som du vill lägga till och markera den.

16. Kopiera XPath-uttrycket för din valda attributet av den **dokumentsökväg** fält.

17. Ta bort den **/env:Envelope / env:Body / wd:Get_Workers_Response / wd:Response_Data /** prefixet från det kopierade uttrycket.

18. Om det sista objektet i det kopierade uttrycket är en nod (exempel ”: / wd: Birth_Date ”), lägger till **/text()** i slutet av uttrycket. Detta är inte nödvändigt om det sista objektet är ett attribut (exempel ”:/@wd: typen”).

19. Resultatet bör vara något som liknar `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Det här värdet är vad du kommer att kopiera till Azure-portalen.

**Lägga till din anpassade Workday-användarattribut i konfigurationen av etablering:**

1. Starta den [Azure-portalen](https://portal.azure.com), och gå till avsnittet etablering arbetsdagen etablering program, vilket beskrivs tidigare i den här självstudien.

2. Ange **Etableringsstatus** till **av**, och välj **spara**. Det här steget hjälper dig att se till att ändringarna träder i kraft endast när du är klar.

3. Under **mappningar**väljer **synkronisera Workday arbetare till på lokala Active Directory** (eller **synkronisera Workday arbetare till Azure AD**).

4. Bläddra längst ned på nästa skärm och välj **visa avancerade alternativ**.

5. Välj **redigera attributlista för Workday**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Rulla till slutet av attributlistan där indatafält finns.

7. För **namn**, ange ett namn för din attributet.

8. För **typ**, Välj typ som motsvarar din attributet på rätt sätt (**sträng** är de vanligaste).

9. För **API-uttryck**, ange XPath-uttryck som du kopierade från Workday Studio. Exempel: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Välj **Lägg till attribut**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Välj **spara** ovan, och sedan **Ja** till dialogrutan. Stäng attributmappning skärmen om det fortfarande är öppen.

12. Tillbaka på huvudsidan **etablering** fliken **synkronisera Workday arbetare till på lokala Active Directory** (eller **synkronisera arbetare till Azure AD**) igen.

13. Välj **Lägg till ny mappning**.

14. Din nya attributet bör nu visas i den **källattribut** lista.

15. Lägg till en mappning för din nya attribut som du vill.

16. När du är klar, Kom ihåg att ange **Etableringsstatus** tillbaka till **på** och spara.

### <a name="exporting-and-importing-your-configuration"></a>Exportera och importera konfigurationen

Det här avsnittet beskriver hur du använder Microsoft Graph API och Graph-testaren att exportera dina Workday etablering attributmappningar och schemat till en JSON-fil och importera den tillbaka till Azure AD.

#### <a name="step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id"></a>Steg 1: Hämta dina Workday etablering App ID för tjänstens huvudnamn (objekt-ID)

1. Starta den [Azure-portalen](https://portal.azure.com), och gå till avsnittet Egenskaper arbetsdagen etablering program.
1. I avsnittet Egenskaper i din allokering app kopiera GUID-värde som är associerade med den *objekt-ID* fält. Det här värdet kallas även den **ServicePrincipalId** för din App och den kommer att användas i Graph-testaren åtgärder.

   ![ID för workday App tjänstens huvudnamn](./media/workday-inbound-tutorial/wd_export_01.png)

#### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Steg 2: Logga in på Microsoft Graph-testaren

1. Starta [Microsoft Graph-testaren](https://developer.microsoft.com/graph/graph-explorer)
1. Klicka på knappen ”Logga In med Microsoft” och logga in med autentiseringsuppgifter för Azure AD Global administratör eller App.

    ![Graph-inloggning](./media/workday-inbound-tutorial/wd_export_02.png)

1. När inloggningen lyckas visas kontoinformation för användaren i det vänstra fönstret.

#### <a name="step-3-retrieve-the-provisioning-job-id-of-the-workday-provisioning-app"></a>Steg 3: Hämta appen för Workday-etablering etablering jobb-ID

I Microsoft Graph-testaren kör du följande GET fråga ersätta [servicePrincipalId] med den **ServicePrincipalId** extraheras från den [steg 1](#step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Du får ett svar som visas nedan. Kopiera attributet ”id” finns i svaret. Det här värdet är den **ProvisioningJobId** och används för att hämta schemametadata för det underliggande.

   [![Etablering jobb-Id](./media/workday-inbound-tutorial/wd_export_03.png)](./media/workday-inbound-tutorial/wd_export_03.png#lightbox)

#### <a name="step-4-download-the-provisioning-schema"></a>Steg 4: Hämta etablering schemat

I Microsoft Graph-testaren kör följande fråga för GET, vilket ersätter [servicePrincipalId] och [ProvisioningJobId] med ServicePrincipalId och ProvisioningJobId som du hämtade i föregående steg.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopiera JSON-objekt från svaret och spara den till en fil för att skapa en säkerhetskopia av schemat.

#### <a name="step-5-import-the-provisioning-schema"></a>Steg 5: Importera etablering schemat

> [!CAUTION]
> Utföra det här steget endast om du vill ändra schemat för konfiguration som inte kan ändras med hjälp av Azure portal eller om du behöver återställa konfigurationen från en tidigare säkerhetskopierade filen med giltig och fungerande schema.

I Microsoft Graph-testaren, konfigurera följande PUT-fråga, vilket ersätter [servicePrincipalId] och [ProvisioningJobId] med ServicePrincipalId och ProvisioningJobId som du hämtade i föregående steg.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopiera innehållet i JSON-schema-filen på fliken ”brödtext i begäran”.

   [![Brödtext i begäran](./media/workday-inbound-tutorial/wd_export_04.png)](./media/workday-inbound-tutorial/wd_export_04.png#lightbox)

På fliken ”begärandehuvuden” Lägg till Content-Type-huvud-attributet med värdet ”application/json”

   [![Begärandehuvuden](./media/workday-inbound-tutorial/wd_export_05.png)](./media/workday-inbound-tutorial/wd_export_05.png#lightbox)

Klicka på knappen ”Kör fråga” för att importera det nya schemat.

## <a name="managing-personal-data"></a>Hantera personliga data

Workday etablera lösningen för Active Directory kräver en etableringsagenten installeras på en lokal Windows server och den här agenten skapar loggar i Windows-händelseloggen som kan innehålla personuppgifter beroende på dina Workday till AD-attribut mappningar. För att uppfylla användaren sekretess skyldigheter, kan du se till att inga data sparas i den händelse att loggarna 48 timmar genom att konfigurera en Windows schemalagda aktiviteten för att rensa händelseloggen.

Den Azure AD-etableringtjänsten hamnar i den **registerförare** kategorin för GDPR-klassificering. Som en registerförare pipeline tillhandahåller tjänsten bearbetning av tjänster till viktiga partner och konsumenter i slutet. Azure AD-etableringtjänsten genererar inte användardata och har ingen oberoende kontroll över vilka personliga data samlas in och hur de används. Datahämtning, aggregering, analys och rapportering i Azure AD-etableringtjänsten baseras på befintliga enterprise-data.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

Med avseende på kvarhållning av data, i Azure AD-etableringtjänsten inte generera rapporter, utföra analyser eller ge insikter äldre än 30 dagar. Azure AD-etableringtjänsten därför inte lagrar, bearbetar eller kvarhålla data äldre än 30 dagar. Den här designen är kompatibel med GDPR bestämmelser, Microsoft sekretessbestämmelser för efterlevnad och Azure AD-principer för kvarhållning av data.

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan Workday och Azure Active Directory](workday-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)
* [Lär dig hur du använder Microsoft Graph API: er för att hantera konfigurationer av etablering](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
