---
title: Hög tillgänglighet för Azure MFA Server - Azure Active Directory
description: Distribuera flera instanser av Azure Multi-Factor Authentication Server i konfigurationer som ger hög tillgänglighet.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e7b5f6bef5358acf0709f994b85215e505fa4db
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653369"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurera Azure Multi Factor Authentication Server för hög tillgänglighet

För att uppnå hög tillgänglighet med din Azure Server MFA-distribution måste du distribuera flera MFA-servrar. Det här avsnittet innehåller information om en belastningsbalanserad design för att uppnå dina högtillgänglighetsmål i din Azure MFS Server-distribution.

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare bör använda molnbaserad Azure Multi-Factor-autentisering. Befintliga kunder som har aktiverat MFA Server före den 1 juli kommer att kunna ladda ner den senaste versionen, framtida uppdateringar och generera aktiveringsautentiseringsuppgifter som vanligt.

## <a name="mfa-server-overview"></a>Översikt över MFA-server

Azure MFA Server-tjänstarkitekturen består av flera komponenter som visas i följande diagram:

 ![MFA-serverarkitekturkomponenter](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

En MFA-server är en Windows-server som har Azure Multi-Factor Authentication programvara installerad. MFA-serverinstansen måste aktiveras av MFA-tjänsten i Azure för att fungera. Mer än en MFA-server kan installeras lokalt.

Den första MFA-servern som installeras är huvud-MFA-servern vid aktivering av Azure MFA-tjänsten som standard. Huvud-MFA-servern har en skrivbar kopia av PhoneFactor.pfdata-databasen. Efterföljande installationer av instanser av MFA-server kallas underordnade. MFA-underordnade har en replikerad skrivskyddad kopia av PhoneFactor.pfdata-databasen. MFA-servrar replikerar information med hjälp av Remote Procedure Call (RPC). Alla MFA Severs måste tillsammans antingen vara domänanslutna eller fristående för att replikera information.

Både MFA-hanterare och underordnade MFA-servrar kommunicerar med MFA-tjänsten när tvåfaktorsautentisering krävs. När en användare till exempel försöker få åtkomst till ett program som kräver tvåfaktorsautentisering autentiseras användaren först av en identitetsprovider, till exempel Active Directory (AD).

Efter lyckad autentisering med AD kommer MFA-servern att kommunicera med MFA-tjänsten. MFA-servern väntar på meddelande från MFA-tjänsten för att tillåta eller neka användaren åtkomst till programmet.

Om MFA-huvudservern kopplas från kan autentiseringar fortfarande bearbetas, men åtgärder som kräver ändringar i MFA-databasen kan inte bearbetas. (Exempel är: tillägg av användare, pin-kodändringar med självbetjäning, ändra användarinformation eller åtkomst till användarportalen)

## <a name="deployment"></a>Distribution

Tänk på följande viktiga punkter för belastningsutjämning av Azure MFA Server och dess relaterade komponenter.

* **Använda RADIUS-standarden för att uppnå hög tillgänglighet**. Om du använder Azure MFA-servrar som RADIUS-servrar kan du eventuellt konfigurera en MFA-server som ett primärt RADIUS-autentiseringsmål och andra Azure MFA-servrar som sekundära autentiseringsmål. Den här metoden för att uppnå hög tillgänglighet kanske inte är praktisk eftersom du måste vänta på att en time out-period ska inträffa när autentisering misslyckas på det primära autentiseringsmålet innan du kan autentiseras mot det sekundära autentiseringsmålet. Det är mer effektivt att läsa in radius-trafiken mellan RADIUS-klienten och RADIUS-servrarna (i det här fallet Azure MFA-servrar som fungerar som RADIUS-servrar) så att du kan konfigurera RADIUS-klienterna med en enda URL som de kan peka på.
* **Behöver manuellt befordra MFA underordnade**. Om huvud-Azure MFA-servern kopplas från fortsätter de sekundära Azure MFA-servrarna att bearbeta MFA-begäranden. Men tills en master MFA-server är tillgänglig, administratörer kan inte lägga till användare eller ändra MFA inställningar, och användare kan inte göra ändringar med hjälp av användarportalen. Att främja en MFA underordnad huvudrollen är alltid en manuell process.
* **Olika komponenter**. Azure MFA Server består av flera komponenter som kan installeras på samma Windows Server-instans eller på olika instanser. Dessa komponenter inkluderar Användarportalen, mobilappwebbtjänsten och ADFS-kortet (agenten). Denna separability gör det möjligt att använda webbprogramproxyn för att publicera användarportalen och mobilappens webbserver från perimeternätverket. En sådan konfiguration ökar den övergripande säkerheten för din design, som visas i följande diagram. MFA User Portal och Mobile App Web Server kan också distribueras i HA belastningsbalanserade konfigurationer.

   ![MFA-server med perimeternätverk](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Engångslösenord (OTP) via SMS (aka enkelriktad SMS) kräver användning av klibbiga sessioner om trafiken är belastningsbalanserad**. Enkelriktad SMS är ett autentiseringsalternativ som gör att MFA-servern skickar ett sms till användarna som innehåller en otp.a. Användaren anger otp i ett promptfönster för att slutföra MFA-utmaningen. Om du läser in Azure MFA-servrar måste samma server som hanterade den första autentiseringsbegäran vara den server som tar emot otp-meddelandet från användaren. Om en annan MFA-server tar emot otp-svaret misslyckas autentiseringsutmaningen. Mer information finns i [One Time Password över SMS som lagts till i Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Belastningsbalanserade distributioner av webbtjänsten Användarportal och Mobilapp kräver klibbiga sessioner**. Om du laddar MFA-användarportalen och mobilappens webbtjänst måste varje session finnas kvar på samma server.

## <a name="high-availability-deployment"></a>Distribution med hög tillgänglighet

Följande diagram visar en komplett HA-belastningsbalanserad implementering av Azure MFA och dess komponenter, tillsammans med ADFS som referens.

 ![Azure MFA Server HA-implementering](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Observera följande objekt för motsvarande numrerade område i föregående diagram.

1. De två Azure MFA-servrarna (MFA1 och MFA2) är belastningsbalanserade (mfaapp.contoso.com) och är konfigurerade för att använda en statisk port (4443) för att replikera PhoneFactor.pfdata-databasen. Web Service SDK installeras på var och en av MFA-servern för att möjliggöra kommunikation via TCP-port 443 med ADFS-servrarna. MFA-servrarna distribueras i en tillståndslös belastningsbalanserad konfiguration. Men om du vill använda OTP via SMS måste du använda tillståndskänslig belastningsutjämning.
   ![Azure MFA Server – Appserver HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Eftersom RPC använder dynamiska portar rekommenderas det inte att öppna brandväggar upp till det intervall av dynamiska portar som RPC potentiellt kan använda. Om du har en brandvägg **mellan** MFA-programservrarna bör du konfigurera MFA-servern så att den kommunicerar på en statisk port för replikeringstrafiken mellan underordnade servrar och huvudservrar och öppnar porten i brandväggen. Du kan tvinga den statiska porten genom ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` att ```Pfsvc_ncan_ip_tcp_port``` skapa ett DWORD-registervärde till anropat och ange värdet till en tillgänglig statisk port. Anslutningar initieras alltid av de underordnade MFA-servrarna till huvudhanteraren, den statiska porten krävs bara på huvudhanteraren, men eftersom du kan befordra en underordnad till att vara huvudbonad när som helst bör du ställa in den statiska porten på alla MFA-servrar.

2. De två användarportalerna/MFA Mobile App-servrarna (MFA-UP-MAS1 och MFA-UP-MAS2) är belastningsbalanserade i en **tillståndskänslig** konfiguration (mfa.contoso.com). Kom ihåg att klibbiga sessioner är ett krav för belastningsutjämning av MFA User Portal och Mobile App Service.
   ![Azure MFA Server – Användarportal och mobilapptjänst HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. ADFS Server-servergruppen är belastningsbalanserad och publiceras på Internet via belastningsbalanserade ADFS-proxyservrar i perimeternätverket. Varje ADFS-server använder ADFS-agenten för att kommunicera med Azure MFA-servrar med en enda belastningsbalanserad URL (mfaapp.contoso.com) över TCP-port 443.

## <a name="next-steps"></a>Nästa steg

* [Installera och konfigurera Azure MFA Server](howto-mfaserver-deploy.md)
