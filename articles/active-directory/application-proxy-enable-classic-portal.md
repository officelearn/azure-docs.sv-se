---
title: Aktivera Azure AD Application Proxy i den klassiska portalen | Microsoft Docs
description: "Aktivera Azure AD Application Proxy på den klassiska Azure-portalen och installera anslutningsverktyget för den omvända proxyn."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: ea97fdc8d146ed524a932018b572ceda0982738b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-application-proxy-in-the-classic-portal-and-download-connectors"></a>Aktivera Application Proxy i den klassiska portalen och hämta kopplingar
I den här artikeln beskrivs steg för steg hur du aktiverar Microsoft Azure AD Application Proxy för din molnkatalog i Azure AD.

Mer information om hur Application Proxy kan hjälpa dig finns i [Konfigurera säker fjärråtkomst till lokala program](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Krav för Application Proxy
Innan du kan aktivera och använda Application Proxy-tjänster behöver du:

* En [Basic- eller Premium-prenumeration på Microsoft Azure AD](active-directory-editions.md) och en Azure AD-katalog som du är en global administratör för.
* En server som kör Windows Server 2012 R2 eller 2016, där du kan installera Application Proxy Connector. Servern skickar förfrågningar till Application Proxy-tjänsterna i molnet och behöver en HTTP- eller HTTPS-anslutning till de program som du publicerar.
  * När det gäller enkel inloggning till dina publicerade program ska den här datorn vara domänansluten i samma AD-domän som de program som du publicerar. Mer information finns i [enkel inloggning med Application Proxy](active-directory-application-proxy-sso-using-kcd.md)
* Om din organisation använder proxyservrar för att ansluta till internet, kan du läsa [fungerar med befintliga lokala proxyservrar](application-proxy-working-with-proxy-servers.md) mer information om hur du konfigurerar dem.

## <a name="open-your-ports"></a>Öppna portar

För att förbereda din miljö för Azure AD Application Proxy, måste du först aktivera kommunikation till Azure-datacenter. Om det finns en brandvägg i sökvägen måste du se till att den är öppen så att anslutningsappen kan göra HTTPS-förfrågningar (TCP) till programproxyn.

1. Öppna följande portar till **utgående** trafik:

   | Portnummer | Hur den används |
   | --- | --- |
   | 80 | Hämta certifikatåterkallning listor över återkallade vid verifiering av SSL-certifikatet |
   | 443 | All utgående kommunikation med tjänsten Application Proxy |

   Om brandväggstrafiken hanteras baserat på användarna som genererar den öppnar du dessa portar för trafik som kommer från Windows-tjänster som körs som en nätverkstjänst.

   > [!IMPORTANT]
   > Tabellen visar portkrav för koppling versioner 1.5.132.0 och senare. Om du fortfarande har en äldre version av koppling, du måste också aktivera följande portar: 5671, 8080, 9090, 9091, 9350, 9352 och 10100 – 10120.
   >
   >Information om hur du uppdaterar din kopplingar till den senaste versionen finns [förstå Azure AD Application Proxy kopplingar](application-proxy-understand-connectors.md#automatic-updates).

2. Om din brandvägg eller proxyserver kan vitlistning av DNS kan du godkända anslutningar till msappproxy.net och servicebus.windows.net. Om inte, måste du tillåta åtkomst till den [Azure DataCenter-IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka.

3. Använd den [Azure AD Application Proxy Connector portar Test Tool](https://aadap-portcheck.connectorporttest.msappproxy.net/) att verifiera att din connector kan nå tjänsten Application Proxy. Kontrollera att den centrala USA och region som är närmast dig har alla gröna bockmarkeringarna minimum. Utöver det kan innebär mer gröna bockmarkeringarna större flexibilitet.

## <a name="enable-application-proxy-in-azure-ad"></a>Aktivera Application Proxy i Azure AD
1. Logga in som administratör på [den klassiska Azure-portalen](https://manage.windowsazure.com/).
2. Gå till Active Directory och välj den katalog som du vill aktivera Application Proxy i.

    ![Active Directory – ikon](./media/active-directory-application-proxy-enable/ad_icon.png)
3. Välj **Konfigurera** på katalogsidan och rulla ned till **Webbprogramproxy**.
4. Ändra **Aktivera programproxytjänster för den här katalogen** till **Aktiverad**.

    ![Aktivera Application Proxy](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. Välj **Hämta nu**. Den **Azure AD Application Proxy Connector hämta** öppnas. Läs och godkänn licensvillkoren och spara Windows Installer-filen (.exe) för anslutningsappen genom att klicka på **Ladda ned**.

## <a name="install-and-register-the-connector"></a>Installera och registrera anslutningsverktyget
1. Kör **AADApplicationProxyConnectorInstaller.exe** på den server som du har förberett och som uppfyller kraven.
2. Följ installationsanvisningarna i guiden.
3. Under installationen uppmanas du att registrera anslutningsverktyget med programproxyn för din Azure AD-klient.

   * Ange dina autentiseringsuppgifter som global Azure AD-administratör. Autentiseringsuppgifterna för klienten som du är global administratör för kan skilja sig från dina Microsoft Azure-autentiseringsuppgifter.
   * Se till att den administratör som registrerar anslutningsappen finns i samma katalog där du har aktiverat programproxytjänsten. Om klientdomänen exempelvis är contoso.com ska administratören vara admin@contoso.com eller något annat alias på den domänen.
   * Om **Förbättrad säkerhetskonfiguration** är inställd på **på** på servern, registreringsskärmen kan blockeras. Följ instruktionerna i felmeddelandet för att tillåta åtkomst. Kontrollera att Förbättrad säkerhetskonfiguration i Internet Explorer är inaktiverat.
   * Om registreringen av anslutningsappen inte lyckas så gå till [Felsöka programproxyn](active-directory-application-proxy-troubleshoot.md).  
4. När installationen är klar läggs två nya tjänster till på servern:

   * **Microsoft AAD Application Proxy Connector** upprättar anslutningarna.

     * **Microsoft AAD Application Proxy Connector Updater** är en tjänst för automatiska uppdateringar. Med jämna mellanrum söker efter nya versioner av anslutningsverktyget och uppdaterar det efter behov.

     ![Application Proxy Connector-tjänster – skärmbild](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. Klicka på **Slutför** i installationsfönstret.

Information om anslutningsappar finns i avsnittet [Understand Azure AD Application Proxy connectors](application-proxy-understand-connectors.md) (Förstå anslutningsappar för Azure AD Application Proxy).

Om du vill upprätthålla hög tillgänglighet bör du distribuera minst två anslutningsappar. Upprepa steg 2 och 3 för att distribuera fler kopplingar. Varje anslutningsapp måste registreras separat.

Om du vill avinstallera anslutningsappen måste du avinstallera både anslutnings- och uppdateringstjänsten. Ta bort tjänsten helt och hållet genom att starta om datorn.

## <a name="next-steps"></a>Nästa steg
Nu kan du börja [publicera program med Application Proxy](active-directory-application-proxy-publish.md).

Om du har program som finns på olika nätverk eller olika platser kan du använda anslutningsgrupper för att organisera olika anslutningar i logiska enheter. Läs mer i [Arbeta med programproxyanslutningar](active-directory-application-proxy-connectors.md).
