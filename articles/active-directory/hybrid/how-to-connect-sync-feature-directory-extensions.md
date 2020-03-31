---
title: 'Synkronisering av Azure AD Connect: Katalogtillägg | Microsoft-dokument'
description: I det här avsnittet beskrivs funktionen för katalogtillägg i Azure AD Connect.
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
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917921"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Synkronisering av Azure AD Connect: Katalogtillägg
Du kan använda katalogtillägg för att utöka schemat i Azure Active Directory (Azure AD) med dina egna attribut från lokala Active Directory. Med den här funktionen kan du skapa LOB-appar genom att använda attribut som du fortsätter att hantera lokalt. Dessa attribut kan förbrukas via [tillägg](https://docs.microsoft.com/graph/extensibility-overview
). Du kan se tillgängliga attribut med hjälp av [Utforskaren](https://developer.microsoft.com/graph/graph-explorer)i Microsoft Graph . Du kan också använda den här funktionen för att skapa dynamiska grupper i Azure AD.

För närvarande förbrukar ingen Office 365-arbetsbelastning dessa attribut.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Anpassa vilka attribut som ska synkroniseras med Azure AD

Du kan konfigurera vilka ytterligare attribut du vill synkronisera i sökvägen till anpassade inställningar i installationsguiden.

>[!NOTE]
>Rutan Tillgängliga attribut är skiftlägeskänslig.

![Guiden Schematillägg](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

Installationen visar följande attribut, som är giltiga kandidater:

* Objekttyper för användare och grupper
* Attribut med ett värde: Sträng, Boolean, Heltal, Binär
* Multi-värderade attribut: Sträng, Binär


>[!NOTE]
> Även om Azure AD Connect stöder synkronisering av multivärderade Active Directory-attribut till Azure AD som flervärdeskatalogtillägg, finns det för närvarande inget sätt att hämta/använda data som överförs i attribut för flervärdeskatalogtillägg.

Listan över attribut läss från schemacachen som skapas under installationen av Azure AD Connect. Om du har utökat Active Directory-schemat med ytterligare attribut måste du [uppdatera schemat](how-to-connect-installation-wizard.md#refresh-directory-schema) innan dessa nya attribut visas.

Ett objekt i Azure AD kan ha upp till 100 attribut för katalogtillägg. Den maximala längden är 250 tecken. Om ett attributvärde är längre trunkeras synkroniseringsmotorn.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Konfigurationsändringar i Azure AD som gjorts av guiden

Under installationen av Azure AD Connect registreras ett program där dessa attribut är tillgängliga. Du kan se det här programmet i Azure-portalen. Dess namn är alltid **Tenant Schema Extension App**.

![Appen Schematillägg](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Se till att du väljer **Alla program** för att se den här appen.

Attributen föregås av **tillägget \_{ApplicationId}\_**. ApplicationId har samma värde för alla attribut i din Azure AD-klientorganisation. Du behöver det här värdet för alla andra scenarier i det här avsnittet.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Visa attribut med Microsoft Graph API

Dessa attribut är nu tillgängliga via Microsoft Graph API, med hjälp av [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> I Microsoft Graph API måste du be om att attributen ska returneras. Markera uttryckligen attributen så `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`här: .
>
> Mer information finns i [Microsoft Graph: Använd frågeparametrar](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Använda attributen i dynamiska grupper

Ett av de mer användbara scenarierna är att använda dessa attribut i dynamisk säkerhet eller Office 365-grupper.

1. Skapa en ny grupp i Azure AD. Ge det ett bra namn och se till att **medlemstypen** är **dynamisk användare**.

   ![Skärmbild med en ny grupp](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Välj om du **vill lägga till dynamisk fråga**. Om du tittar på egenskaperna kommer du inte att se dessa utökade attribut. Du måste lägga till dem först. Klicka på **Hämta anpassade tilläggsegenskaper,** ange program-ID och klicka på **Uppdatera egenskaper**.

   ![Skärmbild där katalogtillägg har lagts till](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Öppna listrutan för egenskapen och observera att de attribut som du har lagt till nu är synliga.

   ![Skärmbild med nya attribut som visas i användargränssnittet](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Fyll i uttrycket som passar dina behov. I vårt exempel är regeln inställd på **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Försäljning och marknadsföring")**.

4. När gruppen har skapats ger du Azure AD lite tid att fylla i medlemmarna och sedan granska medlemmarna.

   ![Skärmbild med medlemmar i den dynamiska gruppen](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Nästa steg
Läs mer om synkroniseringskonfigurationen för [Azure AD Connect.](how-to-connect-sync-whatis.md)

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
