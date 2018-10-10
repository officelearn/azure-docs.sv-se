---
title: 'Azure AD Connect-synkronisering: katalogtillägg | Microsoft Docs'
description: Det här avsnittet beskriver funktionen directory tillägg i Azure AD Connect.
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
ms.date: 10/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9c40cfd9b15f2ae6398b70effc4f4d38111cfe1e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886073"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect-synkronisering: katalogtillägg
Du kan använda katalogtillägg utöka schemat i Azure Active Directory (Azure AD) med egna attribut från en lokal Active Directory. Den här funktionen kan du skapa LOB-appar genom att använda attribut som du fortsätta att hantera lokala. Dessa attribut kan användas via [Azure AD Graph API katalogtillägg](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) eller [Microsoft Graph](https://graph.microsoft.io/). Du kan se tillgängliga attribut med hjälp av [Azure AD Graph-testaren](https://graphexplorer.azurewebsites.net/) och [Microsoft Graph-testaren](https://developer.microsoft.com/graph/graph-explorer)respektive.

För närvarande förbrukar inga Office 365-arbetsbelastning dessa attribut.

Du kan konfigurera vilka ytterligare attribut som du vill synkronisera i sökvägen för anpassade inställningar i installationsguiden.

>[!NOTE]
>Rutan tillgängliga attribut är skiftlägeskänsligt.

![Schemat tillägget guiden](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Installationen visar följande attribut som är giltig kandidater:

* Användare och grupper objekttyper
* Enkelvärdesattribut: sträng, booleskt värde, heltal, binär
* Flera värden attribut: sträng, binär


>[!NOTE]
> Azure AD Connect har stöd för synkronisering med flera värden Active Directory-attribut till Azure AD som flera värden katalogtillägg. Men inga funktioner i Azure AD för närvarande stöd för användning av flera värden katalogtillägg.

Lista över attribut som läses från schema-cacheminnet som skapas under installationen av Azure AD Connect. Om du har utökat Active Directory-schemat med ytterligare attribut, måste du [uppdatera schemat](how-to-connect-installation-wizard.md#refresh-directory-schema) innan dessa nya attribut är synliga.

Ett objekt i Azure AD kan ha upp till 100 attribut för katalogtillägg. Den maximala längden är 250 tecken. Om ett attributvärde inte trunkerar Synkroniseringsmotorn den.

Under installationen av Azure AD Connect registreras ett program när attributen är tillgängliga. Du kan se det här programmet i Azure-portalen.

![Schemat tillägget app](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Attributen har prefixet tillägget \_{AppClientId}\_. AppClientId har samma värde för alla attribut i Azure AD-klienten.

Dessa attribut är nu tillgängliga via Azure AD Graph API. Du kan skicka frågor mot dem med hjälp av [Azure AD Graph-testaren](https://graphexplorer.azurewebsites.net/).

![Azure AD Graph-testaren](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Eller du kan fråga attributen via Microsoft Graph-API med hjälp av [Microsoft Graph-testaren](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Du behöver fråga efter attribut som ska returneras. Uttryckligen välja attribut så här: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select = extension_9d98ed114c4840d298fad781915f27e4_employeeID extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Mer information finns i [Microsoft Graph: Använd frågeparametrar](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Nästa steg
Läs mer om den [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
