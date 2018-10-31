---
title: Konfigurera Hybrid Azure Active Directory-anslutningsenheter | Microsoft Docs
description: Lär dig att konfigurera Hybrid Azure Active Directory-anslutningsenheter.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 28344ac7c50b48b472ba6f907b116b3b202de454
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238805"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Hur du planerar din hybrid Azure Active Directory join-implementering

På liknande sätt som en användare blir en enhet till en identitet som du vill skydda och också använda för att skydda dina resurser, alltid och överallt. Det kan du göra genom att överföra enheternas identiteter till Azure Active Directory på något av följande sätt:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

När du börjar använda dina enheter med Azure Active Directory maximerar du användarnas produktivitet med enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. Samtidigt kan du skydda tillgången till dina resurser i molnet och lokalt med [villkorad åtkomst](../active-directory-conditional-access-azure-portal.md).

Om du har en lokal Active Directory-miljö och du vill ansluta dina domänanslutna enheter till Azure AD kan du göra det genom att konfigurera Hybrid Azure AD-anslutna enheter. Den här artikeln innehåller du med relaterade stegen för att implementera en hybrid Azure AD join i din miljö. 


## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med den [introduktionen till enhetshantering i Azure Active Directory](../device-management-introduction.md).


## <a name="plan-your-implementation"></a>Planera implementeringen

Om du vill planera hybridimplementeringen Azure AD, bör du bekanta dig med:

|   |   |
|---|---|
|![Markera][1]|Granska stöd för enheter|
|![Markera][1]|Saker du bör känna till granskning|
|![Markera][1]|Välj ditt scenario|


 


## <a name="review-supported-devices"></a>Granska stöd för enheter 

Hybrid Azure AD-anslutning har stöd för en bred Windows-enheter. Eftersom konfigurationen för enheter som kör äldre versioner av Windows kräver ytterligare eller andra steg, är enheter som stöds grupperade i två kategorier:

**Befintliga Windows-enheter**

- Windows 10
    
- Windows Server 2016


För enheter som kör Windows operativsystem, versionen som stöds är Windows 10 Anniversary Update (version 1607) eller senare. Ett bra tips är att uppgradera till den senaste versionen av Windows 10.



 **Windows äldre enheter**

- Windows 8.1
 
- Windows 7

- Windows Server 2012 R2
 
- Windows Server 2012 
 
- Windows Server 2008 R2 


Som ett första steg för planering, bör du granska din miljö och Bestäm om du behöver stöd för Windows äldre enheter.



## <a name="review-things-you-should-know"></a>Saker du bör känna till granskning

Du kan inte använda en hybrid Azure AD-anslutning om miljön består av en enda skog som synkroniseras identitetsdata till mer än en Azure AD-klient.

Om du lita på systemförberedelseverktyget (Sysprep), se till att skapa avbildningar från en installation av Windows som inte har konfigurerats för hybrid Azure AD-anslutning.

Om du lita på en virtuell dator (VM)-ögonblicksbild för att skapa ytterligare virtuella datorer, kontrollera att du använder en VM-ögonblicksbild som inte har konfigurerats för hybrid Azure AD-anslutning.

Hybrid Azure AD-koppling för Windows äldre enheter:

- **Är** stöd i icke-federerade miljöer via [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 

- **Är inte** stöds när du använder Azure AD-direktautentisering utan sömlös enkel inloggning.

- **Är inte** stöds när du använder centrala autentiseringsuppgifter eller användarprofil nätverksväxling eller när du använder virtuell datorinfrastruktur (VDI).


Registreringen av Windows Server som kör domänkontrollanten (DC)-rollen stöds inte.

Om din organisation kräver Internetåtkomst via en autentiserad proxyserver för utgående trafik måste du se till att dina Windows 10-datorer kan autentisera till den utgående proxyn. Eftersom Windows 10-datorer utför enhetsregistrering med maskinkontext måste autentiseringen för den utgående proxyn konfigureras med maskinkontext.


Hybrid Azure AD-anslutning är en process för att automatiskt registrera dina lokala domänanslutna enheter med Azure AD. Det finns fall där du inte vill att alla dina enheter att registrera automatiskt. Om det här gäller för dig, se [hur du styr hybrid Azure AD-anslutning av dina enheter](hybrid-azuread-join-control.md).



## <a name="select-your-scenario"></a>Välj ditt scenario

Du kan konfigurera hybrid Azure AD-anslutning för följande scenarier:

- Hanterade domäner
- Federerade domäner  



Om din miljö har hanterade domäner, stöder hybrid Azure AD-anslutning:

- Vidarebefordra via autentisering (PTA) med sömlös enkel inloggning (SSO) 

- Lösenordets Hash-synkronisering (PHS) med sömlös enkel inloggning (SSO) 

Från och med version 1.1.819.0 tillhandahåller Azure AD Connect en guide för konfiguration av Hybrid Azure AD-anslutning. Med guiden kan du förenkla konfigurationsprocessen avsevärt. Mer information finns i:

- [Konfigurera Azure Active Directory Join-hybrid för federerade domäner](hybrid-azuread-join-federated-domains.md)


- [Konfigurera Azure Active Directory Join-hybrid för hanterade domäner](hybrid-azuread-join-managed-domains.md)


 Om du installerar den nödvändiga versionen av Azure AD Connect inte är ett alternativ för dig, se [hur du manuellt konfigurera enhetsregistrering](../device-management-hybrid-azuread-joined-devices-setup.md). 






## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera hybrid Azure Active Directory-anslutning för federerade domäner](hybrid-azuread-join-federated-domains.md)
> [konfigurera hybrid Azure Active Directory-anslutning för hanterade domäner](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
