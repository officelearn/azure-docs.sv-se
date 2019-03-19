---
title: Distribuera ett hybridmoln med Azure och Azure Stack | Microsoft Docs
description: Lär dig hur du distribuerar ett hybridmoln med Azure och Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 336a2a3fd98f7829694eb095ff2646d9d361afd3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097328"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>Självstudier: Distribuera en lösning för hybridmoln med Azure och Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Den här självstudien visar hur du distribuerar en lösning för hybridmoln som använder det offentliga Azure-molnet och det privata molnet i Azure Stack.

Genom att använda en hybridmolnlösning kan kombinera du efterlevnad fördelarna med ett privat moln med skalbarheten hos det offentliga molnet. Utvecklarna kan dessutom dra nytta av Microsofts utvecklare ekosystem och använda sina kunskaper till molnet och lokala miljöer.

## <a name="overview-and-assumptions"></a>Översikt och förutsättningar

Du kan följa den här kursen och konfigurera ett arbetsflöde som ger utvecklare möjlighet att distribuera en identisk webbapp till ett offentligt moln och ett privat moln. Det här programmet kommer att kunna komma åt en icke-Internet dirigerbara nätverk som finns på det privata molnet. Dessa webbprogram som övervakas och när det finns en topp i trafiken, ett program ändrar du DNS-poster för att omdirigera trafik till det offentliga molnet. När trafik sjunker till nivå innan insamling, dirigeras trafiken tillbaka till det privata molnet.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> - Distribuera en hybrid-anslutna SQL Server-databasserver.
> - Ansluta en webbapp i globala Azure till en hybridnätverk.
> - Konfigurera DNS för att skala i molnet.
> - Konfigurera SSL-certifikat för att skala i molnet.
> - Konfigurera och distribuera webbprogrammet.
> - Skapa en Traffic Manager-profil och konfigurera den för att skala i molnet.
> - Konfigurera Application Insights, övervakning och avisering för ökad trafik.
> - Konfigurera automatisk trafiken växlar mellan globala Azure och Azure Stack.

### <a name="assumptions"></a>Antaganden

