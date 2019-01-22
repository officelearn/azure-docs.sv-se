---
title: Konfigurera Azure MFA Server för hög tillgänglighet | Microsoft Docs
description: Distribuera flera instanser av Azure Multi-Factor Authentication Server i konfigurationer med hög tillgänglighet.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: e8aaf7b3d0ff17682d0288939136cc6d2f1bfd89
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437631"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurera Azure Multi-Factor Authentication-servern för hög tillgänglighet

Du måste distribuera flera MFA-servrar för att uppnå hög tillgänglighet med Azure MFA för Server-distributionen. Det här avsnittet innehåller information om en Utjämning av nätverksbelastning design att uppnå dina mål för hög tillgänglighet i du Azure MFS serverdistribution.

## <a name="mfa-server-overview"></a>Översikt över MFA Server

Azure MFA Server service-arkitektur består av flera komponenter som du ser i följande diagram:

 ![MFA Server-arkitektur](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

En MFA-Server är en Windows-Server med Azure Multi-Factor Authentication program som finns installerade. MFA Server-instansen måste aktiveras av MFA-tjänsten i Azure ska fungera. Mer än en MFA-servern kan vara installerade på plats.

Den första MFA-servern som är installerad är huvudservern MFA vid aktivering av tjänsten Azure MFA som standard. Master MFA-servern har en skrivbar kopia av databasen PhoneFactor.pfdata. Efterföljande installationer av instanser av MFA-Server kallas underordnade. MFA-underordnade ha en replikerad skrivskyddad kopia av PhoneFactor.pfdata-databas. MFA-servrarna replikerar information med hjälp av Remote Procedure Call (RPC). Alla MFA-servrarna måste sammantaget antingen vara domänanslutna eller fristående att replikera information.

Både MFA huvudservrar och underordnade MFA-servrar kommunicerar med MFA-tjänsten när tvåfaktorsautentisering krävs. Till exempel när en användare försöker få åtkomst till ett program som kräver tvåfaktorsautentisering, autentiseras användaren först av en identitetsprovider, till exempel Active Directory (AD).

Efter en lyckad autentisering med AD kommunicerar MFA-servern med MFA-tjänsten. MFA-servern väntar på meddelanden från MFA-tjänsten för att tillåta eller neka användaråtkomst till programmet.

Om MFA-huvudserver kopplas från, autentiseringar kan bearbetas fortfarande, men åtgärder som kräver ändringar i MFA-databasen kan inte bearbetas. (Exempel: tillägg av användare, självbetjäning pinkodsändringar och ändra information om användare)

## <a name="deployment"></a>Distribution

Tänk på följande viktiga för belastningsutjämning i Azure MFA-servern och dess relaterade komponenter.

* **Med RADIUS-standarden för att uppnå hög tillgänglighet**. Du kan potentiellt konfigurera en MFA-Server som en primär RADIUS-mål för autentisering och andra Azure MFA-servrar som mål för sekundär autentisering om du använder Azure MFA-servrar som RADIUS-servrar. Men kanske den här metoden för att uppnå hög tillgänglighet inte praktiska eftersom du måste vänta tills en timeout-period ska uppvisas när autentisering misslyckas på den primära autentisering måldatorn innan du kan autentiseras mot målet för sekundär autentisering. Det är mer effektivt att belastningsutjämna RADIUS-trafik mellan RADIUS-klienten och RADIUS-servrar (i det här fallet Azure MFA-servrar som fungerar som RADIUS-servrar) så att du kan konfigurera RADIUS-klienter med en enskild URL som de kan pekar på.
* **Du måste manuellt uppgradera MFA underordnade**. Om Azure MFA-huvudservern kopplas från, fortsätta sekundärservrar för Azure MFA ska bearbeta begäranden för MFA. Dock tills en MFA-huvudserver finns tillgänglig, Administratörer kan inte lägga till användare eller ändra inställningar för MFA, och användarna kan inte göra ändringar i användarportalen. Uppgradera en MFA är som är underordnad rollen alltid manuellt.
* **Avskiljbarhet komponenter**. Azure MFA Server består av flera komponenter som kan installeras på samma Windows Server-instans eller på olika instanser. Dessa komponenter omfattar Användarportalen och webbtjänsten Mobile App ADFS-adaptern (agent). Den här Avskiljbarhet gör det möjligt att använda Web Application Proxy för att publicera Användarportalen och webbservern för Mobile App från perimeternätverket. En sådan konfiguration lägger till den övergripande säkerheten för din design, som visas i följande diagram. MFA-Användarportalen och webbservern för Mobile App kan också distribueras i konfigurationer för hög tillgänglighet Utjämning av nätverksbelastning.

   ![MFA Server med ett perimeternätverk](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Engångslösenord (OTP) via SMS (även kallat enkelriktad SMS) måste du använda fästsessioner om trafiken är Utjämning av nätverksbelastning**. Enkelriktad SMS är ett alternativ för autentisering som orsakar MFA-servern att skicka ett textmeddelande som innehåller ett Engångslösenord för användarna. Användaren anger OTP i en fråga fönster för att slutföra MFA-kontrollen. Om du belastningsutjämna Azure MFA-servrar måste på samma server som hanteras inledande autentiseringsbegäran vara den server som tar emot OTP-meddelande från användaren. Om en annan MFA-servern tar emot svaret från OTP, misslyckas autentiseringsfråga. Mer information finns i [en tid lösenord via SMS har lagts till Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Utjämning av nätverksbelastning distributioner av Användarportalen och webbtjänsten Mobile App kräver fästsessioner**. Om du belastningsutjämning MFA-Användarportalen och webbtjänsten Mobile App måste varje session stanna kvar på samma server.

## <a name="high-availability-deployment"></a>Hög tillgänglighet i driftsättningen

Följande diagram visar en fullständig HA Utjämning av nätverksbelastning implementering av Azure MFA och dess komponenter, tillsammans med AD FS som referens.

 ![Azure MFA Server HA implementering](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Observera följande objekt för motsvarande numrerade området föregående diagram.

1. De två Azure MFA-servrar (MFA1 och MFA2) belastningsutjämnas (mfaapp.contoso.com) och är konfigurerade för att använda en statisk port (4443) för att replikera PhoneFactor.pfdata-databasen. Webbtjänst-SDK är installerat på var och en av MFA-Server för att aktivera kommunikation via TCP-port 443 med AD FS-servrar. MFA-servrar distribueras i en tillståndslös konfiguration för Utjämning av nätverksbelastning. Men om du vill skicka Engångslösenord via SMS, måste du använda tillståndskänsliga Utjämning av nätverksbelastning.
   ![Azure MFA Server - appservern hög tillgänglighet](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Eftersom RPC använder dynamiska portar, rekommenderas det inte att öppna brandväggar upp till intervallet för dynamiska portar som RPC kan användaren använda. Om du har en brandvägg **mellan** din MFA-programservrar, bör du konfigurera MFA-servern för att kommunicera på en statisk port för replikeringstrafiken mellan underordnade och överordnade servrar och öppna den porten i brandväggen. Du kan tvinga den statiska porten genom att skapa ett DWORD-registervärde på ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` kallas ```Pfsvc_ncan_ip_tcp_port``` och ställer in värdet på en tillgänglig statisk port. Anslutningar initieras alltid av de underordnade servrarna MFA till huvuddatabasen, den statiska porten krävs endast i bakgrunden, men eftersom du kan flytta upp en underordnad server för att vara huvuddatabasen när som helst, bör du ange den statiska porten på alla servrar för MFA.

2. De två användare Portal/MFA-Mobilapp-servrarna (MFA-UP-MAS1 och MFA-UP-MAS2) belastningsutjämnas i en **tillståndskänslig** konfiguration (mfa.contoso.com). Kom ihåg att fästsessioner är ett krav för MFA-Användarportalen och Mobile App-tjänsten för belastningsutjämning.
   ![Azure MFA Server - Användarportalen och Mobiltjänst till App hög tillgänglighet](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. AD FS-servergruppen är load balanced och publiceras till Internet via Utjämning av nätverksbelastning AD FS-proxyservrar i perimeternätverket. Varje AD FS-servern använder AD FS-agenten för att kommunicera med Azure MFA-servrar med hjälp av en enda Utjämning av nätverksbelastning URL (mfaapp.contoso.com) via TCP-port 443.

## <a name="next-steps"></a>Nästa steg

* [Installera och konfigurera Azure MFA Server](howto-mfaserver-deploy.md)
