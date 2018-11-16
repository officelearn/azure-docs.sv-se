---
title: Azure AD App Proxy - börja installera connector | Microsoft Docs
description: Aktivera Application Proxy på Azure-portalen och installera anslutningsverktyget för omvänd proxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 9a869055613da6465a9beda9b8edc1bf812b6dfe
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712117"
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Kom igång med Application Proxy och installera connector
Den här artikeln vägleder dig igenom stegen för att aktivera Application Proxy i Azure Active Directory (AD Azure).

Om du inte ännu medveten om säkerhet och produktivitet fördelarna Application Proxy ger för din organisation, Läs mer om [att tillhandahålla säker fjärråtkomst till lokala program](application-proxy.md).

## <a name="prerequisites"></a>Förutsättningar
Om du vill aktivera Application Proxy, behöver du:

* En [Microsoft Azure AD basic eller premium-prenumeration](https://azure.microsoft.com/pricing/details/active-directory). 
* Ett administratörskonto för programmet.

### <a name="windows-server"></a>Windows server
Du behöver en server som kör Windows Server 2012 R2 eller senare som du kan installera anslutningsappen för programproxyn. Servern måste ansluta till Application Proxy-tjänsterna i Azure och lokala program som du publicerar.

Windows server måste ha aktiverats innan du installerar Application Proxy connector som TLS 1.2. Befintliga anslutningar med versioner under 1.5.612.0 fortsätter att fungera i tidigare versioner av TLS tills vidare. Aktivera TLS 1.2:

1. Ange följande registernycklar:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Starta om servern

För enkel inloggning till program som använder Kerberos uttag delegering (KCD), måste Windows server och de program som du publicerar vara i samma Active Directory-domän. Mer information finns i [KCD för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md).
  
### <a name="proxy-servers"></a>Proxyservrar

Om din organisation använder proxyservrar för att ansluta till internet, måste du konfigurera dem för Application Proxy.  Mer information finns i [fungerar med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md). 



## <a name="open-your-ports"></a>Öppna din portar

För att förbereda miljön för Azure AD Application Proxy måste du först aktivera kommunikation till Azure-datacenter. Om det finns en brandvägg i sökvägen måste du se till att den är öppen så att anslutningsappen kan göra HTTPS-förfrågningar (TCP) till programproxyn.

1. Öppna följande portar för **utgående** trafik:

   | Portnummer | Hur den används |
   | --- | --- |
   | 80 | Hämta återkallade certifikat listor (CRL) vid verifiering av SSL-certifikatet |
   | 443 | All utgående kommunikation med tjänsten Application Proxy |

   Om brandväggstrafiken enligt användarna som genererar, öppna dessa portar för trafik från Windows-tjänster som körs som en nätverkstjänst.

   > [!IMPORTANT]
   > Visas i tabellen portkraven för anslutningstjänsten versioner 1.5.132.0 och senare. Om du fortfarande har en äldre version av anslutningen kan du också behöva aktivera följande portar utöver 80 och 443: 5671, 8080 9090 9091 9350 9352 10100 – 10120.
   >
   >Information om hur du uppdaterar dina anslutningar till den senaste versionen finns i [alternativ för Azure AD-programproxyn kopplingar](application-proxy-connectors.md#automatic-updates).

2. Om din brandvägg eller proxyserver kan du DNS-listan över tillåtna program, kan du lista över tillåtna anslutningar till msappproxy.net och servicebus.windows.net. Om inte, du vill tillåta åtkomst till den [Azure DataCenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka.

3. Microsoft använder fyra adresser för att verifiera certifikat. Tillåt åtkomst till följande URL: er om du inte gjort det för andra produkter:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. Din anslutningsapp behöver åtkomst till login.windows.net och login.microsoftonline.com för registreringen.


## <a name="install-and-register-a-connector"></a>Installera och registrera en anslutningsapp
1. Logga in som administratör i den [Azure-portalen](https://portal.azure.com/).
2. Din aktuella katalog visas under ditt användarnamn i det övre högra hörnet. Om du vill ändra katalog väljer du ikonen.
3. Gå till **Azure Active Directory** > **programproxy**.

   ![Gå till Application Proxy](./media/application-proxy-enable/app_proxy_navigate.png)

4. Välj **hämta Connector**.

   ![Hämta anslutningsprogram](./media/application-proxy-enable/download_connector.png)

5. Kör **AADApplicationProxyConnectorInstaller.exe** på den server som du har förberett och som uppfyller kraven.
6. Följ installationsanvisningarna i guiden. Under installationen uppmanas du att registrera anslutningsverktyget med programproxyn för Azure AD-klienten.

   * Ange dina autentiseringsuppgifter som global Azure AD-administratör. Autentiseringsuppgifterna för klienten som du är global administratör för kan skilja sig från dina Microsoft Azure-autentiseringsuppgifter.
   * Se till att den administratör som registrerar anslutningsappen finns i samma katalog där du har aktiverat programproxytjänsten. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat alias på den domänen.
   * Om **Förbättrad säkerhetskonfiguration** är inställd på **på** på servern där du installerar anslutningstjänsten, kan du inte se registreringsskärmen. Följ instruktionerna i felmeddelandet för att få åtkomst. Kontrollera att Förbättrad säkerhetskonfiguration i Internet Explorer är inaktiverat.

Om du vill upprätthålla hög tillgänglighet bör du distribuera minst två anslutningsappar. Varje anslutningsapp måste registreras separat.

## <a name="test-that-the-connector-installed-correctly"></a>Testa att anslutningstjänsten har installerats

Du kan bekräfta att en ny koppling installerats korrekt genom att söka efter den i antingen Azure-portalen eller på din server. 

I Azure-portalen, loggar du in din klient och gå till **Azure Active Directory** > **Application Proxy**. Alla dina anslutningsprogram och anslutningsapp-grupper visas på den här sidan. Välj en anslutning för att visa information om eller flytta den till en annan anslutningsgrupp. 

Kontrollera listan över aktiva tjänster för anslutningen och connector updater på din server. De två tjänsterna ska börja köras omedelbart, men om den inte aktivera dem: 

   * **Microsoft AAD Application Proxy Connector** upprättar anslutningarna.

   * **Microsoft AAD Application Proxy Connector Updater** är en tjänst för automatiska uppdateringar. Uppdateringsfilen söker efter nya versioner av anslutningsverktyget och uppdaterar det efter behov.

   ![Application Proxy Connector-tjänster – skärmbild](./media/application-proxy-enable/app_proxy_services.png)

Information om kopplingar och hur de hålls uppdaterad finns i [alternativ för Azure AD-programproxyn kopplingar](application-proxy-connectors.md).


## <a name="next-steps"></a>Nästa steg
Nu kan du börja [publicera program med Application Proxy](application-proxy-publish-azure-portal.md).

Om du har program som finns på olika nätverk eller olika platser, kan du använda anslutningsapp-grupper för att organisera olika anslutningar i logiska enheter. Läs mer i [Arbeta med programproxyanslutningar](application-proxy-connector-groups.md).