Den här självstudien förutsätter att du har grundläggande kunskaper i globala Azure och Azure Stack. Om du vill veta mer innan du påbörjar den här självstudiekursen kan du läsa de här artiklarna:

 - [Introduktion till Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Viktiga begrepp för Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

Den här självstudien förutsätter också att du har en Azure-prenumeration. Om du inte har någon prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien måste du kontrollera att du uppfylla följande krav:

- Ett Azure Stack Development Kit (ASDK) eller en prenumeration på ett Azure Stack-integrerat System. Om du vill distribuera ett Azure Stack Development Kit, följer du anvisningarna i [distribuera ASDK med hjälp av installationsprogrammet](../asdk/asdk-deploy.md).
- Azure Stack-installationen ska ha följande installerat:
  - Azure App Service. Arbeta med Azure Stack-operatör att distribuera och konfigurera Azure App Service på din miljö. Den här självstudien kräver App Service har minst en (1) tillgängliga dedikerade worker-roll.
  - En avbildning av Windows Server 2016
  - En Windows Server 2016 med en Microsoft SQL Server-avbildning
  - Rätt planer och erbjudanden
  - Ett domännamn för ditt webbprogram. Om du inte har ett domännamn kan du köpa en från en domänleverantör som GoDaddy, Bluehost och InMotion.
- Ett SSL-certifikat för din domän från en betrodd certifikatutfärdare, till exempel LetsEncrypt.
- Ett webbprogram som kommunicerar med en SQL Server-databas och har stöd för Application Insights. Du kan ladda ned den [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) exempelapp från GitHub.
- Hybridnätverk mellan en Azure-nätverk och Azure Stack-nätverk. Detaljerade anvisningar finns i [konfigurera molnet hybridanslutning med Azure och Azure Stack](azure-stack-solution-hybrid-connectivity.md).

- En hybrid kontinuerlig integrering/kontinuerlig distribution (CI/CD) pipeline med en privat skapandeagent på Azure Stack. Detaljerade anvisningar finns i [konfigurera hybrid cloud-identitet med Azure och Azure Stack-program](azure-stack-solution-hybrid-identity.md)

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>Distribuera en hybrid-anslutna SQL Server-databasserver

1. Logga in på användarportalen för Azure Stack.

2. På den **instrumentpanelen**väljer **Marketplace**.

    ![Azure Stack Marketplace](media/azure-stack-solution-hybrid-cloud/image1.png)

3. I **Marketplace**väljer **Compute**, och välj sedan **mer**. Under **mer**väljer den **kostnadsfri SQL Server-licens: SQL Server 2017 Developer på Windows Server** bild.

    ![Välj en avbildning av virtuell dator](media/azure-stack-solution-hybrid-cloud/image2.png)

4. På **kostnadsfri SQL Server-licens: SQL Server 2017 Developer på Windows Server** Välj **skapa**.

5. På **grunderna > Konfigurera grundläggande inställningar**, ange en **namn** för den virtuella datorn (VM), en **användarnamn** för SQL Server-SA, och en **lösenord** för SA.  Från den **prenumeration** listrutan väljer du den prenumeration som du distribuerar till. För **resursgrupp**, använda **Välj befintligt** och placera den virtuella datorn i samma resursgrupp som din Azure Stack webbapp.

    ![Konfigurera grundläggande inställningar för virtuell dator](media/azure-stack-solution-hybrid-cloud/image3.png)

6. Under **storlek**, Välj en storlek för den virtuella datorn. Den här självstudien rekommenderar vi A2_Standard eller en DS2_V2_Standard.

7. Under **Inställningar > Konfigurera valfria funktioner**, konfigurera följande inställningar:

   - **Storage-konto**. Skapa ett nytt konto om du behöver en.
   - **Virtuellt nätverk**

     > [!Important]  
     > Kontrollera att SQL Server-dator har distribuerats på samma virtuella nätverk som VPN-gatewayer.

   - **Offentlig IP-adress**. Du kan använda standardinställningarna.
   - **Nätverkssäkerhetsgrupp** (NSG). Skapa en ny NSG.
   - **Tillägg och övervakning**. Behåll standardinställningarna.
   - **Diagnostiklagringskonto**. Skapa ett nytt konto om du behöver en.
   - Välj **OK** att spara din konfiguration.

     ![Konfigurera valfria funktioner](media/azure-stack-solution-hybrid-cloud/image4.png)

1. Under **SQL Server-inställningar**, konfigurera följande inställningar:
   - För **SQL-anslutning**väljer du att **offentlig (Internet)**.
   - För **Port**, behåller du standardvärdet **1433**.
   - För **SQL-autentisering**väljer **aktivera**.

     > [!Note]  
     > När du aktiverar SQL-autentisering, det bör automatisk ifyllning med ”SQLAdmin” information som du konfigurerade i **grunderna**.

   - Behåll standardinställningarna för resten av inställningarna. Välj **OK**.

     ![Konfigurera SQL Server-inställningar](media/azure-stack-solution-hybrid-cloud/image5.png)

9. På **sammanfattning**, granska konfigurationen av virtuella datorn och välj sedan **OK** att starta distributionen.

    ![Konfigurationssammanfattning](media/azure-stack-solution-hybrid-cloud/image6.png)

10. Det tar lite tid att skapa den nya virtuella datorn. Du kan visa STATUSEN för dina virtuella datorer i **virtuella datorer**.

    ![Virtuella datorer](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>Skapa webbappar i Azure och Azure Stack

Azure App Service gör det enklare att köra och hantera ett webbprogram. Eftersom Azure Stack är konsekvent med Azure kan App Service köras i båda miljöerna. Du använder App Service som värd för ditt program.

### <a name="create-web-apps"></a>Skapa webbappar

1. Skapa en webbapp i Azure genom att följa instruktionerna i [hantera en App Service-plan i Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan). Kontrollera att du lägger till webbappen i samma prenumeration och resursgrupp som din hybridnätverk.

2. Upprepa föregående steg (1) i Azure Stack.

### <a name="add-route-for-azure-stack"></a>Lägga till väg för Azure Stack

App Service på Azure Stack måste vara dirigerbara från det offentliga Internet så att användare kan komma åt ditt program. Om din Azure Stack är tillgänglig från Internet, anteckna den offentliga IP-adressen eller URL för Azure Stack webbapp.

Om du använder en ASDK, kan du [konfigurera en statisk NAT-mappning](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-vpn-connection-one-node#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal) att exponera App Service utanför den virtuella miljön.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Ansluta en webbapp i Azure till ett hybridnätverk

Att tillhandahålla anslutningsbarhet mellan webbservergrupp i Azure och SQL Server-databas i Azure Stack webbappen anslutas till hybridnätverk mellan Azure och Azure Stack. Om du vill aktivera anslutning, måste du:

- Konfigurera punkt-till-plats-anslutning
- Konfigurera webbappen
- Ändra den lokala nätverksgatewayen i Azure Stack.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>Konfigurera Azure-nätverket för punkt-till-plats-anslutning

Den virtuella nätverksgatewayen i Azure-sidan av hybridnätverk måste tillåta punkt-till-plats-anslutningar att integrera med Azure App Service.

1. Gå till sidan gateway för virtuellt nätverk i Azure. Under **inställningar**väljer **punkt-till-plats-konfiguration**.

    ![Punkt-till-plats-alternativ](media/azure-stack-solution-hybrid-cloud/image8.png)

2. Välj **konfigurera nu** konfigurera punkt-till-plats.

    ![Starta punkt-till-plats-konfiguration](media/azure-stack-solution-hybrid-cloud/image9.png)

3. På den **punkt-till-plats** konfigurationen anger du den privata IP-adressintervall som du vill använda i **-adresspoolen**.

   > [!Note]  
   > Se till att det intervall du anger inte överlappar något av adressintervallen som redan används av undernät i de globala Azure eller Azure Stack-komponenterna i hybridnätverk.

   Under **Tunneltyp**, avmarkera de **IKEv2 VPN**. Välj **spara** att slutföra konfigurationen av punkt-till-plats.

   ![Inställningar för punkt-till-plats](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>Integrera Azure App Service-programmet med hybridnätverk

1. För att ansluta programmet till Azure VNet, följer du anvisningarna i [aktiverar VNet-integrering](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#enabling-vnet-integration).

2. Gå till **inställningar** för App Service-planen som värdar för webbprogrammet. I **inställningar**väljer **nätverk**.

    ![Konfigurera nätverk](media/azure-stack-solution-hybrid-cloud/image11.png)

3. I **VNET-integrering**väljer **Klicka här för att hantera**.

    ![Hantera VNET-integrering](media/azure-stack-solution-hybrid-cloud/image12.png)

4. Välj det virtuella nätverk som du vill konfigurera. Under **IP-adresser DIRIGERAS till VNET**, anger du IP-adressintervallet för Azure VNet, Azure Stack VNet och adressutrymmen för punkt-till-plats. Välj **spara** validera och spara inställningarna.

    ![IP-adressintervall för att dirigera](media/azure-stack-solution-hybrid-cloud/image13.png)

Läs mer om hur App Service kan integreras med virtuella Azure-nätverk i [integrera din app med Azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet).

### <a name="configure-the-azure-stack-virtual-network"></a>Konfigurera det virtuella nätverket i Azure Stack

Den lokala nätverksgatewayen i Azure Stack-nätverk måste konfigureras för att dirigera trafik från adressintervallet för punkt-till-plats App Service.

1. I Azure Stack, går du till **lokal nätverksgateway**. Under **Inställningar** väljer du **Konfiguration**.

    ![Alternativ för gateway](media/azure-stack-solution-hybrid-cloud/image14.png)

2. I **adressutrymme**, ange adressintervallet för punkt-till-plats för den virtuella nätverksgatewayen i Azure.l Välj **spara** validera och spara den här konfigurationen.

    ![Adressutrymme för punkt-till-plats](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>Konfigurera DNS för att skala i molnet

Genom att konfigurera DNS för program i molnet, kan användare komma åt den globala Azure och Azure Stack instanser av din webbapp. DNS-konfigurationen för den här självstudiekursen kan Azure Traffic Manager dirigera trafik när belastningen ökar eller minskar.

Den här självstudien använder Azure DNS hanterar DNS. (App Service-domäner fungerar inte.)

### <a name="create-subdomains"></a>Create subdomains

Eftersom Traffic Manager är beroende av DNS-CNAME-poster, behövs en underdomän för att dirigera trafik korrekt till slutpunkter. Läs mer om DNS-poster och domänmappning [mappa domäner med Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)

Slutpunkten ska du skapa en underdomän som användare kan använda att få åtkomst till din webbapp för Azure. Den här självstudien kan använda **app.northwind.com**, men du bör anpassa det här värdet baserat på din egen domän.

Du måste också skapa en underdomän med en A-post för Azure Stack-slutpunkten. Du kan använda **azurestack.northwind.com**.

### <a name="configure-a-custom-domain-in-azure"></a>Konfigurera ett anpassat domännamn i Azure

1. Lägg till den **app.northwind.com** värdnamnet till Azure-webbapp med [mappa en CNAME-post till Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

### <a name="configure-custom-domains-in-azure-stack"></a>Konfigurera anpassade domäner i Azure Stack

1. Lägg till den **azurestack.northwind.com** värdnamnet till Azure Stack webbapp med [mappar en A-post till Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record). Använd Internet-dirigerbara IP-adressen för App Service-programmet.

2. Lägg till den **app.northwind.com** värdnamnet till Azure Stack webbapp med [mappa en CNAME-post till Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record). Använd det värdnamn som du konfigurerade i föregående steg (1) som mål för CNAME.

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>Konfigurera SSL-certifikat för att skala i molnet

Du måste säkerställa att känsliga data som samlas in av ditt webbprogram är säker överföring till, och i vila på SQL-databasen.

Du konfigurerar dina Azure och Azure Stack-webbprogram att använda SSL-certifikat för all inkommande trafik.

### <a name="add-ssl-to-azure-and-azure-stack"></a>Lägg till SSL i Azure och Azure Stack

Lägg till SSL till Azure:

1. Kontrollera att SSL-certifikatet som du har fått är giltig för underdomänen som du skapade. (Det är Okej att använda jokerteckencertifikat.)

2. I Azure, följer du anvisningarna i den **förbereda din webbapp** och **binda SSL-certifikat** delar av den [binda ett befintligt anpassat SSL-certifikat till Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) artiklar. Välj **SNI-baserad SSL** som den **SSL-typ**.

3. Omdirigera all trafik till HTTPS-porten. Följ instruktionerna i den **framtvinga HTTPS** delen av den [binda ett befintligt anpassat SSL-certifikat till Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) artikeln.

Att lägga till SSL i Azure Stack:

- Upprepa steg 1 – 3 som du använde för Azure.

## <a name="configure-and-deploy-the-web-application"></a>Konfigurera och distribuera webbprogrammet

Du ska konfigurera programkoden för rapporten telemetri till rätt Application Insights-instans och konfigurerar webbprogram med rätt anslutningssträngar. Läs mer om Application Insights i [vad är Application Insights?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Lägg till Application Insights

1. Öppna ditt webbprogram i Microsoft Visual Studio.

2. [Lägg till Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core#add-application-insights-telemetry) till projektet för att skicka telemetrin som Application Insights använder för att skapa aviseringar när webbtrafik ökar eller minskar.

### <a name="configure-dynamic-connection-strings"></a>Konfigurera dynamisk anslutningssträngar

Varje instans av webbprogrammet använder en annan metod för att ansluta till SQL-databasen. Program i Azure använder den privata IP-adressen för SQL Server-dator (VM) och programmet i Azure Stack använder offentliga IP-adressen för SQL Server-dator.

> [!Note]  
> Offentliga IP-adress ska inte Internet-dirigerbara på ett Azure Stack integrerat System. På en Azure Stack Development Kit (ASDK), den offentliga IP-adressen är inte dirigerbara utanför ASDK.

Du kan använda miljövariabler i App Service för att skicka en annan anslutningssträng till varje instans av programmet.

1. Öppna programmet i Visual Studio.

2. Öppna Startup.cs och Sök efter följande kodblock:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Ersätt tidigare kodblocket med följande kod, som använder en anslutningssträng som definieras i filen appsettings.json:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>Konfigurera programinställningar för App Service

1. Skapa anslutningssträngar för Azure och Azure Stack. Strängarna ska vara samma, förutom IP-adresser som används.

2. Lägg till lämpliga anslutningssträngen i Azure och Azure Stack, [som en programinställning](https://docs.microsoft.com/azure/app-service/web-sites-configure) i webbprogrammet, med hjälp av `SQLCONNSTR\_` som ett prefix i namnet.

3. **Spara** webbprograminställningarna och starta om programmet.

## <a name="enable-automatic-scaling-in-global-azure"></a>Aktivera automatisk skalning i globala Azure

När du skapar din webbapp i App Service environment startar med en instans. Du kan automatiskt skala ut att lägga till instanser för att ge mer beräkningsresurser för din app. På samma sätt kan du automatiskt skala in och minska antalet instanser behoven för dina appar.

> [!Note]  
> Du måste ha en Apptjänstplan för att konfigurera skala ut och skala in. Om du inte har en plan, skapa en innan du startar nästa steg.

### <a name="enable-automatic-scale-out"></a>Aktivera automatisk skalning

1. I Azure, hitta App Service-planen för platser som du vill skala ut och välj sedan **skala ut (apptjänstplan)**.

    ![Skala ut](media/azure-stack-solution-hybrid-cloud/image16.png)

2. Välj **aktivera autoskalning**.

    ![Aktivera autoskalning](media/azure-stack-solution-hybrid-cloud/image17.png)

3. Ange ett namn för **namn på Autoskalningsinställning**. För den **standard** automatisk skalningsregeln, Välj **skala baserat på ett mått**. Ange den **Instansgränser** till **minst: 1**, **maximala: 10**, och **standard: 1**.

    ![Konfigurera autoskalning](media/azure-stack-solution-hybrid-cloud/image18.png)

4. Välj **+ Lägg till en regel**.

5. I **mått källa**väljer **aktuella resursen**. Använd följande villkor och åtgärder för regeln.

**Kriterie**

1. Under **tidsmängd,** Välj **genomsnittlig**.

2. Under **måttet namnet**väljer **CPU-procent**.

3. Under **operatorn**väljer **är större än**.

   - Ange den **tröskelvärdet** till **50**.
   - Ange den **varaktighet** till **10**.

**Åtgärd**

1. Under **åtgärden**väljer **öka antal med**.

2. Ange den **Instansantalet** till **2**.

3. Ange den **Nedkylningstid** till **5**.

4. Välj **Lägg till**.

5. Välj den **+ Lägg till en regel**.

6. I **mått källa**väljer **aktuella resursen.**

   > [!Note]  
   > Den aktuella resursen innehåller App Service-planens namn eller GUID och **resurstyp** och **Resource** listrutorna är nedtonade.

### <a name="enable-automatic-scale-in"></a>Aktivera automatisk skalning i

När belastningen minskar, kan Azure-webbprogram automatiskt att minska antalet aktiva instanser att minska kostnaderna. Den här åtgärden är mindre än skala ut för att minimera effekten på programanvändare.

1. Navigera till den **standard** skala ut villkor, väljer **+ Lägg till en regel**. Använd följande villkor och åtgärder för regeln.

**Kriterie**

1. Under **tidsmängd,** Välj **genomsnittlig**.

2. Under **måttet namnet**väljer **CPU-procent**.

3. Under **operatorn**väljer **mindre än**.

   - Ange den **tröskelvärdet** till **30**.
   - Ange den **varaktighet** till **10**.

**Åtgärd**

1. Under **åtgärden**väljer **minska antal med**.

   - Ange den **Instansantalet** till **1**.
   - Ange den **Nedkylningstid** till **5**.

2. Välj **Lägg till**.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Skapa en Traffic Manager-profil och konfigurera molnöverskridande skalning

Du skapar en Traffic Manager-profil i Azure och sedan konfigurera slutpunkter för att aktivera molnöverskridande skalning.

### <a name="create-traffic-manager-profile"></a>Skapa Traffic Manager-profil

1. Välj **skapa en resurs**
2. Välj **nätverk**
3. Välj **Traffic Manager-profil** och konfigurera följande:

   - I **namn**, ange ett namn för din profil. Det här namnet **måste** vara unikt inom trafficmanager.net-zonen och används för att skapa en ny DNS-namn (till exempel northwindstore.trafficmanager.net).
   - För **routningsmetod**väljer den **viktat**.
   - För **prenumeration**, Välj den prenumeration som du vill skapa den här profilen i.
   - I **resursgrupp**, skapa en ny resursgrupp för den här profilen.
   - I **Resursgruppsplats** väljer du plats för resursgruppen. Den här inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som ska distribueras globalt.

4. Välj **Skapa**.

    ![Skapa Traffic Manager-profil](media/azure-stack-solution-hybrid-cloud/image19.png)

   När den globala distribueringen av Traffic Manager-profilen är klar visas den i listan över resurser för den resursgrupp du skapade den under.

### <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

1. Sök efter Traffic Manager-profilen som du skapade. (Om du navigerat till resursgruppen för profilen, markerar du profilen).

2. I **Traffic Manager-profil**under **inställningar**väljer **slutpunkter**.

3. Välj **Lägg till**.

4. I **Lägg till slutpunkt**, använder du följande inställningar för Azure Stack:

   - För **typ**väljer **extern slutpunkt**.
   - Ange en **namn** för den här slutpunkten.
   - För **fullständigt kvalificerade domännamnet (FQDN) eller IP-** fyller i extern URL för Azure Stack web Apps.
   - För **vikt**, behåller du standardvärdet **1**. Den här vikt resulterar i all trafik till den här slutpunkten om den är felfri.
   - Lämna **Lägg till som inaktiverad** avmarkerat.

5. Välj **OK** att spara Azure Stack-slutpunkten.

Därefter får du konfigurera Azure-slutpunkten.

1. På **Traffic Manager-profil**väljer **slutpunkter**.
2. Välj **+ Lägg till**.
3. På **Lägg till slutpunkt**, använder du följande inställningar för Azure:

   - För **typ**väljer **Azure-slutpunkt**.
   - Ange en **namn** för den här slutpunkten.
   - För **Målresurstyp**väljer **Apptjänst**.
   - För **målresurs**väljer **väljer en app service** att se en lista över Web Apps i samma prenumeration.
   - I **Resurs** väljer du den apptjänst som du vill lägga till som den första slutpunkten.
   - För **vikt**väljer **2**. Detta resulterar i all trafik till den här slutpunkten om den primära slutpunkten är skadad eller så har du en regel/avisering som nytt dirigerar trafik när den utlöses.
   - Lämna **Lägg till som inaktiverad** avmarkerat.

4. Välj **OK** att spara Azure-slutpunkten.

När båda slutpunkterna har konfigurerats och de är listade i **Traffic Manager-profil** när du väljer **slutpunkter**. Exemplet i följande skärmdump visar två slutpunkter med information om status och konfiguration för var och en.

![Slutpunkter](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Konfigurera Application Insights, övervakning och avisering

Azure Application Insights kan du övervaka program och skicka aviseringar baserat på villkor som du konfigurerar. Några exempel: programmet är inte tillgänglig, har drabbats av fel eller visar prestandaproblem.

Du använder Application Insights-mått för att skapa aviseringar. När dessa aviseringar utlöser stack webbprogrammen instansen kommer automatiskt växla från Azure Stack till Azure för att skala ut och sedan tillbaka till Azure att skala i.

### <a name="create-an-alert-from-metrics"></a>Skapa en avisering utifrån mått

Gå till resursgruppen för den här självstudien och välj Application Insights-instans för att öppna **Application Insights**.

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

Skapa en skalbar aviseringen och en skala i aviseringen ska du använda den här vyn.

### <a name="create-the-scale-out-alert"></a>Skapa skalbara avisering

1. Under **konfigurera**väljer **aviseringar (klassisk)**.
2. Välj **Lägg till måttavisering (klassisk)**.
3. I **Lägg till regel**, konfigurerar du följande:

   - För **namn**, ange **utöka till Azure-molnet**.
   - En **beskrivning** är valfritt.
   - Under **källa**, **Avisera om**väljer **mått**.
   - Under **kriterier**, Välj din prenumeration, resursgrupp för din Traffic Manager-profil och namnet på Traffic Manager-profilen för resursen.

4. För **mått**väljer **för begäran om**.
5. För **villkor**väljer **är större än**.
6. För **tröskelvärdet**, ange **2**.
7. För **Period**väljer **under de senaste 5 minuterna**.
8. Under **meddela via**:
   - Markera kryssrutan för **e-postägare, deltagare och läsare**.
   - Ange din e-postadress för **administratören email(s)**.

9. På menyraden väljer **spara**.

### <a name="create-the-scale-in-alert"></a>Skapa skalan i aviseringen

1. Under **konfigurera**väljer **aviseringar (klassisk)**.
2. Välj **Lägg till måttavisering (klassisk)**.
3. I **Lägg till regel**, konfigurerar du följande:

   - För **namn**, ange **skala tillbaka till Azure Stack**.
   - En **beskrivning** är valfritt.
   - Under **källa**, **Avisera om**väljer **mått**.
   - Under **kriterier**, Välj din prenumeration, resursgrupp för din Traffic Manager-profil och namnet på Traffic Manager-profilen för resursen.

4. För **mått**väljer **för begäran om**.
5. För **villkor**väljer **mindre än**.
6. För **tröskelvärdet**, ange **2**.
7. För **Period**väljer **under de senaste 5 minuterna**.
8. Under **meddela via**:
   - Markera kryssrutan för **e-postägare, deltagare och läsare**.
   - Ange din e-postadress för **administratören email(s)**.

9. På menyraden väljer **spara**.

Följande skärmdump visar aviseringar för att skala ut och skala i.

   ![Aviseringar (klassiska)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>Omdirigera trafik mellan Azure och Azure Stack

Du kan konfigurera manuell eller automatisk växling av Web app trafiken växlar mellan Azure och Azure Stack.

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>Konfigurera manuell växling mellan Azure och Azure Stack

När webbplatsen når de tröskelvärden som du konfigurerar, får du en avisering. Använd följande steg för att manuellt omdirigera trafik till Azure.

1. Välj din Traffic Manager-profil i Azure-portalen.

    ![Traffic Manager-slutpunkter](media/azure-stack-solution-hybrid-cloud/image20.png)

2. Välj **slutpunkter**.
3. Välj den **Azure-slutpunkt**.
4. Under **Status** väljer **aktiverad**, och välj sedan **spara**.

    ![Aktivera Azure-slutpunkt](media/azure-stack-solution-hybrid-cloud/image23.png)

5. På **slutpunkter** för Traffic Manager-profilen väljer **extern slutpunkt**.
6. Under **Status** väljer **inaktiverad**, och välj sedan **spara**.

    ![Inaktivera Azure Stack-slutpunkt](media/azure-stack-solution-hybrid-cloud/image24.png)

När slutpunkterna har konfigurerats och går programmet trafiken till Azure skala ut web Apps i stället för Azure Stack-webbapp.

 ![Slutpunkter som har ändrats](media/azure-stack-solution-hybrid-cloud/image25.png)

Använd de föregående stegen om du vill ändra flödet tillbaka till Azure Stack:

- Aktivera Azure Stack-slutpunkten
- Inaktivera Azure-slutpunkt

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>Konfigurera automatisk växling mellan Azure och Azure Stack

Du kan också använda Application Insights-övervakning om ditt program körs i en [serverlös](https://azure.microsoft.com/overview/serverless-computing/) miljö som tillhandahålls av Azure Functions.

I det här scenariot kan du konfigurera Application Insights om du vill använda en webhook som anropar en funktionsapp. Den här appen automatiskt aktiverar eller inaktiverar en slutpunkt som svar på en avisering.

Använd följande steg som en vägledning för att konfigurera automatisk trafiken växlar.

1. Skapa en funktionsapp i Azure.
2. Skapa en HTTP-utlöst funktion.
3. Importera Azure-SDK: er för Resource Manager, Web Apps och Traffic Manager.
4. Utveckla kod för att:

   - Autentisera till Azure-prenumerationen.
   - Använd en parameter som växlar Traffic Manager-slutpunkter för att dirigera trafik till Azure eller Azure Stack.

5. Spara din kod och lägga till Function-appens URL med lämpliga parametrar för att den **Webhook** i Application Insights varningsregel inställningarna.
6. Trafik dirigeras automatiskt när en Application Insights-avisering utlöses.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure molnmönster i [Molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns).
