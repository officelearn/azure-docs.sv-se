---
title: Anslut Active Directory med Azure Active Directory. | Microsoft Docs
description: Azure AD Connect integrerar dina lokala kataloger med Azure Active Directory. På så sätt kan du tillhandahålla en gemensam identitet för Office 365-, Azure- och SaaS-program som är integrerade med Azure AD.
keywords: introduktion till Azure AD Connect, översikt över Azure AD Connect, vad är Azure AD Connect, installera Active Directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fc18353ff89f8613847c977066e51620fb3e834f
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494672"
---
# <a name="what-is-hybrid-identity"></a>Vad är hybrididentitet? 

Företag och organisationer blir i dag allt mer en blandning av lokala och molnbaserade program.  Användare behöver åtkomst till dessa program både lokalt och i molnet. Det här kravet har blivit ett utmanande scenario. 

Microsofts identitetslösningar omfattar både lokala och molnbaserade funktioner.  Dessa lösningar skapar en gemensam användaridentitet för autentisering och auktorisering för alla resurser, oavsett plats. Vi kallar det för **hybrididentitet**.

För att uppnå hybrididentitet kan en av tre autentiseringsmetoder användas, beroende på dina scenarier.   De tre metoderna är: 

- **[Synkronisering av lösenordshash (PHS)](whatis-phs.md)**  
- **[Direktautentisering (PTA)](how-to-connect-pta.md)**  
- **[Federation](whatis-fed.md)** 

Dessa autentiseringsmetoder ger även funktioner för [enkel inloggning](how-to-connect-sso.md).  Enkel inloggning loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket.

Mer information finns i avsnittet om att [välja rätt autentiseringsmetod för din Azure Active Directory-hybrididentitetslösning](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). 

## <a name="common-scenarios-and-recommendations"></a>Vanliga scenarier och rekommendationer 

Här följer några vanliga scenarier med hybrididentitet och åtkomsthantering med rekommendationer om vilket (eller vilka) hybrididentitetsalternativ som passar för respektive scenario. 

|Jag behöver:|PHS och SSO<sup>1</sup>| PTA och SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Synkronisera nya användare, kontakter och gruppkonton som skapas i min lokala Active Directory till molnet automatiskt.|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Konfigurera min klientorganisation för Office 365-hybridscenarier|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Göra det möjligt för mina användare att logga in och komma åt molntjänster med sina lokala lösenord|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementera enkel inloggning med hjälp av företagsautentiseringsuppgifter|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|  
|Se till att inga lösenordshashvärden lagras i molnet| |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Stödja molnlösningar för multifaktorautentisering| |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Stödja lokala lösningar för multifaktorautentisering| | |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Stödja autentisering med smartkort för mina användare<sup>4</sup>| | |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Visa meddelanden om lösenord som upphör att gälla i Office-portalen och i skrivbordsversionen av Windows 10| | |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Lösenordshash-synkronisering med enkel inloggning. 
> 
> <sup>2</sup> Direktautentisering och enkel inloggning.  
> 
> <sup>3</sup> federerad enkel inloggning med AD FS.  
>  
> <sup>4</sup> AD FS kan integreras med din företags-PKI för att tillåta inloggning med certifikat. Dessa certifikat kan vara mjuka certifikat som distribueras via betrodda etableringskanaler, till exempel MDM, GPO eller smartkortscertifikat (inklusive PIV/CAC-kort) eller Hello för företag (cert-förtroende). Mer information om stöd för smartkortsautentisering finns på [den här bloggen](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="next-steps"></a>Nästa steg 

- [Vad är Azure AD Connect och Connect Health?](whatis-azure-ad-connect.md) 
- [Vad är synkronisering av lösenordshash (PHS)?](whatis-phs.md) 
- [Vad är direktautentisering (PTA)?](how-to-connect-pta.md) 
- [Vad är federation?](whatis-fed.md) 
- [Vad är enkel inloggning?](how-to-connect-sso.md) 

