---
title: Installera den lokala datagatewayen
description: Innan du kan komma åt data lokalt från Azure Logic Apps kan du hämta och installera den lokala datagatewayen
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: 797cd82327d68003d4e5f007d1f16e9534092ac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283997"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installera lokal datagateway för Azure Logic Apps

Innan du kan [ansluta till lokala datakällor från Azure Logic Apps](../logic-apps/logic-apps-gateway-connection.md)kan du hämta och installera den lokala [datagatewayen](https://aka.ms/on-premises-data-gateway-installer) på en lokal dator. Gatewayen fungerar som en brygga som ger snabb dataöverföring och kryptering mellan datakällor på plats och dina logikappar. Du kan använda samma gatewayinstallation med andra molntjänster, till exempel Power BI, Power Automate, Power Apps och Azure Analysis Services. Information om hur du använder gatewayen med dessa tjänster finns i följande artiklar:

* [Microsoft Power Automate lokal datagateway](/power-automate/gateway-reference)
* [Microsoft Power BI lokal datagateway](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps lokal datagateway](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

Den här artikeln visar hur du hämtar, installerar och konfigurerar din lokala datagateway så att du kan komma åt lokala datakällor från Azure Logic Apps. Du kan också läsa mer om [hur datagatewayen fungerar](#gateway-cloud-service) senare i det här avsnittet. Mer information om gatewayen finns i [Vad är en lokal gateway?](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)

<a name="requirements"></a>

## <a name="prerequisites"></a>Krav

* Ett Azure-konto och prenumeration. Om du inte har ett Azure-konto med en prenumeration [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

  * Ditt Azure-konto måste tillhöra en enda [Azure Active Directory -klient eller katalog (Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Du måste använda samma Azure-konto för att installera och administrera gatewayen på din lokala dator.

  * Under gatewayinstallationen loggar du in med ditt Azure-konto, som länkar din gatewayinstallation till ditt Azure-konto och endast det kontot. Senare, i Azure-portalen, måste du använda samma Azure-konto och Azure AD-klient när du skapar en Azure gateway-resurs som registrerar och gör anspråk på din gateway-installation. I Azure Logic Apps använder lokala utlösare och åtgärder sedan gatewayresursen för att ansluta till lokala datakällor.

    > [!NOTE]
    > Du kan bara länka en gateway-installation och en Azure gateway-resurs till varandra. Du kan inte länka samma gatewayinstallation till flera Azure-konton eller Azure gateway-resurser. Ett Azure-konto kan dock länka till flera gatewayinstallationer och Azure gateway-resurser. I en lokal utlösare eller åtgärd kan du välja bland dina olika Azure-prenumerationer och sedan välja en associerad gateway-resurs.

  * Du måste logga in med antingen ett arbetskonto eller ett skolkonto, `username@contoso.com`även kallat ett *organisationskonto,* som ser ut som . Du kan inte använda Azure B2B-konton (gäst)konton @hotmail.com @outlook.comeller personliga Microsoft-konton, till exempel eller .

    > [!TIP]
    > Om du har registrerat dig för ett Office 365-erbjudande och inte `username@domain.onmicrosoft.com`angav din e-postadress för arbetet kan din adress se ut. Ditt konto lagras i en klient i en Azure Active Directory (Azure AD). I de flesta fall är UPN (User Principal Name) för ditt Azure AD-konto detsamma som din e-postadress.
    >
    > Om du vill använda en [Visual Studio Standard-prenumeration](https://visualstudio.microsoft.com/vs/pricing/) som är länkad till ett Microsoft-konto skapar du först [en klient i Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) eller använder standardkatalogen. Lägg till en användare med ett lösenord i katalogen och ge sedan användaren åtkomst till din Azure-prenumeration. Du kan sedan logga in under installationen av gatewayen med det här användarnamnet och lösenordet.

* Här är krav för din lokala dator:

  **Minimikrav**

  * .NET-ramverket 4.7.2
  * 64-bitars version av Windows 7 eller Windows Server 2008 R2 (eller senare)

  **Rekommenderade krav**

  * 8-kärnig CPU
  * 8 GB minne
  * 64-bitarsversion av Windows Server 2012 R2 eller senare
  * SSD-lagring (Solid state drive) för buffring

  > [!NOTE]
  > Gatewayen stöder inte Windows Server Core.

* **Relaterade överväganden**

  * Installera den lokala datagatewayen endast på en lokal dator, inte en domänkontrollant. Du behöver inte installera gatewayen på samma dator som datakällan. Du behöver bara en gateway för alla dina datakällor, så du behöver inte installera gatewayen för varje datakälla.

    > [!TIP]
    > Om du vill minimera svarstiden kan du installera gatewayen så nära datakällan eller på samma dator som möjligt, förutsatt att du har behörighet.

  * Installera gatewayen på en dator som finns i ett kabelanslutet nätverk, ansluten till internet, alltid påslagen och som förs i viloläge. Annars kan gatewayen inte köras och prestanda kan uppstå över ett trådlöst nätverk.

  * Om du planerar att använda Windows-autentisering kontrollerar du att du installerar gatewayen på en dator som är medlem i samma Active Directory-miljö som dina datakällor.

  * Den region som du väljer för gateway-installationen är samma plats som du måste välja när du senare skapar Azure gateway-resursen för logikappen. Som standard är den här regionen samma plats som din Azure AD-klient som hanterar ditt Azure-konto. Du kan dock ändra platsen under installationen av gatewayen.

  * Om du uppdaterar gatewayinstallationen till den senaste versionen avinstallerar du den aktuella gatewayen först för en renare upplevelse.

  * Gatewayen har två lägen: standardläge och personligt läge, som endast gäller för Power BI. Du kan inte ha fler än en gateway som körs i samma läge på samma dator.

  * Azure Logic Apps stöder läs- och skrivåtgärder via gatewayen. Dessa åtgärder har dock [gränser för deras nyttolaststorlek.](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installera datagateway

1. [Hämta och kör gatewayinstallationsprogrammet på en lokal dator](https://aka.ms/on-premises-data-gateway-installer).

1. Granska minimikraven, behåll standardinstallationsvägen, acceptera användningsvillkoren och välj sedan **Installera**.

   ![Granska krav och acceptera användarvillkor](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. När gatewayen har installerats anger du e-postadressen för ditt Azure-konto och väljer sedan **Logga in,** till exempel:

   ![Logga in med arbets- eller skolkonto](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Din gatewayinstallation kan bara länka till ett Azure-konto.

1. Välj **Registrera en ny gateway på den här datorn** > **Nästa**. Det här steget registrerar din gateway-installation med [gatewaymolntjänsten](#gateway-cloud-service).

   ![Registrera gateway på lokal dator](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Ange den här informationen för gatewayinstallationen:

   * Ett gatewaynamn som är unikt för din Azure AD-klientorganisation
   * Återställningsnyckeln, som måste ha minst åtta tecken, som du vill använda
   * Bekräftelse för återställningsnyckeln

   ![Ange information för gatewayinstallation](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Spara och håll återställningsnyckeln på ett säkert ställe. Du behöver den här nyckeln om du någonsin vill ändra platsen, flytta, återställa eller ta över en gateway-installation.

   Observera alternativet Att **lägga till i ett befintligt gateway-kluster**, som du väljer när du installerar ytterligare gateways för scenarier med hög [tillgänglighet](#high-availability).

1. Kontrollera regionen för gateway molntjänsten och [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) som används av din gateway installation. Som standard är den här regionen samma plats som Azure AD-klienten för ditt Azure-konto.

   ![Bekräfta region för gateway-service och servicebuss](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Om du vill acceptera standardområdet väljer du **Konfigurera**. Men om standardregionen inte är den som är närmast dig kan du ändra regionen.

   *Varför ändra regionen för gatewayinstallationen?*

   Om du till exempel vill minska svarstiden kan du ändra gatewayens region till samma region som logikappen. Du kan också välja den region som ligger närmast din lokala datakälla. Din *gatewayresurs i Azure* och din logikapp kan ha olika platser.

   1. Välj Ändra **region**bredvid den aktuella regionen .

      ![Ändra den aktuella gatewayregionen](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Öppna listan **Välj region** på nästa sida, välj önskat område och välj **Klar**.

      ![Välj en annan region för gateway-tjänst](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Granska informationen i det slutliga bekräftelsefönstret. I det här exemplet används samma konto för Logic Apps, Power BI, Power Apps och Power Automate, så gatewayen är tillgänglig för alla dessa tjänster. När du är klar väljer du **Stäng**.

   ![Bekräfta information om datagateway](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. [Skapa nu Azure-resursen för gatewayinstallationen](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Kontrollera eller justera kommunikationsinställningarna

Den lokala datagatewayen är beroende av [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) för molnanslutning och upprättar motsvarande utgående anslutningar till gatewayens associerade Azure-region. Om din arbetsmiljö kräver att trafiken går via en proxy eller brandvägg för att komma åt internet kan den här begränsningen förhindra att den lokala datagatewayen ansluter till gatewaymolntjänsten och Azure Service Bus. Gatewayen har flera kommunikationsinställningar som du kan justera. Mer information finns i de här ämnena:

* [Justera kommunikationsinställningarna för den lokala datagatewayen](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Konfigurera proxyinställningar för den lokala datagatewayen](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Support med hög tillgänglighet

Om du vill undvika enstaka felpunkter för lokal dataåtkomst kan du ha flera gatewayinstallationer (endast standardläge) med var och en på en annan dator och konfigurera dem som ett kluster eller en grupp. På så sätt dirigeras databegäranden till den andra gatewayen och så vidare om den primära gatewayen inte är tillgänglig. Eftersom du bara kan installera en standardgateway på en dator måste du installera varje ytterligare gateway som finns i klustret på en annan dator. Alla kopplingar som fungerar med den lokala datagatewayen stöder hög tillgänglighet.

* Du måste redan ha minst en gatewayinstallation med samma Azure-konto som den primära gatewayen och återställningsnyckeln för den installationen.

* Din primära gateway måste köra gateway-uppdateringen från november 2017 eller senare.

När du har konfigurerat den primära gatewayen väljer du **Lägg till i ett befintligt gatewaykluster**när du går till installera en annan gateway , väljer den primära gatewayen, som är den första gatewayen som du installerade, och tillhandahåller återställningsnyckeln för den gatewayen. Mer information finns i [Kluster med hög tillgänglighet för lokal datagateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Ändra plats, migrera, återställa eller ta över befintlig gateway

Om du måste ändra gatewayens plats, flytta gatewayinstallationen till en ny dator, återställa en skadad gateway eller bli ägare till en befintlig gateway behöver du återställningsnyckeln som angavs under installationen av gatewayen.

1. Kör gatewayinstallationsprogrammet på datorn som har den befintliga gatewayen. Om du inte har det senaste installationsprogrammet för gateway [laddar du ned den senaste gatewayversionen](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Innan du återställer gatewayen på datorn som har den ursprungliga gatewayinstallationen måste du först avinstallera gatewayen på den datorn. Den här åtgärden kopplar från den ursprungliga gatewayen.
   > Om du tar bort eller tar bort ett gateway-kluster för en molntjänst kan du inte återställa klustret.

1. När installationsprogrammet har öppnats loggar du in med samma Azure-konto som användes för att installera gatewayen.

1. Välj **Migrera, återställa eller ta över en befintlig gateway** > **Nästa,** till exempel:

   ![Välj "Migrera, återställa eller ta över en befintlig gateway"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Välj bland tillgängliga kluster och gateways och ange återställningsnyckeln för den valda gatewayen, till exempel:

   ![Välj gateway och ange återställningsnyckel](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Om du vill ändra regionen väljer du **Ändra region**och väljer den nya regionen.

1. När du är klar väljer du **Konfigurera** så att du kan slutföra uppgiften.

## <a name="tenant-level-administration"></a>Administration på klientnivå

För att få insyn i alla lokala datagateways i en Azure AD-klient kan globala administratörer i den klienten logga in på [Power Platform Admin Center](https://powerplatform.microsoft.com) som klientadministratör och välja alternativet Data **gateways.** Mer information finns i [Administration på klientnivå för den lokala datagatewayen](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Starta om gateway

Som standard körs gatewayinstallationen på den lokala datorn som ett Windows-tjänstkonto med namnet "Lokal datagatewaytjänst". Gateway-installationen använder dock `NT SERVICE\PBIEgwService` namnet för sina kontoautentiseringsuppgifter för "Logga in som" och har behörigheterna "Logga in som en tjänst".

> [!NOTE]
> Ditt Windows-tjänstkonto skiljer sig från det konto som används för att ansluta till lokala datakällor och från Det Azure-konto som du använder när du loggar in på molntjänster.

Precis som alla andra Windows-tjänster kan du starta och stoppa gatewayen på olika sätt. Mer information finns i [Starta om en lokal datagateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Så här fungerar gatewayen

Användare i organisationen kan komma åt lokala data som de redan har auktoriserad åtkomst till. Men innan dessa användare kan ansluta till din lokala datakälla måste du installera och konfigurera en lokal datagateway. Vanligtvis är en administratör den person som installerar och konfigurerar en gateway. Dessa åtgärder kan kräva serveradministratörsbehörighet eller särskild kunskap om dina lokala servrar.

Gatewayen hjälper till att underlätta snabbare och säkrare kommunikation bakom kulisserna. Den här kommunikationen flödar mellan en användare i molnet, gatewaymolntjänsten och din lokala datakälla. Molntjänsten för gateway krypterar och lagrar dina datakälluppgifter och gatewayinformation. Tjänsten dirigerar också frågor och deras resultat mellan användaren, gatewayen och din lokala datakälla.

Gatewayen fungerar med brandväggar och använder endast utgående anslutningar. All trafik kommer som säker utgående trafik från gateway-agenten. Gatewayen vidarebefordrar data från lokala källor på krypterade kanaler via [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). Den här servicebussen skapar en kanal mellan gatewayen och samtalstjänsten, men lagrar inga data. Alla data som färdas genom gatewayen krypteras.

![Arkitektur för lokal datagateway](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Beroende på molntjänsten kan du behöva konfigurera en datakälla för gatewayen.

De här stegen beskriver vad som händer när du interagerar med ett element som är anslutet till en lokal datakälla:

1. Molntjänsten skapar en fråga tillsammans med de krypterade autentiseringsuppgifterna för datakällan. Tjänsten skickar sedan frågan och autentiseringsuppgifterna till gatewaykön för bearbetning.

1. Gateway-molntjänsten analyserar frågan och skickar begäran till Azure Service Bus.

1. Azure Service Bus skickar väntande begäranden till gatewayen.

1. Gatewayen hämtar frågan, dekrypterar autentiseringsuppgifterna och ansluter till en eller flera datakällor med dessa autentiseringsuppgifter.

1. Gatewayen skickar frågan till datakällan för körning.

1. Resultaten skickas från datakällan tillbaka till gatewayen och sedan till gatewaymolntjänsten. Gateway-molntjänsten använder sedan resultaten.

### <a name="authentication-to-on-premises-data-sources"></a>Autentisering till lokala datakällor

En lagrad autentiseringsuppgift används för att ansluta från gatewayen till lokala datakällor. Oavsett användare använder gatewayen den lagrade autentiseringsuppgifterna för att ansluta. Det kan finnas autentiseringsundantag för specifika tjänster, till exempel DirectQuery och LiveConnect för Analysis Services i Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsofts molntjänster använder [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) för att autentisera användare. En Azure AD-klient innehåller användarnamn och säkerhetsgrupper. Vanligtvis är den e-postadress som du använder för inloggning samma som UPN (User Principal Name) för ditt konto.

### <a name="what-is-my-upn"></a>Vad är min UPN?

Om du inte är domänadministratör kanske du inte känner till ditt UPN. Om du vill hitta UPN `whoami /upn` för ditt konto kör du kommandot från din arbetsstation. Även om resultatet ser ut som en e-postadress blir resultatet UPN för ditt lokala domänkonto.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Synkronisera ett lokalt Active Directory med Azure AD

UPN för dina lokala Active Directory-konton och Azure AD-konton måste vara desamma. Se därför till att varje lokalt Active Directory-konto matchar ditt Azure AD-konto. Molntjänsterna vet bara om konton i Azure AD. Du behöver därför inte lägga till ett konto i den lokala Active Directory. Om kontot inte finns i Azure AD kan du inte använda det kontot.

Här är några sätt som du kan matcha dina lokala Active Directory-konton med Azure AD.

* Lägg till konton manuellt i Azure AD.

  Skapa ett konto i Azure-portalen eller i Microsoft 365-administrationscentret. Kontrollera att kontonamnet matchar UPN för det lokala Active Directory-kontot.

* Synkronisera lokala konton med din Azure AD-klient med hjälp av Azure Active Directory Connect-verktyget.

  Azure AD Connect-verktyget innehåller alternativ för katalogsynkronisering och autentiseringskonfiguration. Dessa alternativ inkluderar synkronisering av lösenord hash, direktautentisering och federation. Om du inte är klientadministratör eller lokal domänadministratör kontaktar du IT-administratören för att konfigurera Azure AD Connect. Azure AD Connect säkerställer att din Azure AD UPN matchar ditt lokala Active Directory UPN. Den här matchningen hjälper om du använder Analysis Services live-anslutningar med Power BI eller SSO-funktioner (Single Sign-on).

  > [!NOTE]
  > Om du synkroniserar konton med Azure AD Connect-verktyget skapas nya konton i din Azure AD-klientorganisation.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Vanliga frågor och felsökning

Mer information finns i de här ämnena:

* [Vanliga frågor och svar om lokal datagateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Felsöka den lokala datagatewayen](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Övervaka och optimera gatewayprestanda](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till lokala data från logikappar](../logic-apps/logic-apps-gateway-connection.md)
* [Funktioner för företagsintegrering](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Anslutningsprogram för Azure Logic Apps](../connectors/apis-list.md)
