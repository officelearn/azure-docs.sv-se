---
title: Publicera fjärrskrivbord med Azure AD App Proxy | Microsoft-dokument
description: Täcker grunderna om Azure AD Application Proxy-kopplingar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6ca64e2de5734c567173fc735776074f4c87fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108467"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publicera fjärrskrivbord med Azure AD-programproxy

Remote Desktop Service och Azure AD Application Proxy arbetar tillsammans för att förbättra produktiviteten för arbetstagare som är borta från företagsnätverket. 

Den avsedda målgruppen för den här artikeln är:
- Aktuella Application Proxy-kunder som vill erbjuda fler program till sina slutanvändare genom att publicera lokala program via Fjärrskrivbordstjänster.
- Aktuella kunder för Fjärrskrivbordstjänster som vill minska angreppsytan för sin distribution med hjälp av Azure AD Application Proxy. Det här scenariot ger en begränsad uppsättning kontroller med två steg och villkorlig åtkomst till RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Så här passar Programproxy i standarddistributionen av RDS

En standarddistribution av fjärrskrivbordstjänster innehåller olika rolltjänster för fjärrskrivbord som körs på Windows Server. Om du tittar på [arkitekturen Fjärrskrivbordstjänster](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)finns det flera distributionsalternativ. Till skillnad från andra rds-distributionsalternativ har [RDS-distributionen med Azure AD Application Proxy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (som visas i följande diagram) en permanent utgående anslutning från servern som kör anslutningstjänsten. Andra distributioner lämnar öppna inkommande anslutningar via en belastningsutjämnare.

![Programproxy sitter mellan den virtuella datorn för fjärrskrivbord och det offentliga internet](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

I en RDS-distribution körs webbrollen för fjärrskrivbord och rollen fjärrskrivbordsgateway på internetinriktade datorer. Dessa effektmått exponeras av följande skäl:
- Rd Web ger användaren en offentlig slutpunkt för att logga in och visa de olika lokala program och skrivbord som de kan komma åt. När du väljer en resurs skapas en RDP-anslutning med hjälp av den inbyggda appen i operativsystemet.
- Rd Gateway kommer in i bilden när en användare startar RDP-anslutningen. Rd Gateway hanterar krypterad RDP-trafik som kommer över internet och översätter den till den lokala servern som användaren ansluter till. I det här fallet kommer trafiken som fjärrskrivbordsgatewayen tar emot från Azure AD Application Proxy.

>[!TIP]
>Om du inte har distribuerat FJÄRRSKRIVBORD TIDIGARE, eller vill ha mer information innan du börjar, lär du dig hur du [sömlöst distribuerar RDS med Azure Resource Manager och Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Krav

- Använd en annan klient än webbklienten för fjärrskrivbord, eftersom webbklienten inte stöder programproxy.

- Slutpunkterna för fjärrskrivbords webb- och fjärrskrivbordsgateway måste finnas på samma dator och med en gemensam rot. Webb- och fjärrskrivbordsgateway publiceras som ett enda program med Application Proxy så att du kan få en enda inloggning mellan de två programmen.

- Du bör redan ha [distribuerat RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)och [aktiverat Programproxy](application-proxy-add-on-premises-application.md).

- Det här scenariot förutsätter att dina slutanvändare går via Internet Explorer på Windows 7- eller Windows 10-skrivbord som ansluter via webbsidan för fjärrskrivbord. Om du behöver stödja andra operativsystem läser du [Stöd för andra klientkonfigurationer](#support-for-other-client-configurations).

- När du publicerar webb för fjärrskrivbord rekommenderar vi att du använder samma interna och externa FQDN. Om de interna och externa FQDN:erna är olika bör du inaktivera Översättning av begärhuvud för att undvika att klienten tar emot ogiltiga länkar. 

- Aktivera ActiveX-tillägget för RDS i Internet Explorer.

- För Azure AD-flödet före autentisering kan användare bara ansluta till resurser som publiceras till dem i fönstret **RemoteApp och stationära datorer.** Användare kan inte ansluta till ett skrivbord med fönstret **Anslut till en fjärrdator.**

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Distribuera det gemensamma RDS- och Application Proxy-scenariot

När du har konfigurerat RDS och Azure AD Application Proxy för din miljö följer du stegen för att kombinera de två lösningarna. De här stegen går igenom publiceringen av de två webbinriktade RDS-slutpunkterna (Rd Web och RD Gateway) som program och dirigerar sedan trafik på din RDS för att gå igenom Programproxy.

### <a name="publish-the-rd-host-endpoint"></a>Publicera slutpunkten för fjärrskrivbordsvärden

1. [Publicera ett nytt programproxyprogram](application-proxy-add-on-premises-application.md) med följande värden:
   - Intern URL: `https://\<rdhost\>.com/` `\<rdhost\>` , där är den vanliga roten som rd Web och RD Gateway-resurs.
   - Extern URL: Det här fältet fylls i automatiskt baserat på namnet på programmet, men du kan ändra det. Användarna går till den här webbadressen när de använder RDS.
   - Förautentiseringsmetod: Azure Active Directory
   - Översätt URL-rubriker: Nej
2. Tilldela användare till det publicerade fjärrskrivbordsprogrammet. Se till att de alla har tillgång till RDS också.
3. Lämna den enda inloggningsmetoden för programmet som **azure AD enkel inloggning inaktiverad**. Användarna uppmanas att autentisera en gång till Azure AD och en gång till rd web, men har enkel inloggning till fjärrskrivbordsgateway.
4. Välj **Azure Active Directory**och sedan **Appregistreringar**. Välj din app i listan.
5. Under **Hantera**väljer du **Branding**.
6. Uppdatera **URL-fältet** för startsidan så att det `https://\<rdhost\>.com/RDWeb`pekar på slutpunkten för fjärrskrivbords webb (till exempel ).

### <a name="direct-rds-traffic-to-application-proxy"></a>Dirigera RDS-trafik till programproxy

Anslut till RDS-distributionen som administratör och ändra servernamnet för fjärrskrivbordsgateway för distributionen. Den här konfigurationen säkerställer att anslutningar går via Azure AD Application Proxy-tjänsten.

1. Anslut till rds-servern som kör rollen Anslutningsutjämning för fjärrskrivbord.
2. Starta **Serverhanteraren**.
3. Välj **Fjärrskrivbordstjänster** i fönstret till vänster.
4. Välj **Översikt**.
5. I avsnittet Distributionsöversikt markerar du den nedrullningsbara menyn och väljer **Redigera distributionsegenskaper**.
6. På fliken Fjärrskrivbordsgateway ändrar du fältet **Servernamn** till den externa URL:en som du anger för slutpunkten för fjärrskrivbordsvärden i Programproxy.
7. Ändra fältet **Inloggningsmetod** till **Lösenordsautentisering**.

   ![Skärmen Distributionsegenskaper på RDS](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Kör det här kommandot för varje samling. Ersätt * \<ditt samlingsnamn\> * och * \<proxyfrontendurl\> * med din egen information. Med det här kommandot kan du logga in mellan webb- och fjärrskrivbordsgateway och prestanda:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Till exempel:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Ovanstående kommando använder en backtick i "'nrequire".

9. Om du vill verifiera ändringen av de anpassade RDP-egenskaperna samt visa rdp-filinnehållet som hämtas från RDWeb för den här samlingen kör du följande kommando:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Nu när du har konfigurerat Fjärrskrivbord har Azure AD Application Proxy tagits över som internet-facing komponent i RDS. Du kan ta bort andra offentliga internetvända slutpunkter på webb- och fjärrskrivbordsdatormaskinerna.

## <a name="test-the-scenario"></a>Testa scenariot

Testa scenariot med Internet Explorer på en Windows 7- eller 10-dator.

1. Gå till den externa webbadressen som du konfigurerar eller hitta programmet på [panelen Mina program](https://myapps.microsoft.com).
2. Du uppmanas att autentisera till Azure Active Directory. Använd ett konto som du har tilldelat programmet.
3. Du uppmanas att autentisera till Rd Web.
4. När rds-autentiseringen lyckas kan du välja det skrivbord eller program du vill ha och börja arbeta.

## <a name="support-for-other-client-configurations"></a>Stöd för andra klientkonfigurationer

Konfigurationen som beskrivs i den här artikeln är avsedd för användare i Windows 7 eller 10, med Internet Explorer plus RDS ActiveX-tillägget. Om du behöver kan du dock stödja andra operativsystem eller webbläsare. Skillnaden är i den autentiseringsmetod som du använder.

| Autentiseringsmetod | Klientkonfiguration som stöds |
| --------------------- | ------------------------------ |
| Förautentisering    | Windows 7/10 med Internet Explorer + RDS ActiveX-tillägg |
| Genomströmning | Alla andra operativsystem som stöder Microsoft Remote Desktop-programmet |

Flödet före autentisering ger fler säkerhetsfördelar än genomströmningsflödet. Med pre-autentisering kan du använda Azure AD-autentiseringsfunktioner som enkel inloggning, villkorlig åtkomst och tvåstegsverifiering för dina lokala resurser. Du ser också till att endast autentrad trafik når nätverket.

Om du vill använda vidareutgåsautentisering finns det bara två ändringar i stegen i den här artikeln:
1. I [Publicera slutpunktsslutpunkten för fjärrskrivbordsvärden](#publish-the-rd-host-endpoint) 1 anger du metoden Förautentisering till **Passthrough**.
2. Hoppa över steg 8 helt i [Direkt RDS-trafik till Programproxy.](#direct-rds-traffic-to-application-proxy)

## <a name="next-steps"></a>Nästa steg

[Aktivera fjärråtkomst till SharePoint med Azure AD-programproxy](application-proxy-integrate-with-sharepoint-server.md)  
[Säkerhetsöverväganden för åtkomst till appar på distans med hjälp av Azure AD Application Proxy](application-proxy-security.md)
