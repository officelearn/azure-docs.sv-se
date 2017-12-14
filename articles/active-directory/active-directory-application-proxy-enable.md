---
title: "Azure AD App Proxy - börja installera connector | Microsoft Docs"
description: "Aktivera Application Proxy på Azure-portalen och installera anslutningsverktyget för omvänd proxy."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 09497144ef4047c1191f3c02afccb881d48aaf3c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Kom igång med Application Proxy och installera connector
I den här artikeln beskrivs steg för steg hur du aktiverar Microsoft Azure AD Application Proxy för din molnkatalog i Azure AD.

Om du inte är medveten om säkerhet och produktivitet fördelarna Application Proxy ger för din organisation, lär du dig mer om [ge säker fjärråtkomst till lokala program](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Krav för Application Proxy
Innan du kan aktivera och använda Application Proxy-tjänster behöver du:

* En [Basic- eller Premium-prenumeration på Microsoft Azure AD](active-directory-editions.md) och en Azure AD-katalog som du är en global administratör för.
* En server som kör Windows Server 2012 R2 eller 2016, där du kan installera Application Proxy Connector. Servern behöver för att kunna ansluta till Application Proxy-tjänster i molnet och lokala program som du publicerar.
  * För enkel inloggning till ditt publicerade program med hjälp av Kerberos-begränsad delegering, måste den här datorn vara ansluten till domänen i samma AD-domän som de program som du publicerar. Mer information finns i [KCD för enkel inloggning med Application Proxy](active-directory-application-proxy-sso-using-kcd.md).

Om din organisation använder proxyservrar för att ansluta till internet, kan du läsa [fungerar med befintliga lokala proxyservrar](application-proxy-working-with-proxy-servers.md) mer information om hur du konfigurerar dem innan du börjar med Application Proxy.

## <a name="open-your-ports"></a>Öppna portar

För att förbereda din miljö för Azure AD Application Proxy, måste du först aktivera kommunikation till Azure-datacenter. Om det finns en brandvägg i sökvägen måste du se till att den är öppen så att anslutningsappen kan göra HTTPS-förfrågningar (TCP) till programproxyn.

1. Öppna följande portar till **utgående** trafik:

   | Portnummer | Hur den används |
   | --- | --- |
   | 80 | Hämta certifikatåterkallning listor över återkallade vid verifiering av SSL-certifikatet |
   | 443 | All utgående kommunikation med tjänsten Application Proxy |

   Om brandväggen tillämpar trafik enligt ursprung användare, kan du öppna dessa portar för trafik från Windows-tjänster som körs som Nätverkstjänst.

   > [!IMPORTANT]
   > Tabellen visar portkrav för koppling versioner 1.5.132.0 och senare. Om du fortfarande har en äldre version av koppling, du måste också aktivera följande portar förutom 80 och 443: 5671 8080 9090 9091 9350 9352 10100 – 10120.
   >
   >Information om hur du uppdaterar din kopplingar till den senaste versionen finns [förstå Azure AD Application Proxy kopplingar](application-proxy-understand-connectors.md#automatic-updates).

2. Om din brandvägg eller proxyserver kan vitlistning av DNS kan du godkända anslutningar till msappproxy.net och servicebus.windows.net. Om inte, måste du tillåta åtkomst till den [Azure DataCenter-IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka.

3. Microsoft använder fyra adresser för att verifiera certifikat. Tillåt åtkomst till följande URL-adresser om du inte gjort det för andra produkter:
   * mscrl.microsoft.com:80
   * CRL.microsoft.com:80
   * OCSP.msocsp.com:80
   * www.microsoft.com:80

4. Din anslutningstjänst behöver åtkomst till login.windows.net och login.microsoftonline.com för registreringen.

5. Använd den [Azure AD Application Proxy Connector portar Test Tool](https://aadap-portcheck.connectorporttest.msappproxy.net/) att verifiera att din connector kan nå tjänsten Application Proxy. Kontrollera att den centrala USA och region som är närmast dig har alla gröna bockmarkeringarna minimum. Utöver det kan innebär mer gröna bockmarkeringarna större flexibilitet.

## <a name="install-and-register-a-connector"></a>Installera och registrera en koppling
1. Logga in som administratör i den [Azure-portalen](https://portal.azure.com/).
2. Den aktuella katalogen visas under ditt användarnamn i det övre högra hörnet. Om du behöver ändra kataloger väljer du ikonen.
3. Gå till **Azure Active Directory** > **programproxy**.

   ![Navigera till Application Proxy](./media/active-directory-application-proxy-enable/app_proxy_navigate.png)

4. Välj **hämta Connector**.

   ![Hämta Connector](./media/active-directory-application-proxy-enable/download_connector.png)

5. Kör **AADApplicationProxyConnectorInstaller.exe** på den server som du har förberett och som uppfyller kraven.
6. Följ installationsanvisningarna i guiden. Under installationen uppmanas du att registrera anslutningsverktyget med programproxyn för din Azure AD-klient.

   * Ange dina autentiseringsuppgifter som global Azure AD-administratör. Autentiseringsuppgifterna för klienten som du är global administratör för kan skilja sig från dina Microsoft Azure-autentiseringsuppgifter.
   * Se till att den administratör som registrerar anslutningsappen finns i samma katalog där du har aktiverat programproxytjänsten. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat alias på den domänen.
   * Om **Förbättrad säkerhetskonfiguration** är inställd på **på** på servern där du installerar anslutningen, kanske inte visas registreringsskärmen. Följ instruktionerna i felmeddelandet för att få åtkomst. Kontrollera att Förbättrad säkerhetskonfiguration i Internet Explorer är inaktiverat.

Om du vill upprätthålla hög tillgänglighet bör du distribuera minst två anslutningsappar. Varje anslutningsapp måste registreras separat.

## <a name="test-that-the-connector-installed-correctly"></a>Kontrollera att anslutningen har installerats

Du kan bekräfta att en ny koppling installerats korrekt genom att söka efter den antingen i Azure-portalen eller på servern. 

Logga in på Azure-portalen på din klient och gå till **Azure Active Directory** > **Application Proxy**. Alla kopplingar och connector grupper visas på den här sidan. Välj en koppling för att visa information eller flytta den till en annan koppling grupp. 

Kontrollera listan över aktiva tjänster för anslutningen och connector updater på servern. De två tjänsterna ska börja köras omedelbart, men om inte, aktivera dem: 

   * **Microsoft AAD Application Proxy Connector** upprättar anslutningarna.

   * **Microsoft AAD Application Proxy Connector Updater** är en tjänst för automatiska uppdateringar. Uppdateringsfilen söker efter nya versioner av anslutningsverktyget och uppdaterar det efter behov.

   ![Application Proxy Connector-tjänster – skärmbild](./media/active-directory-application-proxy-enable/app_proxy_services.png)

Information om kopplingar och hur de hålls uppdaterad finns [förstå Azure AD Application Proxy kopplingar](application-proxy-understand-connectors.md).


## <a name="next-steps"></a>Nästa steg
Nu kan du börja [publicera program med Application Proxy](application-proxy-publish-azure-portal.md).

Om du har program som finns i separata nätverk eller olika platser kan du använda anslutningstjänsten grupper för att organisera olika kopplingar i logiska enheter. Läs mer i [Arbeta med programproxyanslutningar](active-directory-application-proxy-connectors-azure-portal.md).
