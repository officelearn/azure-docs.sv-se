---
title: "Konfigurera Azure MFA-Server för hög tillgänglighet | Microsoft Docs"
description: "Distribuera flera instanser av Azure Multi-Factor Authentication-Server i konfigurationer som ger hög tillgänglighet."
services: multi-factor-authentication
keywords: Azure MFA
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: c2f9a2422ea5a47edd80948073c7f7a6cb0d0945
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurera Azure Multi-Factor Authentication-servern för hög tillgänglighet

För att uppnå hög tillgänglighet med Azure MFA för Server-distribution, måste du distribuera flera servrar för MFA. Det här avsnittet innehåller information om en belastningsutjämnad design att uppnå dina mål för hög tillgänglighet i du Azure MFS Server-distributionen.

## <a name="mfa-server-overview"></a>Översikt över MFA-Server

Azure MFA-servertjänst består av flera komponenter som visas i följande diagram:

 ![Arkitekturen för MFA-Server](./media/mfa-server-high-availability/mfa-ha-architecture.png)

En MFA-Server är en Windows-Server med Azure Multi-Factor Authentication-programvaran. MFA-Server-instansen måste aktiveras av MFA-tjänsten i Azure ska fungera. Mer än en MFA-Server kan vara installerad lokalt.

Den första MFA-Server som är installerad är master MFA-servern vid aktivering av tjänsten Azure MFA som standard. Huvud MFA-servern har en skrivbar kopia av PhoneFactor.pfdata-databasen. Efterföljande installationer av instanser av MFA-Server kallas slaves. MFA-slaves har en replikerad skrivskyddad kopia av databasen PhoneFactor.pfdata. MFA-servrarna replikerar information med hjälp av Remote Procedure Call (RPC). Alla MFA-servrarna måste gemensamt antingen vara domänanslutna eller fristående för att replikera information.

Både MFA master och slav MFA-servrar kommunicerar med MFA-tjänsten när tvåfaktorsautentisering krävs. Till exempel när en användare försöker få åtkomst till ett program som kräver tvåfaktorsautentisering, autentiseras användaren först genom en identitetsleverantör, till exempel Active Directory (AD).

Efter en lyckad autentisering med AD kommunicerar MFA-servern med MFA-tjänsten. MFA-servern väntar på meddelanden från MFA-tjänsten för att tillåta eller neka användaråtkomst till programmet.

Om MFA huvudservern kopplas från autentiseringar kan fortfarande bearbetas, men åtgärder som kräver ändringar i MFA-databasen kan inte bearbetas. (Exempel: tillägg av användare, självbetjäning pinkodsändringar och ändra information om användare)

## <a name="deployment"></a>Distribution

Tänk på följande viktiga för belastningsutjämning Azure MFA-Server och dess relaterade komponenter.

* **Använda RADIUS-standarden för att uppnå hög tillgänglighet**. Om du använder Azure MFA-servrar som RADIUS-servrar konfigurera du eventuellt en MFA-Server som en primär RADIUS-mål för autentisering och andra Azure MFA-servrar som mål för sekundära autentiseringen. Men kanske den här metoden för att uppnå hög tillgänglighet inte praktiskt eftersom du måste vänta tills en timeout-period när autentiseringen misslyckas på målet för primär autentisering innan du kan autentiseras mot målet sekundära autentiseringen. Det är mer effektivt att belastningsutjämna RADIUS-trafik mellan RADIUS-klienten och RADIUS-servrar (i det här fallet Azure MFA-servrar som fungerar som RADIUS-servrar) så att du kan konfigurera RADIUS-klienter med en enskild URL som de kan pekar på.
* **Du måste manuellt uppgradera MFA slaves**. Om Azure MFA huvudservern kopplas från, fortsätta sekundärservrar för Azure MFA ska bearbeta begäranden för MFA. Dock tills en huvudserver MFA finns tillgänglig, Domänadministratörer kan inte lägga till användare eller ändra inställningar för MFA och användare kan inte göra ändringar i användarportalen. Uppgradera en MFA-slavserver till rollen är alltid en manuell process.
* **Avskiljbarhet komponenter**. Azure MFA-Server består av flera komponenter som kan installeras på samma Windows Server-instans eller på olika instanser. Dessa komponenter omfattar Användarportalen, Mobilappwebbtjänsten och AD FS-kort (agent). Den här Avskiljbarhet gör det möjligt att använda Web Application Proxy för att publicera Användarportalen och Mobile App-webbservern från perimeternätverket. En sådan konfiguration lägger till den övergripande säkerheten för din design som visas i följande diagram. MFA Användarportalen och Mobile App webbservern kan också distribueras i belastningsutjämnade konfigurationer med hög tillgänglighet.

   ![MFA-servern med ett perimeternätverk](./media/mfa-server-high-availability/mfasecurity.png)

