---
title: Resurser för att migrera appar till Azure Active Directory | Microsoft Docs
description: Resurser som hjälper dig att migrera programåtkomst och autentisering till Azure Active Directory (AD Azure).
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2018
ms.author: barbkess
ms.reviewer: baselden
ms.openlocfilehash: 750bf1905a3ca352e181dcd5b7fcecdfc8d04f76
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465511"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Resurser för att migrera program till Azure Active Directory

Resurser som hjälper dig att migrera programåtkomst och autentisering till Azure Active Directory (AD Azure). 

| Resurs  | Beskrivning  |
|:-----------|:-------------|
|[Migrera dina appar till Azure AD](https://aka.ms/migrateapps/whitepaper) | Detta white paper visar fördelarna med migreringen och beskriver hur du planerar för migrering i fyra faser som tydligt beskrivs: identifiering, klassificering, migrering och kontinuerlig hantering. Du får guidas genom hur du tycker om processen och dela upp ditt projekt i lätt att förstå delar. Är länkar till viktiga resurser som hjälper dig på vägen i hela dokumentet. |
|[Lösningsguide: migrera appar från Active Directory Federation Services (AD FS) i Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Den här lösningsguiden vägleder dig genom de samma fyra stegen för planering och körning av ett programprojekt migrering som beskrivs på en högre nivå i faktabladet migrering. I den här guiden får du lära dig hur du använder dessa faser för den specifika mål för att flytta ett program från Azure Directory Federation Services (AD FS) till Azure AD.|
| [Verktyget: Active Directory Federation Services beredskap Migreringsskriptet](https://aka.ms/migrateapps/adfstools) | Det här är ett skript du kan köra på din lokala Active Directory Federation Services (AD FS) server att fastställa beredskap för migrering till Azure AD-appar.|
| [Distributionsplan: migrera från AD FS till lösenordets hash-synkronisering](https://aka.ms/ADFSTOPHSDPDownload) | Med synkronisering av lösenordshash synkroniseras hash-värden för användarlösenord från lokala Active Directory till Azure AD. På så sätt kan Azure AD för att autentisera användare utan att interagera med lokala Active Directory.| 
| [Distributionsplan: migrera från AD FS till direktautentisering](https://aka.ms/ADFSTOPTADPDownload)|Azure AD-direktautentisering hjälper användare att logga in på både lokala och molnbaserade program genom att använda samma lösenord. Den här funktionen ger användarna en bättre upplevelse eftersom de har mindre komma ihåg ett lösenord. Det minskar också IT-kostnaderna för supportavdelningen eftersom användarna är mindre troligt att glömma logga in när de bara behöver komma ihåg ett lösenord. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.|
| [Distributionsplan: Aktivera enkel inloggning till en SaaS-app med Azure AD](https://aka.ms/SSODPDownload) | Enkel inloggning (SSO) kan du komma åt alla appar och resurser du behöver för att göra affärer, när du loggar in bara en gång, med ett enda användarkonto. Till exempel när en användare har loggat in kan användaren flytta från Microsoft Office till SalesForce och Box utan autentiseras en gång (t.ex, att skriva ett lösenord). 
| [Distributionsplan: utökning av appar till Azure AD med Application Proxy](https://aka.ms/AppProxyDPDownload)| Tilldela åtkomst från medarbetare bärbara datorer och andra enheter till lokala program har traditionellt inneburit virtuella privata nätverk (VPN) eller demilitariserad zoner (DMZ-miljöer). De här lösningarna är inte bara komplexa och svåra att skydda, utan de är även dyra att konfigurera och hantera. Azure AD-programproxyn gör det enklare att åtkomst till lokala program. |
| [Planer för distribution](../fundamentals/active-directory-deployment-plans.md) | Hitta fler distribution planer för att distribuera funktioner som multifaktorautentisering, villkorlig åtkomst, användaretablering, sömlös SSO, lösenordsåterställning via självbetjäning och mer! |


