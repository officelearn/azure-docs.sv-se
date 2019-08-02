---
title: 'Azure AD Connect synkronisering: Katalog tillägg | Microsoft Docs'
description: I det här avsnittet beskrivs funktionen katalog tillägg i Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fdfce58bdd8e13637e77d01d4b6c0ab21f696a
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607648"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect synkronisering: Katalogtillägg
Du kan använda katalog tillägg för att utöka schemat i Azure Active Directory (Azure AD) med dina egna attribut från lokala Active Directory. Med den här funktionen kan du bygga LOB-appar genom att förbruka attribut som du fortsätter att hantera lokalt. Dessa attribut kan användas via [Azure AD Graph API Directory-tillägg](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) eller [Microsoft Graph](https://developer.microsoft.com/graph/). Du kan se tillgängliga attribut med hjälp av [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) respektive [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

Inga Office 365-arbetsbelastningar förbrukar dessa attribut.

Du konfigurerar vilka ytterligare attribut som du vill synkronisera i sökvägen för anpassade inställningar i installations guiden.

>[!NOTE]
>Rutan Tillgängliga attribut är Skift läges känslig.

![Guiden schema tillägg](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

I installationen visas följande attribut, som är giltiga kandidater:

* Användar-och grupp objekt typer
* Attribut med enkel värde: Sträng, boolesk, heltal, binär
* Attribut med flera värden: Sträng, binär


>[!NOTE]
> Även om Azure AD Connect stöder synkronisering av multi-Value Active Directory-attribut till Azure AD som flera katalog tillägg för flera värden, finns det för närvarande inget sätt att hämta/använda data som laddas upp i attribut för flera värdefulla katalog tillägg.

Listan med attribut läses från det schema-cacheminne som skapas under installationen av Azure AD Connect. Om du har utökat Active Directory schema med ytterligare attribut måste du [uppdatera schemat](how-to-connect-installation-wizard.md#refresh-directory-schema) innan dessa nya attribut visas.

Ett objekt i Azure AD kan ha upp till 100 attribut för katalog tillägg. Den maximala längden är 250 tecken. Om ett attributvärde är längre, trunkerar Synkroniseringsmotorn det.

Under installationen av Azure AD Connect registreras ett program där dessa attribut är tillgängliga. Du kan se det här programmet i Azure Portal.

![App för schema tillägg](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Attributen föregås av tillägget \_{AppClientId}.\_ AppClientId har samma värde för alla attribut i din Azure AD-klient.

De här attributen är nu tillgängliga via Azure AD-Graph API. Du kan fråga dem med hjälp av [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/).

![Azure AD Graph Explorer](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

Du kan också fråga attributen via Microsoft Graph-API: et genom att använda [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Du måste be om de attribut som ska returneras. Välj uttryckligen attributen så här: https\://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $Select = extension_9d98ed114c4840d298fad781915f27e4_employeeID, extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> Mer information finns i [Microsoft Graph: Använd frågeparametrar](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure AD Connect Sync](how-to-connect-sync-whatis.md) -konfigurationen.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
