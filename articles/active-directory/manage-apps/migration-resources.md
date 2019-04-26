---
title: Resurser för att migrera appar till Azure Active Directory | Microsoft Docs
description: Resurser som hjälper dig att migrera programåtkomst och autentisering till Azure Active Directory (AD Azure).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2018
ms.author: celested
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92ce62d745bfb0299dc8f3eb401f5a66e38580f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440670"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Resurser för att migrera program till Azure Active Directory

Resurser som hjälper dig att migrera programåtkomst och autentisering till Azure Active Directory (AD Azure). Den här korta undersökningen (https://aka.ms/AppsMigrationFeedback) att ge feedback om din upplevelse migrera appar till Azure AD (inklusive blockeringar för migrering behöver du för verktyg / hjälp eller skäl för att bevara den lokala IDP: N). 

| Resurs  | Beskrivning  |
|:-----------|:-------------|
|[Migrera dina appar till Azure AD](https://aka.ms/migrateapps/whitepaper) | Detta white paper visar fördelarna med migreringen och beskriver hur du planerar för migrering i fyra faser som tydligt beskrivs: identifiering, klassificering, migrering och kontinuerlig hantering. Du får guidas genom hur du tycker om processen och dela upp ditt projekt i lätt att förstå delar. Är länkar till viktiga resurser som hjälper dig på vägen i hela dokumentet. |
|[Lösningsguiden: Migrera appar från Active Directory Federation Services (AD FS) i Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Den här lösningsguiden vägleder dig genom de samma fyra stegen för planering och körning av ett programprojekt migrering som beskrivs på en högre nivå i faktabladet migrering. I den här guiden får du lära dig hur du använder dessa faser för den specifika mål för att flytta ett program från Azure Directory Federation Services (AD FS) till Azure AD.|
| [Verktyget: Beredskap för Active Directory Federation Services Migreringsskriptet](https://aka.ms/migrateapps/adfstools) | Det här är ett skript du kan köra på din lokala Active Directory Federation Services (AD FS) server att fastställa beredskap för migrering till Azure AD-appar.|
| [Distributionsplan: Migrera från AD FS till lösenordets hash-synkronisering](https://aka.ms/ADFSTOPHSDPDownload) | Med synkronisering av lösenordshash synkroniseras hash-värden för användarlösenord från lokala Active Directory till Azure AD. På så sätt kan Azure AD för att autentisera användare utan att interagera med lokala Active Directory.| 
| [Distributionsplan: Migrera från AD FS till direktautentisering](https://aka.ms/ADFSTOPTADPDownload)|Azure AD-direktautentisering hjälper användare att logga in på både lokala och molnbaserade program genom att använda samma lösenord. Den här funktionen ger användarna en bättre upplevelse eftersom de har mindre komma ihåg ett lösenord. Det minskar också IT-kostnaderna för supportavdelningen eftersom användarna är mindre troligt att glömma logga in när de bara behöver komma ihåg ett lösenord. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.|
| [Distributionsplan: Aktivera enkel inloggning till en SaaS-app med Azure AD](https://aka.ms/SSODPDownload) | Enkel inloggning (SSO) kan du komma åt alla appar och resurser du behöver för att göra affärer, när du loggar in bara en gång, med ett enda användarkonto. Till exempel när en användare har loggat in kan användaren flytta från Microsoft Office till SalesForce och Box utan autentiseras en gång (t.ex, att skriva ett lösenord). 
| [Distributionsplan: Utökning av appar till Azure AD med Application Proxy](https://aka.ms/AppProxyDPDownload)| Tilldela åtkomst från medarbetare bärbara datorer och andra enheter till lokala program har traditionellt inneburit virtuella privata nätverk (VPN) eller demilitariserad zoner (DMZ-miljöer). De här lösningarna är inte bara komplexa och svåra att skydda, utan de är även dyra att konfigurera och hantera. Azure AD-programproxyn gör det enklare att åtkomst till lokala program. |
| [Planer för distribution](../fundamentals/active-directory-deployment-plans.md) | Hitta fler distribution planer för att distribuera funktioner som multifaktorautentisering, villkorlig åtkomst, användaretablering, sömlös SSO, lösenordsåterställning via självbetjäning och mer! |


