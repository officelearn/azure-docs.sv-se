---
title: PowerShell-exempel för Azure AD-programproxy
description: Använd dessa PowerShell-exempel för Azure AD Application Proxy för att få information om programproxy-appar och -kopplingar i katalogen, tilldela användare och grupper till appar och hämta certifikatinformation.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481270"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD PowerShell-exempel för Azure AD-programproxy

Följande tabell innehåller länkar till PowerShell-skriptexempel för Azure AD Application Proxy. Dessa exempel kräver antingen [AzureAD V2 PowerShell för Graph-modulen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) eller [AzureAD V2 PowerShell for Graph-modulens förhandsversion](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), om inget annat anges.


Mer information om de cmdlets som används i dessa exempel finns i [Application Proxy Application Management](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) och Application Proxy Connector [Management](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| | |
|---|---|
|**Programproxy-appar**||
| [Lista grundläggande information för alla programproxyappar](scripts/powershell-get-all-app-proxy-apps-basic.md) | Visar grundläggande information (AppId, DisplayName, ObjId) om alla programproxyappar i katalogen . |
| [Lista utökad information för alla programproxyappar](scripts/powershell-get-all-app-proxy-apps-extended.md) | Visar utökad information (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) om alla programproxyappar i katalogen.  |
| [Lista alla programproxyappar efter anslutningsgrupp](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Visar information om alla programproxyappar i katalogen och vilka anslutningsgrupper apparna är tilldelade till. |
| [Hämta alla Application Proxy-appar med en princip för tokenlivslängd](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Visar alla programproxyappar i katalogen med en tokenlivsprincip och dess information. Det här exemplet kräver förhandsversionen av [AzureAD V2 PowerShell for Graph-modulen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview). |
|**Anslutningsgrupper**||
| [Hämta alla kopplingsgrupper och kopplingar i katalogen](scripts/powershell-get-all-connectors.md) | Visar alla anslutningsgrupper och kopplingar i katalogen. |
| [Flytta alla appar som tilldelats en kopplingsgrupp till en annan kopplingsgrupp](scripts/powershell-move-all-apps-to-connector-group.md) | Flyttar alla program som för närvarande är tilldelade till en kopplingsgrupp till en annan kopplingsgrupp. |
|**Användare och grupp som tilldelats**||
| [Visa användare och grupper som tilldelats ett programproxyprogram](scripts/powershell-display-users-group-of-app.md) | Visar en lista över användare och grupper som tilldelats ett visst programproxyprogram. |
| [Tilldela en användare till ett program](scripts/powershell-assign-user-to-app.md) | Tilldelar en viss användare ett program. |
| [Tilldela en grupp till ett program](scripts/powershell-assign-group-to-app.md) | Tilldelar en viss grupp till ett program. |
|**Extern URL-konfiguration**||
| [Hämta alla programproxyappar med standarddomäner (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Visar en lista över alla programproxyprogram med standarddomäner (.msappproxy.net). |
| [Hämta alla Programproxy-appar med jokerteckenpublicering](scripts/powershell-get-all-wildcard-apps.md) | Visar alla programproxy-appar med jokerteckenpublicering. |
|**Anpassad domänkonfiguration**||
| [Hämta alla Programproxy-appar med hjälp av anpassade domäner och certifikatinformation](scripts/powershell-get-all-custom-domains-and-certs.md) | Visar alla programproxyappar som använder anpassade domäner och certifikatinformationen som är associerad med de anpassade domänerna. |
| [Få alla Azure AD-proxyprogram publicerade utan att certifikatet har överförts](scripts/powershell-get-all-custom-domain-no-cert.md) | Visar alla programproxyappar som använder anpassade domäner men inte har ett giltigt TLS/SSL-certifikat uppladdat. |
| [Få alla Azure AD-proxyprogram publicerade med identiska certifikat](scripts/powershell-get-custom-domain-identical-cert.md) | Visar alla Azure AD-proxyprogram som har publicerats med samma certifikat. |
| [Få alla Azure AD Proxy-programappar publicerade med identiska certifikat och ersätt det](scripts/powershell-get-custom-domain-replace-cert.md) | För Azure AD Proxy-programappar som publiceras med ett identiskt certifikat kan du ersätta certifikatet i grupp. |
