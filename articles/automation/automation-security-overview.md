---
title: Introduktion till autentisering i Azure Automation
description: I den här artikeln ges en översikt över säkerheten i Automation och de olika autentiseringsmetoder som är tillgängliga för Automation-konton i Azure Automation.
keywords: automation security, secure automation; automation authentication
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ROBOTS: NOINDEX
ms.openlocfilehash: 60175a5b9309c08d5fdab997469a9d0526afaefe
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437971"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Introduktion till autentisering i Azure Automation  
Med Azure Automation kan du automatisera åtgärder mot resurser i Azure, lokalt och med andra molnproviders, till exempel Amazon Web Services (AWS).  För att en runbook ska kunna utföra sina åtgärder måste den ha behörighet att komma åt resurserna på ett säkert sätt med den minsta behörighet som krävs i prenumerationen.

Den här artikeln täcker de olika autentiseringsscenarier som stöds av Azure Automation och visar hur du kommer igång baserat på den eller de miljöer som du behöver hantera.  

## <a name="automation-account-overview"></a>Översikt för Automation-konton
När du startar Azure Automation för första gången måste du skapa minst ett Automation-konto. Automation-konton gör att du kan isolera dina Automation-resurser (runbooks, tillgångar, konfigurationer) från resurserna som finns i andra Automation-konton. Du kan använda Automation-konton för att hålla resurser åtskilda i separata logiska miljöer. Du kan exempelvis använda ett konto för utveckling, ett annat för produktion och ett annat för din lokala miljö.  Ett Azure Automation-konto skiljer sig från ditt eller dina Microsoft-konton som skapats i Azure-prenumerationen.

Automation-resurserna för varje Automation-konto associeras med en enda Azure-region, men Automation-konton kan hantera alla resurser i din prenumeration. Huvudskälet till att skapa Automation-konton i olika regioner är om du har principer som kräver att data och resurser är isolerade i en specifik region.

Alla aktiviteter som du utför mot resurser med hjälp av Azure Resource Manager och Azure-cmdletar i Azure Automation måste autentisera till Azure med hjälp av autentiseringsuppgiftsbaserad autentisering med organisationens Azure Active Directory-ID.  Certifikatbaserad autentisering var den ursprungliga autentiseringsmetoden i klassisk Azure men det var svårt att konfigurera.  Autentisering i Azure med Azure AD-användare infördes igen 2014 inte bara för att förenkla processen att konfigurera ett autentiseringskonto, men även för att ge stöd för icke-interaktiv autentisering i Azure med ett enda användarkonto som fungerade med både Azure Resource Manager- och klassiska resurser.   

För närvarande när du skapar ett nytt Automation-konto på Azure Portal, skapas automatiskt:

* Kör som-kontot som skapar ett nytt namn för tjänstobjektet i Azure Active Directory och ett certifikat samt tilldelar rollen som deltagare med rollbaserad åtkomstkontroll (RBAC), som används för att hantera Resource Manager-resurser med hjälp av runbooks.
* Ett klassiskt Kör som-konto genom att överföra ett hanteringscertifikat, som används för att hantera klassiska Azure-resurser med hjälp av runbooks.  

Rollbaserad åtkomstkontroll är tillgängligt i Azure Resource Manager-läge för att bevilja tillåtna åtgärder för ett Azure AD-användarkonto och Kör som-konto för att autentisera tjänstobjektet.  Mer information om hur du utvecklar din modell för att hantera Automation-behörigheter finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).  

Runbooks som körs i en Hybrid Runbook Worker i ditt datacenter eller mot beräkningstjänster i AWS kan inte använda samma metod som normalt används för runbooks-autentisering mot Azure-resurser.  Detta beror på att resurserna körs utanför Azure och därför kräver sina egna säkerhetsreferenser i Automation för att autentisera mot resurser som de ska komma åt lokalt.  

## <a name="authentication-methods"></a>Autentiseringsmetoder
Följande tabell sammanfattar de olika autentiseringsmetoderna för varje miljö som stöds av Azure Automation och artikeln som beskriver hur du konfigurerar autentisering för dina runbooks.

| Metod | Miljö | Artikel |
| --- | --- | --- |
| Azure AD-användarkonto |Azure Resource Manager och klassisk Azure |[Autentisera Runbooks med Azure AD-användarkonto](automation-create-aduser-account.md) |
| Kör som-konto i Azure |Azure Resource Manager |[Autentisera runbooks med ett ”Kör som”-konto i Azure](automation-sec-configure-azure-runas-account.md) |
| Klassiskt Kör som-konto i Azure |Den klassiska Azure-portalen |[Autentisera runbooks med ett ”Kör som”-konto i Azure](automation-sec-configure-azure-runas-account.md) |
| Windows-autentisering |Lokalt datacenter |[Autentisera runbooks för Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) |
| AWS-autentiseringsuppgifter |Amazon Web Services |[Autentisera runbooks med Amazon Web Services (AWS)](automation-config-aws-account.md) |

