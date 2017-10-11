---
title: "Felsöka: ”Active Directory-objekt är saknas eller är inte tillgänglig | Microsoft Docs"
description: "Vad du gör om menyalternativet för Active Directory inte visas i Azure-hanteringsportalen."
services: active-directory
documentationcenter: na
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.openlocfilehash: be3a797c4a405fd2f6636e67f4c961dd6d143486
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Felsöka: ”Active Directory-objekt är saknas eller är inte tillgänglig
Många av anvisningar för användning av Azure Active Directory-funktioner och tjänster som börjar med ”gå till Azure-hanteringsportalen och klicka på **Active Directory**”. Men vad gör du om Active Directory-tillägget eller menyobjektet inte visas eller om den är markerad **saknas**? Det här avsnittet är avsedda att hjälpa. Beskriver villkor under vilka **Active Directory** visas inte eller är inte tillgängligt och förklarar hur du fortsätter.

## <a name="active-directory-is-missing"></a>Active Directory saknas
Normalt en **Active Directory** objektet visas i den vänstra navigeringsmenyn. Anvisningarna i Azure Active Directory procedurer förutsätter att det här objektet är i vyn.

![Skärmbild som visar: Active Directory i Azure](./media/active-directory-troubleshooting/typical-view.png)

Active Directory-objektet visas i den vänstra navigeringsmenyn när något av följande villkor är sant. Annars visas inte objektet.

* Den aktuella användaren har loggat in med ett Microsoft-konto (tidigare känt som Windows Live ID).
  
    ELLER
* Azure-klient har en katalog och det aktuella kontot är en katalogadministratör.
  
    ELLER
* Azure-klient har minst en Azure AD-åtkomstkontroll (ACS) namnområde. Mer information finns i [Access Control Namespace](https://msdn.microsoft.com/library/azure/gg185908.aspx).
  
    ELLER
* Azure-klient har minst en provider för Azure Multi-Factor Authentication. Mer information finns i [administrera Azure Multi-Factor Authentication-Providers](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Klicka för att skapa ett namnområde för åtkomstkontroll eller en flerfunktionsautentiseringsleverantör **+ ny** > **Apptjänster** > **Active Directory**.

Om du vill ha administrativ behörighet till en katalog har en administratör tilldela en administratörsroll till ditt konto. Mer information finns i [Tilldela administratörsroller](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>Active Directory är inte tillgängligt
När du klickar på **+ ny** > **Apptjänster**, en **Active Directory** objektet visas. Mer specifikt visas Active Directory-objekt när någon av Active Directory-funktioner, till exempel katalog, Access Control eller leverantör av Multifaktorautent, är tillgängliga för den aktuella användaren.

Dock medan sidan läses objektet är nedtonad och markeras **inte tillgänglig**. Detta är ett tillfälligt tillstånd. Om du vänta några sekunder blir objektet tillgänglig. Om fördröjningen förlängs, löser uppdatera sidan ofta problemet.

![Skärmbild som visar: Active Directory är inte tillgängligt](./media/active-directory-troubleshooting/not-available.png)

