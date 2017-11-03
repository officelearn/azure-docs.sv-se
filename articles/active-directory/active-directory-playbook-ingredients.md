---
title: "Azure Active Directory PoC Playbook beståndsdelar | Microsoft Docs"
description: "Utforska och snabbt implementera scenarier för identitets- och åtkomsthantering"
services: active-directory
keywords: Azure active directory, playbook, konceptbevis, PoC
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 7d43be3cbfd63b6bc5f06426e9810a37bbf3d071
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Azure Active Directory bevis på koncept Playbook komponenter 

## <a name="theme"></a>Tema
Azure AD innehåller identitets- och lösningar i flera områden i företaget. Vi klassificera scenarier inom följande områden: 

* [Många appar, en identitet](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [Öka säkerheten](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [Skala med självbetjäning](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

Definiera ett tema för att ram PoC hjälper dig att fokusera på arbetet som får respons med verksamhetsmål som ofta är av intresse för ett konceptbevis utlösare i första hand. 

## <a name="environment"></a>Miljö

Det är viktigt att fastställa information om miljön där du levererar konceptbeviset. Det bästa du kan bygga vidare på den när konceptbeviset har slutförts. Målmiljön är avgörande bör du hitta rätt balans mellan att göra det som verkliga som möjligt och arbetet med begränsningar eller ytterligare överväganden. Miljöer för POC är:
* **Produktion:** scenarier kommer att genomföras i live-miljön och redan har distribuerats Microsoft Cloud-tjänsterna (produktion AD, Office 365, Azure AD-klient/SSO lösning). 
* **Användaren godkänner testa (UAT) / utvecklingsmiljö:** du har test-infrastruktur (parallell AD och eventuellt Azure AD-klient/SSO-lösning) med testdata som liknar produktion. Normalt delas testmiljön mellan flera projekt i företaget.

De flesta scenarier i den här handboken är additiva till sin natur. De kan därför distribueras i produktion klienten utan att påverka användare utanför konceptbeviset. I det här dokumentet ringer vi ut vilka scenarier som skulle ha klient hela effekt. I sådana fall kanske du vill överväga en icke-produktionsmiljö. 


## <a name="target-users"></a>Målgruppsanvändare

Det är viktigt att fastställa vilken uppsättning användare som ska ha scenarier, särskilt när miljön är produktions- och mål. Mål-användare för PoC är:
* **Pilotanvändarna:** verkliga användare i miljön som kommer att använda lösningen med det konto de använder för sina dagliga jobbfunktioner
* **Testanvändare:** testa konton som har skapats i miljön 

De flesta scenarier i den här guiden kan utnyttjas av pilotanvändare. I det här dokumentet kommer vi ringer ut mål användaren överväganden om det behövs.


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]