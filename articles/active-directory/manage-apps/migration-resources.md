---
title: Resurser för att migrera appar till Azure Active Directory | Microsoft Docs
description: Resurser som hjälper dig att migrera program åtkomst och autentisering till Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: mimart
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: b30469858a5dd83f7f5f707f74466302b3000510
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968733"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Resurser för att migrera program till Azure Active Directory

Resurser som hjälper dig att migrera program åtkomst och autentisering till Azure Active Directory (Azure AD). Ta den här korta undersökningen (https://aka.ms/AppsMigrationFeedback) för att ge feedback om hur du migrerar appar till Azure AD (inklusive Blocker för migrering, behov av verktyg, vägledning eller orsaker till att du behåller din lokala IDP). 

| Resurs  | Beskrivning  |
|:-----------|:-------------|
|[Migrera dina appar till Azure AD](https://aka.ms/migrateapps/whitepaper) | Den här white paper visar fördelarna med migreringen och beskriver hur du planerar migreringen i fyra tydliga steg: identifiering, klassificering, migrering och kontinuerlig hantering. Du kommer att vägleda dig genom processen och dela upp ditt projekt i lätt att förbruka delar. I hela dokumentet finns länkar till viktiga resurser som hjälper dig längs vägen. |
|[Lösnings guide: Migrera appar från Active Directory Federation Services (AD FS) (AD FS) till Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Den här lösnings guiden vägleder dig genom samma fyra faser i planering och körning av ett programmigrerings projekt som beskrivs på en högre nivå i dokumentationen för migreringen. I den här guiden får du lära dig hur du tillämpar dessa faser på det specifika målet med att flytta ett program från Azure Directory federerade tjänster (AD FS) till Azure AD.|
| [Verktyg: skript för Active Directory Federation Services (AD FS) migration readiness](https://aka.ms/migrateapps/adfstools) | Det här är ett skript som du kan köra på din lokala Active Directory Federation Services (AD FS)-Server (AD FS) för att fastställa om apparnas beredskap ska migreras till Azure AD.|
| [Distributions plan: Migrera från AD FS till hash-synkronisering av lösen ord](https://aka.ms/ADFSTOPHSDPDownload) | Med hash-synkronisering av lösen ord synkroniseras hashar av användar lösen ord från lokala Active Directory till Azure AD. Detta gör att Azure AD kan autentisera användare utan att interagera med den lokala Active Directory.| 
| [Distributions plan: Migrera från AD FS till direktautentisering](https://aka.ms/ADFSTOPTADPDownload)|Med Azure AD-direktautentisering kan användarna logga in i både lokala och molnbaserade program genom att använda samma lösen ord. Den här funktionen ger användarna en bättre upplevelse eftersom de har ett mindre lösen ord att komma ihåg. Det minskar också kostnaderna för IT-supportavdelningen eftersom användarna är mindre sannolika att glömma att logga in när de bara behöver komma ihåg ett lösen ord. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.|
| [Distributions plan: aktivera enkel inloggning till en SaaS-app med Azure AD](https://aka.ms/SSODPDownload) | Enkel inloggning (SSO) hjälper dig att komma åt alla appar och resurser du behöver för att göra affärer, samtidigt som du bara loggar in en gång, med ett enda användar konto. Efter att en användare har loggat in kan användaren till exempel flytta från Microsoft Office till SalesForce, till Box utan autentisering (till exempel skriva ett lösen ord) en andra gång. 
| [Distributions plan: utöka appar till Azure AD med Application Proxy](https://aka.ms/AppProxyDPDownload)| Att ge åtkomst från anställdas bärbara datorer och andra enheter till lokala program har traditionellt involverade virtuella privata nätverk (VPN) eller demilitariserad zoner (DMZs). De här lösningarna är inte bara komplexa och svåra att skydda, utan de är även dyra att konfigurera och hantera. Azure AD-programproxy gör det lättare att komma åt lokala program. |
| [Distributions planer](../fundamentals/active-directory-deployment-plans.md) | Hitta fler distributions planer för att distribuera funktioner som Multi-Factor Authentication, villkorlig åtkomst, användar etablering, sömlös SSO, självbetjäning för återställning av lösen ord och mycket mer! |


