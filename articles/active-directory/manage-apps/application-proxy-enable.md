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
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 59ca9ca7711904fe7882aac4878bd62c597645d8
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034974"
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Kom igång med Application Proxy och installera connector
I den här artikeln beskrivs steg för steg hur du aktiverar Microsoft Azure AD Application Proxy för din molnkatalog i Azure AD.

Om du inte ännu medveten om säkerhet och produktivitet fördelarna Application Proxy ger för din organisation, Läs mer om [att tillhandahålla säker fjärråtkomst till lokala program](application-proxy.md).

## <a name="application-proxy-prerequisites"></a>Krav för Application Proxy
Innan du kan aktivera och använda Application Proxy-tjänster behöver du:

* En [Basic- eller Premium-prenumeration på Microsoft Azure AD](../fundamentals/active-directory-whatis.md) och en Azure AD-katalog som du är en global administratör för.
* En server som kör Windows Server 2012 R2 eller 2016, där du kan installera Application Proxy Connector. Servern måste kunna ansluta till Application Proxy-tjänsterna i molnet och lokala program som du publicerar.
  * För enkel inloggning till dina publicerade program med hjälp av Kerberos-begränsad delegering, bör den här datorn vara domänansluten i samma AD-domän som de program som du publicerar. Mer information finns i [KCD för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md).
* TLS 1.2 som körs på det underliggande operativsystemet. Om du vill ändra till TLS 1.2, följer du stegen i [Aktivera TLS 1.2](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites#enable-tls-12-for-azure-ad-connect). Innehållet är för Azure AD Connect, är den här proceduren samma för alla .NET-klienter.

Om din organisation använder proxyservrar för att ansluta till internet kan du läsa [fungerar med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md) mer information om hur du konfigurerar dem innan du sätter igång med Application Proxy.

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
