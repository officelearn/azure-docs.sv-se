---
title: SSH-autentisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå SSH-integrering med Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb766150339820f9356fe94311cd1ff33dda5480
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462978"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) är ett nätverks protokoll som tillhandahåller kryptering för drift nätverks tjänster på ett säkert sätt över ett oskyddat nätverk. SSH tillhandahåller också kommando rads inloggning, kör fjärrkommandon och överför filer på ett säkert sätt. Den används ofta i UNIX-baserade system som Linux®. SSH ersätter Telnet-protokollet, som inte tillhandahåller kryptering i ett oskyddat nätverk. 

Azure Active Directory (Azure AD) tillhandahåller ett tillägg för virtuella datorer (VM) för Linux®-baserade system som körs på Azure. 

## <a name="use-when"></a>Använd när 

* Arbeta med Linux®-baserade virtuella datorer som kräver fjärran sluten inloggning

* Köra fjärrkommandon i Linux®-baserade system

* Överföra filer säkert i ett oskyddat nätverk

![diagram över Azure AD med SSH-protokoll](./media/authentication-patterns/ssh-auth.png)

SSH med Azure AD

## <a name="components-of-system"></a>System komponenter 

* **Användare**: startar SSH-klienten för att upprätta en anslutning till Linux® virtuella datorer och ger autentiseringsuppgifter för autentisering.

* **Webbläsare**: komponenten som användaren interagerar med. Den kommunicerar med identitets leverantören (Azure AD) för att på ett säkert sätt autentisera och auktorisera användaren.

* **SSH-klient**: styr installationen av anslutnings processen.

* **Azure AD**: autentiserar användarens identitet med hjälp av enhets flödet och utfärdar token till de virtuella Linux-datorerna.

* **Virtuell Linux-dator**: accepterar token och ger lyckad anslutning.

## <a name="implement-ssh-with-azure-ad"></a>Implementera SSH med Azure AD 

* [Logga in på en Linux® virtuell dator med Azure Active Directory autentiseringsuppgifter – Azure Virtual Machines ](https://docs.microsoft.com/azure/virtual-machines/linux/login-using-aad) 

* [OAuth 2,0 enhets kod flöde-Microsoft Identity Platform ](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)

* [Integrera med Azure Active Directory (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

 
