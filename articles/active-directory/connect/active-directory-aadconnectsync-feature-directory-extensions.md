---
title: "Azure AD Connect-synkronisering: katalogtillägg | Microsoft Docs"
description: "Det här avsnittet beskriver katalogfunktionen i Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 9abd035b13a0d51c534eb8cac50c045012399a69
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect-synkronisering: katalogtillägg
Katalogtillägg kan du utöka schemat i Azure AD med dina egna attribut från lokala Active Directory. Den här funktionen kan du skapa LOB-appar som förbrukar attribut du fortsätta att hantera lokalt. Attributen kan användas via [Azure AD Graph katalogtillägg](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) eller [Microsoft Graph](https://graph.microsoft.io/). Du kan se den attribut tillgängliga med hjälp av [Azure AD Graph explorer](https://graphexplorer.azurewebsites.net/) och [Microsoft Graph explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) respektive.

För närvarande förbrukar inga Office 365-arbetsbelastning attributen.

Du kan konfigurera vilka ytterligare attribut som du vill synkronisera i sökvägen för anpassade inställningar i installationsguiden.
![Schemat tillägget guiden](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
Installationen visar följande attribut som är giltig kandidater:

* Användare och grupp objekttyper
* Enkelvärdesattribut anges: String, Boolean, heltal, binär
* Flera värden attribut: sträng, binär


>[!NOTE]
> Azure AD Connect har stöd för synkronisering med flera värden AD-attribut till Azure AD som flera värden katalogtillägg, men det finns för närvarande inga funktioner i Azure AD som stöder användning av flera värden katalogtillägg.

I listan med attribut läses från schema-cache skapas under installationen av Azure AD Connect. Om du har utökat Active Directory-schemat med ytterligare attribut i [schemat måste uppdateras](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) innan dessa nya attribut visas.

Ett objekt i Azure AD kan ha upp till 100 katalogattribut tillägg. Maxlängden är 250 tecken. Om ett attributvärde är längre trunkeras det av Synkroniseringsmotorn.

Under installationen av Azure AD Connect registreras ett program när dessa attribut är tillgängliga. Du kan se det här programmet i Azure-portalen.  
![Schemat tillägget App](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Attribut med tillägget prefixet\_{AppClientId}\_. AppClientId har samma värde för alla attribut i Azure AD-klienten.

Dessa attribut är nu tillgängliga via den **Azure AD Graph**:

Vi kan ställa frågor till Azure AD Graph via Azure AD Graph Utforskaren: [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)

![Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Eller via den **Microsoft Graph API**:

Vi kan ställa frågor till Microsoft Graph API via Microsoft Graph Utforskaren: [https://developer.microsoft.com/en-us/graph/graph-explorer#](https://developer.microsoft.com/en-us/graph/graph-explorer#)

>[!NOTE]
> Du måste uttryckligen begära för attributet som ska returneras. Detta kan göras genom att uttryckligen välja attribut så här: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $select = extension_9d98ed114c4840d298fad781915f27e4_employeeID extension_9d98ed114c4840d298fad781915f27e4_division för mer information finns i [Microsoft Graph: Använd frågeparametrar](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter)

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
