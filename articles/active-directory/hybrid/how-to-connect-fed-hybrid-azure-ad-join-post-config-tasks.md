---
title: 'Azure AD Connect: Hybrid Azure AD-kopplingspostkonfigurationsuppgifter | Microsoft-dokument'
description: Det här dokumentet innehåller information om efterkonfigurationsuppgifter som behövs för att slutföra Hybrid Azure AD-kopplingen
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffb8243041bb93ba8be6a65bb83df6f84affaee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049668"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Uppgifter efter konfiguration för Hybrid Azure AD-anslutning

När du har kört Azure AD Connect för att konfigurera organisationen för Hybrid Azure AD-koppling finns det några ytterligare steg som du måste slutföra för att slutföra installationen.  Utför bara de steg som gäller för dina enheter.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurera kontrollerad distribution (valfritt)
Alla domänanslutna enheter som kör Windows 10 och Windows Server 2016 registrerar sig automatiskt hos Azure AD när alla konfigurationssteg är klara. Om du föredrar en kontrollerad distribution i stället för den här automatiska registreringen kan du använda grupprincipen för att selektivt aktivera eller inaktivera automatisk distribution.  Den här grupprincipen bör anges innan du startar de andra konfigurationsstegen:
* Skapa ett grupprincipobjekt i Active Directory.
* Namnge den (ex- Hybrid Azure AD-koppling).
* Redigera och gå till: Principer för datorkonfiguration > > administrativa mallar > Windows-komponenter > enhetsregistrering.

>[!NOTE]
>För 2012R2 finns principinställningarna på **Datorkonfiguration > principer > administrativa mallar > Windows-komponenter > Arbetsplatsanslutning > Automatiskt arbetsplats ansluta till klientdatorer**

* Aktivera den här inställningen: Registrera domänanslutna datorer som enheter.
* Ansök och klicka på OK.
* Länka grupprincipobjektet till valfri plats (organisationsenhet, säkerhetsgrupp eller domän för alla enheter).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurera nätverk med slutpunkter för enhetsregistrering
Kontrollera att följande url:er är tillgängliga från datorer i organisationen för registrering till Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementera WPAD för Windows 10-enheter
Om din organisation ansluter till Internet via en utgående proxy implementerar du WPAD (Web Proxy Auto-Discovery) för att aktivera Windows 10-datorer för att registrera sig i Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurera SCP i alla skogar som inte har konfigurerats av Azure AD Connect 

Tjänstanslutningspunkten (SCP) innehåller din Azure AD-klientinformation som ska användas av dina enheter för automatisk registrering.  Kör PowerShell-skriptet ConfigureSCP.ps1 som du hämtade från Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Konfigurera alla federationstjänster som inte har konfigurerats av Azure AD Connect

Om din organisation använder en federationstjänst för att logga in på Azure AD måste anspråksreglerna i ditt Azure AD-förlitande partiförtroende tillåta enhetsautentisering. Om du använder federation med AD FS går du till [AD FS-hjälpen](https://aka.ms/aadrptclaimrules) för att generera anspråksreglerna. Om du använder en federationslösning som inte kommer från Microsoft kontaktar du den leverantören för att få vägledning.  

>[!NOTE]
>Om du har Windows-enheter på lägre nivå måste tjänsten ha stöd för att utfärda autentiseringsmetoden och wiaormultiauthn-anspråken när du tar emot begäranden till Azure AD-förtroendet. I AD FS bör du lägga till en utgivningstransformationsregel som går igenom autentiseringsmetoden.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Aktivera Azure AD Seamless SSO för Windows-enheter på lägre nivå

Om din organisation använder lösenordshÃ¶ringssynkronisering eller direktautentisering för att logga in på Azure AD aktiverar du https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ssoAzure AD Seamless SSO med den inloggningsmetoden för att autentisera Windows-enheter på lägre nivå: . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Ange Azure AD-princip för Windows-enheter på lägre nivå

Om du vill registrera Windows enheter på lägre nivå måste du se till att Azure AD-principen tillåter användare att registrera enheter. 

* Logga in på ditt konto i Azure-portalen.
* Gå till: Azure Active Directory >-enheter > enhetsinställningar
* Ange "Användare kan registrera sina enheter med Azure AD" till ALLA.
* Klicka på Spara

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Lägg till Azure AD-slutpunkt i Windows enheter på lägre nivå

Lägg till slutpunkten för Azure AD-enhetsautentisering i de lokala intranätzonerna på windows-enheter på lägre nivå för att undvika certifikatuppmaningar när enheterna autentiseras:`https://device.login.microsoftonline.com` 

Om du använder [Seamless SSO](how-to-connect-sso.md)aktiverar du även "Tillåt uppdateringar av statusfältet via skript" i den zonen och lägger till följande slutpunkt:`https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Installera Microsoft Workplace Join på Windows enheter på lägre nivå

Den här installationsprogrammet skapar en schemalagd aktivitet på enhetssystemet som körs i användarens kontext. Uppgiften utlöses när användaren loggar in i Windows. Uppgiften ansluter tyst enheten med Azure AD med användarautentiseringsuppgifterna efter autentisering med integrerad Windows-autentisering. Download center är https://www.microsoft.com/download/details.aspx?id=53554på . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurera grupprincipen så att enhetsregistreringen tillåts

* Skapa ett grupprincipobjekt i Active Directory – om det inte redan har skapats.
* Namnge den (ex- Hybrid Azure AD-koppling).
* Redigera & gå till: Principer för datorkonfiguration > > administrativa mallar > Windows-komponenter > enhetsregistrering
* Aktivera: Registrera domänanslutna datorer som enheter
* Ansök och klicka på OK.
* Länka grupprincipobjektet till valfri plats (organisationsenhet, säkerhetsgrupp eller domän för alla enheter).

>[!NOTE]
>För 2012R2 finns principinställningarna på **Datorkonfiguration > principer > administrativa mallar > Windows-komponenter > Arbetsplatsanslutning > Automatiskt arbetsplats ansluta till klientdatorer**

## <a name="next-steps"></a>Nästa steg
[Konfigurera tillbakaskrivning av enheten](how-to-connect-device-writeback.md)