* **Engångslösenord (OTP) via SMS (aka enkelriktad SMS) kräver att Fäst sessioner om trafiken är belastningsutjämnad**. Enkelriktad SMS är ett alternativ för autentisering som orsakar MFA-servern att skicka ett SMS med ett Engångslösenord för användarna. Användaren anger OTP i en fråga fönster för att slutföra MFA-kontrollen. Om du belastningsutjämna Azure MFA-servrar måste på samma server som den första autentiseringen begäranden behandlades vara den server som tar emot meddelandet OTP från användaren. Om en annan MFA-servern tar emot svaret Engångslösenord, autentisering-anropet att misslyckas. Mer information finns i [en gång lösenord via SMS lagts till i Azure MFA-Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Utjämning av nätverksbelastning distributioner av Användarportalen och Mobilappwebbtjänsten kräver Fäst sessioner**. Om du belastningsutjämning Användarportalen MFA och den Mobilappwebbtjänsten, måste varje session förblir på samma server.

## <a name="high-availability-deployment"></a>Distribution med hög tillgänglighet

Följande diagram visar en fullständig HA belastningsutjämnade implementering av Azure MFA och dess komponenter, tillsammans med AD FS för referens.

 ![Azure MFA-servern HA implementering](./media/mfa-server-high-availability/mfa-ha-deployment.png)

Observera följande objekt för motsvarande numrerade området föregående diagram.

1. De två Azure MFA-servrar (MFA1 och MFA2) belastningsutjämnas (mfaapp.contoso.com) och har konfigurerats för att använda en statisk port (4443) för att replikera PhoneFactor.pfdata-databasen. Webbtjänst-SDK är installerat på var och en av MFA-Server för att aktivera kommunikation via TCP-port 443 med AD FS-servrar. MFA-servrar distribueras i en tillståndslös konfiguration för Utjämning av nätverksbelastning. Men om du vill använda Engångslösenord via SMS måste du använda tillståndskänsliga belastningsutjämning.
   ![Azure MFA-Server - App-servern hög tillgänglighet](./media/mfa-server-high-availability/mfaapp.png)

   > [!NOTE]
   > Det rekommenderas inte att öppna brandväggar upp till intervallet för dynamiska portar som RPC kan användaren använda eftersom RPC använder dynamiska portar. Om du har en brandvägg **mellan** MFA programservrar, bör du konfigurera MFA-Server för att kommunicera med en statisk port för replikeringstrafiken mellan underordnade och huvudservrar och öppna porten i brandväggen. Du kan tvinga statisk port genom att skapa ett DWORD-registervärde på ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` kallas ```Pfsvc_ncan_ip_tcp_port``` och ange värdet till en tillgänglig statisk port. Anslutningar initieras alltid av underordnade servrar MFA i bakgrunden, statisk port krävs endast i bakgrunden, men eftersom du kan flytta en slav ska huvudservern när som helst, bör du ställa statisk port på alla servrar för MFA.

2. Två användare Portal/MFA Mobilapp-servrar (MFA-UP-MAS1 och MFA-UP-MAS2) belastningsutjämnas i en **stateful** configuration (mfa.contoso.com). Kom ihåg att Fäst sessioner är ett krav för belastningsutjämning MFA Användarportalen och Mobile App Service.
   ![Azure MFA-Server - Användarportalen och mobila Apptjänst hög tillgänglighet](./media/mfa-server-high-availability/mfaportal.png)
3. AD FS-servergruppen är belastningen på den belastningsutjämnade och publiceras till Internet via en belastningsutjämnad AD FS-proxyservrar i perimeternätverket. Varje AD FS-servern använder AD FS-agenten kan kommunicera med Azure MFA-servrar med en enda belastningsutjämnade URL (mfaapp.contoso.com) via TCP-port 443.

## <a name="next-steps"></a>Nästa steg

* [Installera och konfigurera Azure MFA-Server](multi-factor-authentication-get-started-server.md)
