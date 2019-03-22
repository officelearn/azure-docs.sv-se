---
title: Publicera Fjärrskrivbord med Azure AD App Proxy | Microsoft Docs
description: Beskriver grunderna om Azure AD Application Proxy-kopplingar.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: celested
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 295422e0f456c4dfd4166911ef8150e8a896ba1a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58111114"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publicera Fjärrskrivbord med Azure AD Application Proxy

Fjärrskrivbordstjänst och Azure AD Application Proxy fungerar tillsammans för att förbättra produktiviteten för personer som är ansluten till företagets nätverket. 

Målgruppen för den här artikeln är:
- Aktuella Application Proxy-kunder som vill erbjuda fler program slutanvändarna genom att publicera lokala program via Fjärrskrivbordstjänster.
- Aktuella Remote Desktop Services-kunder som vill minska risken för angrepp på deras distribution med hjälp av Azure AD-programproxy. Det här scenariot ger en begränsad uppsättning tvåstegsverifiering och kontroller för villkorlig åtkomst till Fjärrskrivbordstjänster.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Hur Application Proxy passar in i standard RDS-distribution

En vanlig RDS-distribution innehåller olika rolltjänsterna för fjärrskrivbord som körs på Windows Server. Titta på den [Remote Desktop Services arkitektur](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture), det finns flera distributionsalternativ. Till skillnad från andra alternativ för distribution av Fjärrskrivbordstjänster, den [RDS-distribution med Azure AD Application Proxy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (visas i följande diagram) har en permanent utgående anslutning från den server som kör kopplingstjänsten. Andra distributioner lämna öppna inkommande anslutningar via en belastningsutjämnare.

![Programproxyn är placerad mellan RDS VM och det offentliga internet](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

I en RDS-distribution köra RD Web-roll och rollen för fjärrskrivbordsgateway på Internet-riktade datorer. De här slutpunkterna exponeras av följande skäl:
- Webbåtkomst ger användaren en offentlig slutpunkt för att logga in och visa de olika lokala program och skrivbord som de kan komma åt. När du har valt en resurs skapas en RDP-anslutning med hjälp av den inbyggda appen i Operativsystemet.
- RD Gateway kommer in i bilden när en användare startar RDP-anslutningen. RD Gateway hanterar krypterade RDP-trafik via internet, omvandlar det till den lokala servern som användaren ansluter till. I det här scenariot kommer RD Gateway tar emot trafiken från Azure AD-programproxyn.

>[!TIP]
>Om du inte har distribuerat RDS innan eller vill ha mer information innan du börjar, lär du dig hur du [smidigt distribuera RDS med Azure Resource Manager och Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Krav

- Använda en klient än webbklienten fjärrskrivbord eftersom webbklienten inte stöder Application Proxy.

- Både webbåtkomst och fjärrskrivbordsgateway slutpunkter måste finnas på samma dator, och med rot. Webbåtkomst och fjärrskrivbordsgateway publiceras som ett enda program med programproxy så att du kan ha en enkel inloggning mellan de två programmen.

- Du bör redan ha [distribueras Fjärrskrivbordstjänster](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure), och [aktiverat programproxyn](application-proxy-add-on-premises-application.md).

- Det här scenariot förutsätter att slutanvändarna går via Internet Explorer på Windows 7 eller Windows 10-datorer som ansluter via sidan webbåtkomst. Om du behöver stöd för andra operativsystem, se [stöd för andra klientkonfigurationer](#support-for-other-client-configurations).

- När du publicerar webbåtkomst, rekommenderar vi att du använder samma FQDN för interna och externa. Om interna och externa FQDN: er är olika bör du inaktivera begär rubrik översättning för att undvika att klienten tar emot ogiltiga länkar. 

- Aktivera RDS ActiveX-tillägg i Internet Explorer.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Distribuera det gemensamma RDS och Application Proxy-scenariot

När du har installerat RDS och Azure AD-programproxy för din miljö, följer du stegen för att kombinera de två lösningarna. De här stegen igenom publicera de två web-RDS slutpunkterna (webbåtkomst och RD Gateway) som program och sedan dirigera trafik på din RDS gå igenom Application Proxy.

### <a name="publish-the-rd-host-endpoint"></a>Publicera slutpunkten för RD-värd

1. [Publicera ett nytt Application Proxy-program](application-proxy-add-on-premises-application.md) med följande värden:
   - Intern URL: `https://\<rdhost\>.com/`, där `\<rdhost\>` är vanliga roten som webbåtkomst och fjärrskrivbordsgateway delar.
   - Externa URL: en: Det här fältet fylls i automatiskt baserat på namnet på programmet, men du kan ändra den. Användarna kommer att gå till denna URL när de ansluter till Fjärrskrivbordstjänster.
   - Förautentiseringsmetoden: Azure Active Directory
   - Översätt URL-huvuden: Nej
2. Tilldela användare till det publicerade RD-programmet. Kontrollera att de har åtkomst till RDS, för.
3. Lämna den enda inloggnings-metoden för programmet som **Azure AD enkel inloggning inaktiverad**. Användarna uppmanas att autentisera en gång till Azure AD och en gång till webbåtkomst, men har enkel inloggning till RD Gateway.
4. Gå till **Azure Active Directory** > **Appregistreringar** > *programmets* > **inställningar**.
5. Välj **egenskaper** och uppdatera den **-URL-Adressen** fält så att den pekar till Webbåtkomst slutpunkten (t.ex. `https://\<rdhost\>.com/RDWeb`).

### <a name="direct-rds-traffic-to-application-proxy"></a>RDS trafiken till Application Proxy

Anslut till RDS-distribution som administratör och byta namn på RD Gateway-servern för distributionen. Den här konfigurationen säkerställer att anslutningarna går via Azure AD Application Proxy-tjänsten.

1. Anslut till RDS-server som kör rollen Anslutningsutjämning för fjärrskrivbord.
2. Starta **Serverhanteraren**.
3. Välj **Remote Desktop Services** från fönstret till vänster.
4. Välj **Översikt**.
5. Välj den nedrullningsbara menyn i översiktsavsnittet för distribution och välj **redigera distributionsegenskaperna**.
6. På fliken RD Gateway kan ändra den **servernamn** automatiskt till den externa URL: en som du anger för slutpunkten för RD-värden i Application Proxy.
7. Ändra den **logga in metod** fältet **lösenordsautentisering**.

   ![Egenskaper för distributionsskärmen på Fjärrskrivbordstjänster](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Kör det här kommandot för varje samling. Ersätt *\<yourcollectionname\>* och *\<proxyfrontendurl\>* med din egen information. Det här kommandot aktiverar enkel inloggning mellan webbåtkomst och fjärrskrivbordsgateway och optimerar prestanda:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Till exempel:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Kommandot ovan använder ett backtick i ”' nrequire”.

9. För att verifiera ändringen av de anpassade RDP-egenskaperna och visa innehållet för RDP-fil som ska laddas ned från RDWeb för den här samlingen, kör du följande kommando:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Nu när du har konfigurerat fjärrskrivbord kan har Azure AD-programproxyn övertagit som internet-riktade komponent i Fjärrskrivbordstjänster. Du kan ta bort de andra offentliga internet-riktade slutpunkterna på dina webbåtkomst och RD Gateway-datorer.

## <a name="test-the-scenario"></a>Testa scenariot

Testa scenariot med Internet Explorer på en Windows 7 eller 10-dator.

1. Gå till den externa URL: en som du har konfigurerat eller hitta ditt program i den [MyApps panelen](https://myapps.microsoft.com).
2. Du uppmanas att autentisera till Azure Active Directory. Använd ett konto som du tilldelat till programmet.
3. Du uppmanas att autentisera till webbåtkomst.
4. När RDS autentiseringen lyckas, kan du väljer du desktop eller program som du vill och börjar arbeta.

## <a name="support-for-other-client-configurations"></a>Stöd för andra klientkonfigurationer

Konfigurationen som beskrivs i den här artikeln är avsedd för användare på Windows 7 eller 10, med Internet Explorer plus RDS ActiveX-tillägg. Om du vill, men har du stöd för andra operativsystem eller webbläsare. Skillnaden är i den autentiseringsmetod som du använder.

| Autentiseringsmetod | Stöds-klientkonfiguration |
| --------------------- | ------------------------------ |
| Förautentisering    | Windows 7/10 med hjälp av Internet Explorer + RDS ActiveX-tillägg |
| Genomströmning | Alla andra operativsystem som stöder Microsoft Remote Desktop-program |

Flödet för förautentisering erbjuder mer säkerhetsfördelarna än genomströmning flödet. Du kan använda Azure AD-autentisering-funktioner som enkel inloggning, villkorlig åtkomst och tvåstegsverifiering för dina lokala resurser med förautentisering. Du kan också kontrollera som endast autentiserad trafik når ditt nätverk.

Om du vill använda direktautentisering, finns det bara två ändringar i stegen i den här artikeln:
1. I [publicera RD värd slutpunkten](#publish-the-rd-host-endpoint) steg 1 genom att ange förautentiseringsmetoden **genomströmning**.
2. I [direkt RDS-trafik till Application Proxy](#direct-rds-traffic-to-application-proxy), hoppa över steg 8 helt och hållet.

## <a name="next-steps"></a>Nästa steg

[Aktivera fjärråtkomst till SharePoint med Azure AD Application Proxy](application-proxy-integrate-with-sharepoint-server.md)  
[Säkerhetsöverväganden för att komma åt appar via fjärranslutning med hjälp av Azure AD Application Proxy](application-proxy-security.md)
