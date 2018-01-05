---
title: "Hantera enkel inloggning för Azure AD Application Proxy | Microsoft Docs"
description: "Lär dig grunderna om enkel inloggning med Application Proxy"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 60422b4c8256df024bbc870fc43ec6b491f674f1
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Hur tillhandahåller Azure AD Application Proxy enkel inloggning?

Enkel inloggning är en nyckelfaktor för Azure AD Application Proxy.  Det ger den bästa användarupplevelsen eftersom användarna bara behöver logga in på Azure Active Directory i molnet. När de autentiserar till Azure Active Directory hanterar Application Proxy connector autentiseringen till lokala program. Backend-programmet kan inte se skillnaden mellan en fjärransluten användare logga in via Application Proxy och en vanlig användning på en domänansluten enhet. 

Om du vill använda Azure Active Directory för enkel inloggning för dina program, måste du markera **Azure Active Directory** som metod för autentisering. Om du väljer **Passthrough** användarna inte autentisera till Azure Active Directory alls, men skickas direkt till programmet. Du kan konfigurera den här inställningen när du först publicera ett program eller navigera till ditt program i Azure-portalen och redigera Application Proxy-inställningar. 

Följ dessa steg om du vill se dina alternativ för enkel inloggning:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till **Azure Active Directory** > **företagsprogram** > **alla program**.
3. Välj den app som enkel inloggning alternativ du vill hantera.
4. Välj **enkel inloggning**.

   ![Listrutan för enkel inloggning](./media/application-proxy-sso-overview/single-sign-on-mode.png)

Den nedrullningsbara menyn visas fem alternativ för enkel inloggning till ditt program:

* Azure AD enkel inloggning inaktiverad
* Lösenordsbaserade inloggning
* Länkade inloggning
* Integrerad Windows-autentisering
* Rubrik-baserade inloggning

## <a name="azure-ad-single-sign-on-disabled"></a>Azure AD enkel inloggning inaktiverad

Om du inte vill använda Azure Active Directory-integrering för enkel inloggning i ditt program, väljer du **Azure AD enkel inloggning inaktiverat**. Med det här alternativet kan dina användare kan autentiseras två gånger. Först de autentiserar till Azure Active Directory och sedan logga in på programmet. 

Det här alternativet är bra om lokala program kräver inte användare att autentisera, men du vill lägga till Azure Active Directory som ett säkerhetslager för fjärråtkomst. 

## <a name="password-based-sign-on"></a>Lösenordsbaserade inloggning

Om du vill använda Azure Active Directory som ett valv lösenord för lokala program väljer du **lösenordsbaserade inloggning**. Det här alternativet är bra om programmet autentiserar med användarnamn/lösenord kombinationsruta i stället för åtkomst-token eller huvuden. Med lösenordsbaserade inloggning måste användarna logga in programmet första tills de har åtkomst till den. Efter det tillhandahåller Azure Active Directory användarnamnet och lösenordet för användarens räkning. 

Information om hur du konfigurerar lösenordsbaserade inloggning finns i [lösenord vaulting för enkel inloggning med Application Proxy](application-proxy-sso-azure-portal.md).

## <a name="linked-sign-on"></a>Länkade inloggning

Om du redan har en enkel inloggning lösning har ställts in för dina lokala identiteter väljer **länkade inloggning**. Det här alternativet kan Azure Active Directory för att utnyttja befintliga SSO-lösningar, men ger ändå användarna fjärråtkomst till programmet. 

Information om länkade inloggning (tidigare kallat befintliga enkel inloggning) finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

Om lokala program som använder integrerad Windows-Authentication(IWA) eller om du vill använda Kerberos-begränsad delegering (KCD) för enkel inloggning, välja **integrerad Windows-autentisering**. Användarna behöver bara för att autentisera till Azure Active Directory med det här alternativet och sedan Application Proxy connector personifierar användaren för att få en Kerberos-token och logga in på programmet. 

Information om hur du konfigurerar integrerad Windows-autentisering finns i [Kerberos-begränsad delegering för enkel inloggning med Application Proxy](active-directory-application-proxy-sso-using-kcd.md).

## <a name="header-based-sign-on"></a>Rubrik-baserade inloggning 

Om dina program använder huvuden för autentisering, Välj **huvud-baserade inloggning**. Med det här alternativet behöver användarna bara autentisering Azure Active Directory. Microsoft-partner med en autentiseringstjänst från tredje part kallas PingAccess som översättas Azure Active Directory-åtkomsttoken till en huvudformat för programmet. 

Information om hur du konfigurerar huvud-baserad autentisering finns [huvud-baserad autentisering för enkel inloggning med Application Proxy](application-proxy-ping-access.md).

## <a name="next-steps"></a>Nästa steg

- [Lösenord vaulting för enkel inloggning med Application Proxy](application-proxy-sso-azure-portal.md)
- [Kerberos-begränsad delegering för enkel inloggning med Application Proxy](active-directory-application-proxy-sso-using-kcd.md)
- [Rubrik-baserad autentisering för enkel inloggning med Application Proxy](application-proxy-ping-access.md) 
