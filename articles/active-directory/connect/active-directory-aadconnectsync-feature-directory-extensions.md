---
title: 'Azure AD Connect-synkronisering: katalogtillägg | Microsoft Docs'
description: Det här avsnittet beskriver katalogfunktionen i Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: dda35e63c209951547a667c46639dc0f37c87b43
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593640"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect-synkronisering: katalogtillägg
Du kan använda katalogtillägg för att utöka schemat i Azure Active Directory (AD Azure) med egna attribut från lokala Active Directory. Den här funktionen kan du skapa LOB-appar genom att använda attribut som du även fortsättningsvis att hantera lokala. Attributen kan användas via [Azure AD Graph API katalogtillägg](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) eller [Microsoft Graph](https://graph.microsoft.io/). Du kan se tillgängliga attribut med hjälp av [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) och [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)respektive.

För närvarande förbrukar inga Office 365-arbetsbelastning attributen.

Du kan konfigurera vilka ytterligare attribut som du vill synkronisera i sökvägen för anpassade inställningar i installationsguiden.

![Schemat tillägget guiden](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  

Installationen visar följande attribut som är giltig kandidater:

* Användare och grupp objekttyper
* Enkelvärdesattribut anges: String, Boolean, heltal, binär
* Flera värden attribut: sträng, binär


>[!NOTE]
> Azure AD Connect har stöd för synkronisering med flera värden Active Directory-attribut till Azure AD som katalogtillägg med flera värden. Men inga funktioner i Azure AD för närvarande stöd för flera värden katalogtillägg.

I listan med attribut läses från schema-cache skapas under installationen av Azure AD Connect. Om du har utökat Active Directory-schemat med ytterligare attribut, måste du [uppdatera schemat](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) innan dessa nya attribut visas.

Ett objekt i Azure AD kan ha upp till 100 attribut för katalogtillägg. Den maximala längden är 250 tecken. Om ett attributvärde är längre trunkerar Synkroniseringsmotorn den.

Under installationen av Azure AD Connect registreras ett program när dessa attribut är tillgängliga. Du kan se det här programmet i Azure-portalen.

![Schemat tillägget app](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Attribut med tillägget prefixet \_{AppClientId}\_. AppClientId har samma värde för alla attribut i Azure AD-klienten.

Dessa attribut är nu tillgängliga via Azure AD Graph API. Du kan fråga dem med hjälp av [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/).

![Azure AD Graph Explorer](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Eller fråga attributen via Microsoft Graph-API med hjälp av [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer#).

>[!NOTE]
> Du behöver fråga för de attribut som ska returneras. Uttryckligen väljer attribut så här: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select = extension_9d98ed114c4840d298fad781915f27e4_employeeID extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Mer information finns i [Microsoft Graph: Använd frågeparametrar](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
