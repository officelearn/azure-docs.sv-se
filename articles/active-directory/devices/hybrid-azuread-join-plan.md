---
title: Hur du konfigurerar hybrid Azure Active Directory-anslutna enheter | Microsoft Docs
description: Lär dig mer om att konfigurera hybrid Azure Active Directory-anslutna enheter.
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
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 4e6600d81fedd884cf415d055aedd29d163b3365
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629658"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Hur du planerar din hybrid Azure Active Directory join-implementering

På liknande sätt till en användare blir en enhet en annan identitet som du vill skydda och även använda för att skydda dina resurser på någon tid och plats. Du kan göra det här målet genom att föra identiteter på dina enheter till Azure AD med någon av följande metoder:

- Azure AD-anslutning
- Hybrid Azure AD-anslutning
- Azure AD-registrering

Genom att ta dina enheter till Azure AD, maximera användarnas produktivitet genom att använda enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. På samma gång, kan du skydda åtkomst till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md).

Om du har en lokal Active Directory-miljö och du vill ansluta till dina domänanslutna enheter till Azure AD, kan du göra detta genom att konfigurera hybrid Azure AD-anslutna enheter. Den här artikeln innehåller du med relaterade stegen för att implementera en hybrid Azure AD join i din miljö. 


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

Registreringen av Windows äldre enheter stöds inte för enheter som har konfigurerats för centrala för användarens profil eller autentiseringsuppgifter. Om du lita på centrala profiler eller inställningar, använder du Windows 10.

- Registreringen av Windows äldre enheter **är** stöd i icke-federerade miljöer via sömlös enkel inloggning [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 
 
- Registreringen av Windows äldre enheter **är inte** stöds när du använder Azure AD-direktautentisering utan sömlös enkel inloggning.

- Registreringen av Windows äldre enheter **är inte** stöds för enheter med hjälp av centrala profiler. Om du lita på centrala profiler eller inställningar, använder du Windows 10.


Registreringen av Windows Server som kör domänkontrollanten (DC)-rollen stöds inte.

Om din organisation kräver åtkomst till Internet via en autentiserad proxyserver för utgående, måste du se till att din Windows 10-datorer kan autentisera till utgående proxy. Eftersom Windows 10-datorer kör enhetsregistrering med hjälp av datorns kontext, är det nödvändigt att konfigurera utgående proxy-autentisering med hjälp av datorns kontext.


Hybrid Azure AD-anslutning är en process för att automatiskt registrera dina lokala domänanslutna enheter med Azure AD. Det finns fall där du inte vill att alla dina enheter att registrera automatiskt. Om det här gäller för dig, se [hur du styr hybrid Azure AD-anslutning av dina enheter](hybrid-azuread-join-control.md).



## <a name="select-your-scenario"></a>Välj ditt scenario

Du kan konfigurera hybrid Azure AD-anslutning för följande scenarier:

- Hanterade domäner
- Federerade domäner  



Om din miljö har hanterade domäner, stöder hybrid Azure AD-anslutning:

- Vidarebefordra via autentisering (PTA) med sömlös enkel inloggning (SSO) 

- Lösenordet har synkronisering (PHS) med sömlös enkel inloggning (SSO) 

Från och med version 1.1.819.0, ger Azure AD Connect dig en guide för att konfigurera hybrid Azure AD-anslutning. Guiden kan du avsevärt förenkla konfigurationsprocessen. Mer information finns i:

- [Konfigurera Azure Active Directory Join-hybrid för federerade domäner](hybrid-azuread-join-federated-domains.md)

- [Konfigurera Azure Active Directory Join-hybrid för hanterade domäner](hybrid-azuread-join-managed-domains.md)


 Om du installerar den nödvändiga versionen av Azure AD Connect inte är ett alternativ för dig, se [hur du manuellt konfigurera enhetsregistrering](../device-management-hybrid-azuread-joined-devices-setup.md). 






## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera hybrid Azure Active Directory-anslutning för federerade domäner](hybrid-azuread-join-federated-domains.md)
> [konfigurera hybrid Azure Active Directory-anslutning för hanterade domäner](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
