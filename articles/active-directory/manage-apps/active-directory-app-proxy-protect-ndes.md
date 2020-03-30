---
title: Integrera med AD Application Proxy på en NDES-server
titleSuffix: Azure Active Directory
description: Vägledning om hur du distribuerar en Azure Active Directory Application Proxy för att skydda din NDES-server.
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032262"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integrera med Azure AD Application Proxy på en NDES-server (Network Device Enrollment Service)

Med AD-programproxyn (Azure Active Directory) kan du publicera program i nätverket. Dessa program är sådana som SharePoint-webbplatser, Microsoft Outlook Web App och andra webbprogram. Det ger också säker åtkomst till användare utanför nätverket via Azure.

Om du inte har tidigare i Azure AD Application Proxy och vill veta mer läser du [Fjärråtkomst till lokala program via Azure AD Application Proxy](application-proxy.md).

Azure AD Application Proxy är byggd på Azure. Det ger dig en enorm mängd nätverksbandbredd och serverinfrastruktur för bättre skydd mot distribuerade DDOS-attacker (Denial-of-Service) och utmärkt tillgänglighet. Dessutom finns det ingen anledning att öppna externa brandväggsportar till ditt lokala nätverk och ingen DMZ-server krävs. All trafik har sitt ursprung i inkommande. En fullständig lista över utgående portar finns i [Självstudiekurs: Lägg till ett lokalt program för fjärråtkomst via Programproxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

> Azure AD Application Proxy är en funktion som bara är tillgänglig om du använder Premium- eller Basic-utgåvorna av Azure Active Directory. Mer information finns i [Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/). 
> Om du har EMS-licenser (Enterprise Mobility Suite) kan du använda den här lösningen.
> Azure AD Application Proxy-anslutningen installeras bara på Windows Server 2012 R2 eller senare. Detta är också ett krav på NDES-servern.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Installera och registrera anslutningen på NDES-servern

1. Logga in på [Azure-portalen](https://portal.azure.com/) som programadministratör för den katalog som använder programproxy. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat administratörsalias på den domänen.
1. Välj ditt användarnamn i det övre högra hörnet. Kontrollera att du är inloggad på en katalog som använder Programproxy. Om du behöver ändra kataloger väljer du **Växla katalog** och väljer en katalog som använder Programproxy.
1. Välj Azure Active **Directory**i den vänstra navigeringspanelen .
1. Under **Hantera**väljer du **Programproxy**.
1. Välj **Tjänsten Hämta anslutning**.

    ![Ladda ned anslutningstjänsten för att se användarvillkoren](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Läs användningsvillkoren. När du är klar väljer du **Acceptera villkor & Hämta**.
1. Kopiera installationsfilen för Azure AD Application Proxy-anslutning till NDES-servern. 
   > Du kan installera anslutningen på valfri server i företagets nätverk med åtkomst till NDES. Du behöver inte installera det på NDES-servern själv.
1. Kör installationsfilen, till exempel *AADApplicationProxyConnectorInstaller.exe*. Acceptera licensvillkoren för programvara.
1. Under installationen uppmanas du att registrera anslutningen med programproxyen i din Azure AD-katalog.
   * Ange autentiseringsuppgifterna för en global administratör eller programadministratör i din Azure AD-katalog. Azure AD-autentiseringsuppgifterna för globala administratörer eller programadministratören kan skilja sig från dina Azure-autentiseringsuppgifter i portalen.

        > [!NOTE]
        > Det globala konto eller programadministratörskontot som används för att registrera kopplingen måste tillhöra samma katalog där du aktiverar tjänsten Programproxy.
        >
        > Om Azure AD-domänen till exempel *är contoso.com*bör administratören `admin@contoso.com` för globalt/program vara eller ett annat giltigt alias på den domänen.

   * Om förbättrad säkerhetskonfiguration i Internet Explorer är aktiverat för den server där du installerar anslutningen kan registreringsskärmen blockeras. Om du vill tillåta åtkomst följer du instruktionerna i felmeddelandet eller inaktiverar Förbättrad säkerhet i Internet Explorer under installationsprocessen.
   * Om anslutningsregistreringen misslyckas läser [du Felsöka programproxy](application-proxy-troubleshoot.md).
1. I slutet av installationen visas en anteckning för miljöer med en utgående proxy. Om du vill konfigurera Azure AD Application Proxy-kopplingen så att den `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`fungerar via den utgående proxyn kör du det medföljande skriptet, till exempel .
1. På sidan Programproxy i Azure-portalen visas den nya anslutningen med statusen *Aktiv*, vilket visas i följande exempel:

    ![Den nya Azure AD Application Proxy-anslutningen som visas som aktiv i Azure-portalen](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Om du vill ge hög tillgänglighet för program som autentiserar via Azure AD Application Proxy kan du installera kopplingar på flera virtuella datorer. Upprepa samma steg i föregående avsnitt för att installera anslutningen på andra servrar som är anslutna till azure AD DS-hanterade domänen.

1. Efter en lyckad installation går du tillbaka till Azure-portalen.

1. Välj **Företagsprogram**.

   ![se till att du engagerar rätt intressenter](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Välj **+Nytt program**och välj sedan **Lokalt program**. 

1. Konfigurera följande fält i **lägg till ditt eget lokala program:**

   * **Namn**: Ange ett namn för programmet.
   * **Intern url:** Ange den interna URL/FQDN för din NDES-server där du installerade anslutningen.
   * **Pre-autentisering:** Välj **Genomgående**. Det går inte att använda någon form av förautentisering. Det protokoll som används för certifikatbegäranden (SCEP) ger inte ett sådant alternativ.
   * Kopiera den angivna **externa URL:en** till Urklipp.

1. Välj **+Lägg till** för att spara programmet.

1. Testa om du kan komma åt din NDES-server via Azure AD Application proxy genom att klistra in länken du kopierade i steg 10 till en webbläsare. Du bör se en standard-IIS-välkomstsida.

1. Som ett sista test lägger du till *mscep.dll-sökvägen* till den befintliga URL som du klistrade in i föregående steg:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Du bör se ett **HTTP-fel 403 – Förbjuden** svar.

1. Ändra NDES-URL:en (via Microsoft Intune) till enheter, den här ändringen kan antingen finnas i Microsoft Endpoint Configuration Center eller i Intune Cloud.

   * För Configuration Center går du till certifikatregistreringspunkten (CRP) och justerar URL:en. Den här webbadressen är vad enheter ropar till och presenterar sin utmaning.
   * För Endast Intune Cloud, även känd som Fristående Intune, redigerar eller skapar du en ny SCEP-princip och lägger till den nya URL:en.

## <a name="next-steps"></a>Nästa steg

Med Azure AD Application Proxy integrerad med NDES, publicera program för användare att komma åt. Mer information finns i [publicera program med Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).
