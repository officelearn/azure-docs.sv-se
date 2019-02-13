---
title: Så här fungerar enkel inloggning till lokala resurser på Azure AD-anslutna enheter | Microsoft Docs
description: Lär dig att konfigurera anslutna Azure Active Directory-hybridenheter.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c42a2240bd5b242634cbb255bf717f11610f063
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56161599"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Så här fungerar enkel inloggning till lokala resurser på Azure AD-anslutna enheter

Det är förmodligen inte en överraskning att en domänansluten enhet i Azure Active Directory (AD Azure) får du en enkel inloggning (SSO)-upplevelse till molnappar i din klient. Om miljön har en lokal Active Directory (AD), kan du utöka SSO-upplevelse på dessa enheter till den.

Den här artikeln förklarar hur det fungerar.

## <a name="how-it-works"></a>Hur det fungerar 

Eftersom du måste komma ihåg bara ett enda användarnamn och lösenord, enkel inloggning underlättar åtkomst till resurser och förbättrar säkerheten för din miljö. Med en Azure AD-ansluten enhet har användarna redan enkel inloggning till molnappar i din miljö. Om din miljö har en Azure AD och har en lokal AD, vill du förmodligen att expandera omfattningen för SSO-upplevelse till din lokala rad Affärsappar (LOB) appar, delade filer och skrivare.  


Azure AD-anslutna enheter har ingen kunskap om dina lokala AD-miljön eftersom de inte är anslutna till den. Men du kan ange ytterligare information om din lokala AD till dessa enheter med Azure AD Connect.
En miljö som har både, en Azure AD och har en lokal AD, är också kända har hybridmiljö. Om du har en hybridmiljö, är det troligt att du redan har Azure AD Connect för att synkronisera din lokala identitetsinformation till molnet. Som en del av synkroniseringsprocessen synkroniserar Azure AD Connect lokala domäninformation till Azure AD. När en användare loggar in med Azure AD ansluten enhet i en hybridmiljö:

1. Azure AD skickar namnet på den lokala domänen användaren är medlem i tillbaka till enheten. 

2. Tjänsten lokala security authority (LSA) gör det möjligt för Kerberos-autentisering på enheten.

Vid ett åtkomstförsök till en resurs i användarens lokala domän, enheten:

1. Använder domäninformation för att hitta en domänkontrollant (DC). 

2. Skickar lokalt autentiseringsuppgifter för domänen och att hitta domänkontrollanten att hämta den användare som autentiseras.

3. Tar emot en Kerberos [biljettbeviljande biljett (TGT)](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets) som används för att få åtkomst till AD-anslutna resurser.

Alla appar som är konfigurerade för **Windows-integrerad autentisering** sömlöst få SSO när en användare försöker komma åt dem.  

Windows Hello för företag kräver ytterligare konfiguration för att aktivera enkel inloggning på plats från en domänansluten Azure AD-enhet. Mer information finns i [konfigurera Azure AD-anslutna enheter för lokal enkel inloggning om hur du använder Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Vad du får

Ansluten enhet som du kan med enkel inloggning, på en Azure AD: 

- Få åtkomst till en UNC-sökväg på en medlemsserver i AD

- Få åtkomst till en AD medlemmen server som är konfigurerad för Windows-integrerad säkerhet 



Om du vill hantera din lokala AD från en Windows-enhet, installera den [Remote Server Administration Tools för Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=45520).

Du kan använda:

- Active Directory-användare och datorer (ADUC) snapin-modulen att administrera alla AD-objekt. Du måste dock ange den domän som du vill ansluta till manuellt.

- DHCP-snapin-modulen att administrera en AD-anslutna DHCP-server. Du kan dock behöva ange DHCP-servernamn eller adress.

 
## <a name="what-you-should-know"></a>Det här bör du känna till

Du kan behöva justera dina [domänbaserade filtreringen](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) i Azure AD Connect för att säkerställa att data om de nödvändiga domänerna är synkroniserade.

Appar och resurser som är beroende av Active Directory som datorn autentisering inte fungerar eftersom Azure AD-anslutna enheter har inte ett datorobjekt i AD. 

Du kan inte dela filer med andra användare i en Azure AD-ansluten enhet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [vad är enhetshantering i Azure Active Directory?](overview.md) 
