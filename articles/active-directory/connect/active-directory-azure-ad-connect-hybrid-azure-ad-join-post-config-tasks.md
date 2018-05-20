---
title: 'Azure AD Connect: Uppgifter Hybrid Azure AD join efter konfigurationen | Microsoft Docs'
description: Det här dokumentet beskriver post konfigurationsuppgifter som behövs för att slutföra Hybrid Azure AD-koppling
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: samueld
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: anandyadavmsft
ms.openlocfilehash: 02f1cbd1f2b8f7b0bec2f8016a300ede1d6f0439
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Publicera konfigurationsuppgifter för Hybrid Azure AD-koppling

När du har kört Azure AD Connect för att konfigurera din organisation för Hybrid Azure AD join, finns det några ytterligare steg som du måste slutföra för att slutföra den installationen.  Utför bara de steg som gäller för dina enheter.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurera kontrollerade distributionen (valfritt)
Alla domänanslutna enheter som kör Windows 10 och Windows Server 2016 automatiskt registrera med Azure AD när alla konfigurationssteg är klara. Om du föredrar en kontrollerad distributionen i stället för den här automatisk registrering av kan du använda grupprinciper för att selektivt aktivera eller inaktivera automatisk distribution.  Den här principen ska ställas in innan du startar den andra configuration steg: Azure AD
* Skapa ett grupprincipobjekt i Active Directory.
* Ge den namnet (ex-Hybrid Azure AD join).
* Redigera & Gå till: Datorkonfiguration > Principer > Administrationsmallar > Windows-komponenter > registrering av enheten.

>[!NOTE]
>För 2012R2 principinställningarna finns på **Datorkonfiguration > Principer > Administrationsmallar > Windows-komponenter > Anslut till arbetsplatsen > automatiskt workplace join klientdatorer**

* Den här inställningen inaktiveras: registrera domänanslutna datorer som enheter.
* Använda och klicka på OK.
* Länka Grupprincipobjektet till platsen (organisationsenhet, säkerhet eller för domän för alla enheter).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurera nätverket med slutpunkter för registrering av enhet
Kontrollera att följande URL: er är tillgängliga från datorer i din organisations nätverk för registrering på Azure AD:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementera WPAD för Windows 10-enheter
Om din organisation har åtkomst till Internet via en utgående proxy, implementera Web Proxy Auto-Discovery (WPAD) för att aktivera Windows 10-datorer att registrera på Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurera Tjänstanslutningspunkten i alla skogar som inte har konfigurerats med Azure AD Connect 

Tjänstanslutningspunkten (SCP) som innehåller din Azure AD-klient information som kommer att användas av dina enheter för automatisk registrering.  Köra PowerShell-skript ConfigureSCP.ps1 som du hämtade från Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Konfigurera en federationstjänst som inte har konfigurerats av Azure AD Connect

Om din organisation använder en federationstjänst för att logga in på Azure AD, måste anspråksregler i din Azure AD-förlitande part tillåta autentisering av enheten. Om du använder federation med AD FS kan du gå till [hjälp om AD FS](https://aka.ms/aadrptclaimrules) att generera anspråksreglerna. Kontakta leverantören för vägledning om du använder en icke-Microsoft-federation-lösning.  

>[!NOTE]
>Om du har äldre Windows-enheter kan tjänsten måste ha stöd för utfärdande av anspråk authenticationmethod och wiaormultiauthn när du tar emot förfrågningar till Azure AD-förtroende. Du bör lägga till transformeringen regeluppsättningarna som överför via autentiseringsmetod i AD FS.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Aktivera Azure AD sömlös SSO för Windows-klientversioner enheter

Om din organisation använder synkronisering av lösenords-Hash eller direkt autentisering för att logga in på Azure AD, aktivera Azure AD sömlös SSO till den metoden för autentisering av Windows-klientversioner enheter: https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Ange princip för Azure AD för äldre Windows-enheter

Om du vill registrera äldre Windows-enheter måste du kontrollera att Azure AD-princip tillåter användare att registrera enheter. 

* Logga in på ditt konto i Azure-portalen.
* Gå till: Azure Active Directory > enheter > Enhetsinställningar
* Ange ”användarna kan registrera sina enheter med Azure AD” alla.
* Klicka på Spara

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Lägg till Azure AD-slutpunkt till äldre Windows-enheter

Lägg till Azure AD-enheten autentiseringsslutpunkten lokalt intranät-zoner på dina Windows-klientversioner enheter att undvika anvisningarna för certifikat vid autentisering av enheterna: https://device.login.microsoftonline.com 

Om du använder [sömlös SSO](https://aka.ms/hybrid/sso), även aktivera ”Tillåt status uppdateras via skript” på den aktuella zonen och Lägg till följande slutpunkt: https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Installera Microsoft Arbetsplatsanslutning på äldre Windows-enheter

Det här installationsprogrammet skapar en schemalagd aktivitet på enheten system som körs i användarens kontext. Aktiviteten utlöses när användaren loggar in på Windows. Aktiviteten ansluter tyst enheten med Azure AD med autentiseringsuppgifterna för användaren när autentisering med integrerad Windows-autentisering. Download center finns på https://www.microsoft.com/en-us/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurera en grupprincip för att tillåta registrering av enheten

* Skapa ett grupprincipobjekt i Active Directory--om du inte redan har skapats.
* Ge den namnet (ex-Hybrid Azure AD join).
* Redigera & Gå till: Datorkonfiguration > Principer > Administrationsmallar > Windows-komponenter > Device Registration
* Aktivera: Registrera domänanslutna datorer som enheter
* Använda och klicka på OK.
* Länka Grupprincipobjektet till platsen (organisationsenhet, säkerhet eller för domän för alla enheter).

>[!NOTE]
>För 2012R2 principinställningarna finns på **Datorkonfiguration > Principer > Administrationsmallar > Windows-komponenter > Anslut till arbetsplatsen > automatiskt workplace join klientdatorer**

## <a name="next-steps"></a>Nästa steg
[Konfigurera tillbakaskrivning av enhet.](./active-directory-aadconnect-feature-device-writeback.md)
