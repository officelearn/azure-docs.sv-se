---
title: 'Azure AD Connect: Uppgifter Hybrid Azure AD join efter konfigurationen | Microsoft Docs'
description: Den här dokumentet beskriver post konfigurationsuppgifter som behövs för att slutföra Hybrid Azure AD-anslutning
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 05cb6d10a7e4269cbe5f9c97ef70cd9eb5a4d68e
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056777"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Publicera konfigurationsuppgifter för Hybrid Azure AD-anslutning

När du har kört Azure AD Connect för att konfigurera din organisation för Hybrid Azure AD-anslutning, finns det några ytterligare steg som du måste utföra för att slutföra den installationen.  Utför bara de steg som gäller för dina enheter.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurera kontrollerad distribution (valfritt)
Alla domänanslutna enheter som kör Windows 10 och Windows Server 2016 automatiskt registrera med Azure AD när alla konfigurationssteg är klara. Om du föredrar en kontrollerad distribution i stället för den här automatisk registrering, kan du använda grupprinciper för att selektivt aktivera eller inaktivera automatisk distribution.  Den här grupprincipen ska anges innan du startar den andra configuration steg: Azure AD
* Skapa ett grupprincipobjekt i Active Directory.
* Ge den namnet (ex-Hybrid Azure AD-anslutning).
* Redigera och gå till: Datorkonfiguration > Principer > Administrationsmallar > Windows-komponenter > Device Registration.

>[!NOTE]
>För 2012R2 principinställningarna finns på **Datorkonfiguration > Principer > Administrationsmallar > Windows-komponenter > Anslut till arbetsplatsen > automatiskt workplace join-klientdatorer**

* Den här inställningen inaktiveras: registrera domänanslutna datorer som enheter.
* Tillämpa och klicka på OK.
* Länka Grupprincipobjektet till platsen för ditt val (organisationsenhet, säkerhet eller för domän för alla enheter).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurera nätverk med slutpunkter för registrering av enhet
Kontrollera att följande URL: er är tillgängliga från datorer i din organisations nätverk för registrering till Azure AD:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementera WPAD för Windows 10-enheter
Om organisationen har åtkomst till Internet via en utgående proxy, implementera Web Proxy Auto-Discovery (WPAD) för att aktivera Windows 10-datorer att registrera till Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurera SCP: N i alla skogar som inte har konfigurerats med Azure AD Connect 

Tjänstanslutningspunkten (SCP) innehåller din Azure AD-klientinformation som ska användas av dina enheter för automatisk registrering.  Kör PowerShell-skriptet ConfigureSCP.ps1 som du laddade ned från Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Konfigurera alla federationstjänsten som inte har konfigurerats med Azure AD Connect

Om din organisation använder en federationstjänst för att logga in på Azure AD, måste anspråksregler i din Azure AD förtroende för förlitande part tillåta enhetsautentisering. Om du använder federation med AD FS, gå till [hjälp om AD FS](https://aka.ms/aadrptclaimrules) att skapa anspråksreglerna. Om du använder en federationslösning som inte kommer från Microsoft, kontaktar du den leverantören för vägledning.  

>[!NOTE]
>Om du har Windows äldre enheter, tjänsten måste ha stöd för utfärdande av anspråk authenticationmethod och wiaormultiauthn när du tar emot förfrågningar till Azure AD-förtroende. Du bör lägga till en regel för utfärdande transformeringen som pass via autentiseringsmetod i AD FS.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Aktivera Azure AD sömlös enkel inloggning för Windows äldre enheter

Om din organisation använder Hashsynkronisering för lösenord eller direktautentisering för att logga in på Azure AD, Aktivera sömlös enkel inloggning för den Azure AD med den metoden för autentisering av Windows äldre enheter: https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Skapa Azure AD-princip för Windows äldre enheter

Om du vill registrera Windows äldre enheter, måste du kontrollera att Azure AD-principen tillåter användare att registrera enheter. 

* Logga in på ditt konto i Azure-portalen.
* Gå till: Azure Active Directory > enheter > Enhetsinställningar
* Ange ”användare kan registrera sina enheter med Azure AD” alla.
* Klicka på Spara

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Lägga till Azure AD-slutpunkten för Windows äldre enheter

Lägg till Azure AD authentication enhetsslutpunkt lokala intranätszoner på dina Windows äldre enheter för att undvika certifikat vid autentisering enheterna: https://device.login.microsoftonline.com 

Om du använder [sömlös SSO](https://aka.ms/hybrid/sso), samt aktivera ”Tillåt status uppdateras via skript” på zonen och lägga till följande slutpunkt: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Installera Microsoft Workplace Join på Windows äldre enheter

Det här installationsprogrammet skapar en schemalagd aktivitet på enhetssystemet som körs i användarens kontext. Aktiviteten utlöses när användaren loggar in på Windows. Uppgiften kopplar tyst enhet med Azure AD med autentiseringsuppgifterna för användaren när de har autentiserat med integrerad Windows-autentisering. Download center finns på https://www.microsoft.com/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurera en grupprincip för att tillåta registrering av enheten

* Skapa ett grupprincipobjekt i Active Directory--om du inte redan har skapats.
* Ge den namnet (ex-Hybrid Azure AD-anslutning).
* Redigera och gå till: Datorkonfiguration > Principer > Administrationsmallar > Windows-komponenter > Device Registration
* Aktivera: Registrera domänanslutna datorer som enheter
* Tillämpa och klicka på OK.
* Länka Grupprincipobjektet till platsen för ditt val (organisationsenhet, säkerhet eller för domän för alla enheter).

>[!NOTE]
>För 2012R2 principinställningarna finns på **Datorkonfiguration > Principer > Administrationsmallar > Windows-komponenter > Anslut till arbetsplatsen > automatiskt workplace join-klientdatorer**

## <a name="next-steps"></a>Nästa steg
[Konfigurera tillbakaskrivning av enhet](./active-directory-aadconnect-feature-device-writeback.md)
