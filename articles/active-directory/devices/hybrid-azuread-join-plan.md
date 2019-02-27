---
title: Hur du planerar hybridimplementeringen Azure Active Directory join i Azure Active Directory (Azure AD) | Microsoft Docs
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
ms.date: 02/03/2019
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7a1d0dab08c4beb93d323bcd1a5f5b00a190daa
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868297"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Instruktioner: Planera implementeringen hybrid Azure Active Directory join

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
|![Markera][1]|Läs om hur du styr hybrid Azure AD-anslutning av dina enheter|
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

Om du lita på systemförberedelseverktyget (Sysprep), se till att avbildningar som skapats från en installation av Windows 10-1803 eller tidigare inte har konfigurerats för hybrid Azure AD-anslutning.

Om du lita på en virtuell dator (VM)-ögonblicksbild för att skapa ytterligare virtuella datorer, kontrollera att du använder en VM-ögonblicksbild som inte har konfigurerats för hybrid Azure AD-anslutning.

Hybrid Azure AD-koppling för Windows äldre enheter:

- **Är** stöd i icke-federerade miljöer via [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 

- **Är inte** stöds när du använder Azure AD-direktautentisering utan sömlös enkel inloggning.

- **Är inte** stöds när du använder centrala autentiseringsuppgifter eller användarprofil nätverksväxling eller när du använder virtuell datorinfrastruktur (VDI).


Registreringen av Windows Server som kör domänkontrollanten (DC)-rollen stöds inte.

Om din organisation kräver Internetåtkomst via en autentiserad proxyserver för utgående trafik måste du se till att dina Windows 10-datorer kan autentisera till den utgående proxyn. Eftersom Windows 10-datorer utför enhetsregistrering med maskinkontext måste autentiseringen för den utgående proxyn konfigureras med maskinkontext.


Hybrid Azure AD-anslutning är en process för att automatiskt registrera dina lokala domänanslutna enheter med Azure AD. Det finns fall där du inte vill att alla dina enheter att registrera automatiskt. Om det här gäller för dig, se [hur du styr hybrid Azure AD-anslutning av dina enheter](hybrid-azuread-join-control.md).

Om din Windows 10-domänanslutna enheter är redan [Azure AD-registrerad](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) till din klient, vi rekommenderar starkt att ta bort det aktuella tillståndet innan du aktiverar Hybrid Azure AD-anslutning. Följande ändringar har gjorts att undvika det här dubbel tillståndet från Windows 10 1809 version: 
 - Alla befintliga Azure AD-registrerad tillstånd skulle tas bort automatiskt när enheten är Hybrid Azure AD-anslutna. 
 - Du kan förhindra att dina domänansluten enhet som Azure AD-registrerad genom att lägga till den här registernyckeln - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, ”BlockAADWorkplaceJoin” = DWORD: 00000001

FIPS-kompatibel TPM: er stöds inte för Hybrid Azure AD-anslutning. Om dina enheter har FIPS-kompatibel TPM: er, måste du inaktivera dem innan du fortsätter med Hybrid Azure AD-anslutning. Microsoft tillhandahåller inte några verktyg för att inaktivera FIPS-läge för TPM: er eftersom den är beroende av TPM-tillverkaren. Kontakta din maskinvara OEM för support.

## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Läs om hur du styr hybrid Azure AD-anslutning av dina enheter

Hybrid Azure AD-anslutning är en process för att automatiskt registrera dina lokala domänanslutna enheter med Azure AD. Det finns fall där du inte vill att alla dina enheter att registrera automatiskt. Det här är för exempel SANT under den inledande distributionen för att kontrollera att allt fungerar som förväntat.

Mer information finns i [hur du styr hybrid Azure AD-anslutning av dina enheter](hybrid-azuread-join-control.md)

## <a name="select-your-scenario"></a>Välj ditt scenario

Du kan konfigurera hybrid Azure AD-anslutning för följande scenarier:

- Hanterade domäner
- Federerade domäner  



Om din miljö har hanterade domäner, stöder hybrid Azure AD-anslutning:

- Autentisering (PTA)

- Lösenordets Hash-synkronisering (PHS)

Från och med version 1.1.819.0 tillhandahåller Azure AD Connect en guide för att konfigurera Hybrid Azure AD-koppling. Med guiden kan du förenkla konfigurationsprocessen avsevärt. Mer information finns i:

- [Konfigurera Azure Active Directory Join-hybrid för federerade domäner](hybrid-azuread-join-federated-domains.md)


- [Konfigurera Azure Active Directory Join-hybrid för hanterade domäner](hybrid-azuread-join-managed-domains.md)


 Om du installerar den nödvändiga versionen av Azure AD Connect inte är ett alternativ för dig, se [hur du manuellt konfigurera enhetsregistrering](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 


## <a name="on-premises-ad-upn-support-in-hybrid-azure-ad-join"></a>Stöd för lokala AD UPN i Hybrid Azure AD-anslutning

Ibland kan din lokala AD UPN: er kan skilja sig från din Azure AD UPN-namn. I sådana fall kan Windows 10-Hybrid Azure AD-anslutning har begränsat stöd för lokala AD UPN-namn baserat på den [autentiseringsmetod](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), typ och version av Windows 10. Det finns två typer av lokala AD UPN: er som kan finnas i din miljö:

 - Dirigerbara UPN: Ett dirigerbart UPN har en giltig verifierad domän som har registrerats hos en domänregistrator. Till exempel om contoso.com är den primära domänen i Azure AD, contoso.org är den primära domänen i den lokala AD som ägs av Contoso och [verifierats i Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
 
 - Icke-dirigerbara UPN: Ett icke-dirigerbara UPN har inte en verifierad domän. Det gäller endast inom din organisations privata nätverk. Till exempel om contoso.com är den primära domänen i Azure AD, contoso.local är den primära domänen i lokala AD men är inte en verifierbar domän på internet och används i Contoso endast användarens nätverk.
 
Tabellen nedan innehåller information om stöd för dessa lokala AD UPN: er i Windows 10-Hybrid Azure AD-anslutning

|Typ av en lokal AD UPN|Domäntyp|Windows 10 version|Beskrivning|
|-----|-----|-----|-----|
|Dirigerbara|Federerad |Från version 1703|Allmänt tillgänglig|
|Dirigerbara|Hanterad|Från version 1709|För tillfället i privat förhandsvisning. Azure AD SSPR stöds inte |
|Icke-dirigerbara|Federerad|Från version 1803|Allmänt tillgänglig|
|Icke-dirigerbara|Hanterad|Stöds inte||



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera hybrid Azure Active Directory-anslutning för federerade domäner](hybrid-azuread-join-federated-domains.md)
> [konfigurera hybrid Azure Active Directory-anslutning för hanterade domäner](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
