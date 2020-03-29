---
title: Resurser för att migrera appar till Azure Active Directory | Microsoft-dokument
description: Resurser som hjälper dig att migrera programåtkomst och autentisering till Azure Active Directory (Azure AD).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968733"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Resurser för att migrera program till Azure Active Directory

Resurser som hjälper dig att migrera programåtkomst och autentisering till Azure Active Directory (Azure AD). Ta den härhttps://aka.ms/AppsMigrationFeedback) korta undersökningen (för att ge feedback om din upplevelse att migrera appar till Azure AD (inklusive blockerare till migrering, behov av verktyg /vägledning eller skäl för att behålla din lokala IDP). 

| Resurs  | Beskrivning  |
|:-----------|:-------------|
|[Migrera dina appar till Azure AD](https://aka.ms/migrateapps/whitepaper) | I den här vitboken presenteras fördelarna med migrering och beskrivs hur du planerar för migreringen i fyra tydligt skisserade faser: upptäckt, klassificering, migrering och pågående hantering. Du kommer att guidas genom hur man tänker på processen och bryta ner ditt projekt i lätt att konsumera bitar. I hela dokumentet finns länkar till viktiga resurser som hjälper dig på vägen. |
|[Lösningsguide: Migrera appar från AD FS (Active Directory Federation Services) till Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Den här lösningsguiden går igenom samma fyra faser av planering och körning av ett programmigreringsprojekt som beskrivs på en högre nivå i migreringsvitboken. I den här guiden får du lära dig hur du tillämpar dessa faser på det specifika målet att flytta ett program från Azure Directory Federated Services (AD FS) till Azure AD.|
| [Verktyg: Skript för migreringsberedskap för Active Directory Services](https://aka.ms/migrateapps/adfstools) | Det här är ett skript som du kan köra på din lokala AD FS-server (Active Directory Federation Services) för att avgöra om du är redo för appar för migrering till Azure AD.|
| [Distributionsplan: Migrera från AD FS till synkronisering av lösenordsh hash](https://aka.ms/ADFSTOPHSDPDownload) | Med synkronisering av lösenord hash synkroniseras hashar av användarlösenord från lokala Active Directory till Azure AD. Detta gör det möjligt för Azure AD att autentisera användare utan att interagera med den lokala Active Directory.| 
| [Distributionsplan: Migrera från AD FS till direktautentisering](https://aka.ms/ADFSTOPTADPDownload)|Azure AD-vidareautentisering hjälper användare att logga in på både lokala och molnbaserade program med samma lösenord. Den här funktionen ger användarna en bättre upplevelse eftersom de har ett lösenord mindre att komma ihåg. Det minskar också IT helpdesk kostnader eftersom användarna är mindre benägna att glömma hur man loggar in när de bara behöver komma ihåg ett lösenord. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.|
| [Distributionsplan: Aktivera enkel inloggning till en SaaS-app med Azure AD](https://aka.ms/SSODPDownload) | Enkel inloggning (SSO) hjälper dig att komma åt alla appar och resurser du behöver göra affärer, samtidigt som du loggar in bara en gång, med ett enda användarkonto. När en användare till exempel har loggat in kan användaren flytta från Microsoft Office, till SalesForce, till Box utan att autentisera (till exempel skriva ett lösenord) en andra gång. 
| [Distributionsplan: Utöka appar till Azure AD med programproxy](https://aka.ms/AppProxyDPDownload)| Att ge åtkomst från anställdas bärbara datorer och andra enheter till lokala program har traditionellt involverat virtuella privata nätverk (VPN) eller demilitariserade zoner (DMZs). De här lösningarna är inte bara komplexa och svåra att skydda, utan de är även dyra att konfigurera och hantera. Azure AD Application Proxy gör det enklare att komma åt lokala program. |
| [Distributionsplaner](../fundamentals/active-directory-deployment-plans.md) | Hitta fler distributionsplaner för distribution av funktioner som multifaktorautentisering, villkorlig åtkomst, användaretablering, sömlös SSO, återställning av lösenord med självbetjäning med mera! |


