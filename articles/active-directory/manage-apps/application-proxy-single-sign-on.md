---
title: Hantera enkel inloggning för Azure AD Application Proxy | Microsoft Docs
description: Lär dig grunderna för enkel inloggning med programproxy
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: dbdbe8b83af20f66ad2cc99d2a5665262479b4a9
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364156"
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Hur tillhandahåller Azure AD Application Proxy enkel inloggning?

Enkel inloggning är en viktig komponent i Azure AD-programproxy.  Det ger den bästa användarupplevelsen eftersom användarna bara behöver logga in på Azure Active Directory i molnet. När de autentiserar till Azure Active Directory hanterar Application Proxy connector autentiseringen till dina lokala program. Backend-applikationer kan inte se skillnaden mellan en fjärranvändare logga in via Application Proxy och en vanlig användning på en domänansluten enhet. 

Om du vill använda Azure Active Directory för enkel inloggning för dina program, du måste välja **Azure Active Directory** som förautentiseringsmetoden. Om du väljer **genomströmning** användarna inte autentisera till Azure Active Directory alls, men dirigeras direkt till programmet. Du kan konfigurera den här inställningen när du först publicera ett program eller navigera till ditt program i Azure-portalen och redigera Application Proxy-inställningar. 

Följ dessa steg om du vill se dina alternativ för enkel inloggning:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till **Azure Active Directory** > **företagsprogram** > **alla program**.
3. Välj den app som enkel inloggning alternativ du vill hantera.
4. Välj **enkel inloggning**.

   ![Listrutemeny för enkel inloggning](./media/application-proxy-single-sign-on/single-sign-on-mode.png)

Den nedrullningsbara menyn visar fem alternativ för enkel inloggning i ditt program:

* Azure AD enkel inloggning inaktiverad
* Lösenordsbaserad inloggning
* Länkad inloggning
* Integrerad Windows-autentisering
* Rubrikbaserad inloggning

## <a name="azure-ad-single-sign-on-disabled"></a>Azure AD enkel inloggning inaktiverad

Om du inte vill använda Azure Active Directory-integrering för enkel inloggning i ditt program, väljer **Azure AD enkel inloggning inaktiverad**. Med det här alternativet kan dina användare kan autentiseras två gånger. Först de autentisera till Azure Active Directory och logga sedan in på själva programmet. 

Det här alternativet är ett bra alternativ om ditt lokala program kräver inte användare att autentisera, men du vill lägga till Azure Active Directory som ett lager av säkerhet för fjärråtkomst. 

## <a name="password-based-sign-on"></a>Lösenordsbaserad inloggning

Om du vill använda Azure Active Directory som ett lösenord valv för dina lokala program, Välj **lösenordsbaserad inloggning**. Det här alternativet är ett bra alternativ om ditt program som autentiseras med en kombinationsruta för användarnamn/lösenord i stället för åtkomsttoken eller rubriker. Med lösenordsbaserad inloggning behöver användarna för att logga in till den tidpunkt när programmet först de komma åt den. Efter det tillhandahåller Azure Active Directory användarnamnet och lösenordet för användarens räkning. 

Information om hur du konfigurerar lösenordsbaserad inloggning finns i [lösenord vaulting för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-password-vaulting.md).

## <a name="linked-sign-on"></a>Länkad inloggning

Om du redan har en enda inloggning lösning för dina lokala identiteter kan du välja **länkad inloggning**. Det här alternativet gör att Azure Active Directory för att utnyttja befintliga lösningar för enkel inloggning, men ger ändå användarna fjärråtkomst till programmet. 

Läs om hur länkad inloggning (kallades tidigare känt som befintlig enkel inloggning) [vad är programåtkomst och enkel inloggning med Azure Active Directory?](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

Om dina lokala program som använder integrerad Windows-Authentication(IWA) eller om du vill använda Kerberos-begränsad delegering (KCD) för enkel inloggning, välja **integrerad Windows-autentisering**. Med det här alternativet kan användarna bara behöver för att autentisera till Azure Active Directory och sedan anslutningsappen för programproxyn personifierar användaren för att få en Kerberos-token och logga in till programmet. 

Information om hur du konfigurerar integrerad Windows-autentisering finns i [Kerberos-begränsad delegering för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="header-based-sign-on"></a>Rubrikbaserad inloggning 

Om ditt program använder rubriker för autentisering måste du välja **rubrikbaserad inloggning**. Med det här alternativet behöver användarna bara autentisering med Azure Active Directory. Microsoft-partner med en tredje parts autentiseringstjänst kallas PingAccess som översättas åtkomsttoken för Azure Active Directory till ett format för sidhuvud för programmet. 

Information om hur du konfigurerar rubrikbaserad autentisering finns i [rubrikbaserad autentisering för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-ping-access.md).

## <a name="next-steps"></a>Nästa steg

- [Lösenord vaulting för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos-begränsad delegering för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md)
- [Rubrikbaserad autentisering för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-ping-access.md) 
