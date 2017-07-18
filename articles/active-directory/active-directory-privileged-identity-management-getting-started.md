---
title: "Komma igång med Azure AD Privileged Identity Management | Microsoft Docs"
description: "Lär dig hur du hanterar privilegierade identiteter med programmet Azure Active Directory Privileged Identity Management på Azure-portalen."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 17cdff033cc3dbb199d11c3b8ac1acbc92499877
ms.contentlocale: sv-se
ms.lasthandoff: 07/10/2017

---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Börja använda Azure AD Privileged Identity Management
Med Azure Active Directory (AD) Privileged Identity Management kan du hantera, kontrollera och övervaka åtkomst inom din organisation. Det här omfånget inkluderar åtkomst till resurser i Azure AD och andra Microsoft online-tjänster som Office 365 eller Microsoft Intune.

Den här artikeln visar hur du lägger till appen Azure AD PIM på instrumentpanelen på Azure-portalen.

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Innan du använder Azure AD Privileged Identity Management måste du lägga till programmet på instrumentpanelen på Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) som global administratör för din katalog.
2. Om din organisation har mer än en katalog väljer du ditt användarnamn längst upp till höger på Azure-portalen. Välj den katalog där du vill använda PIM.
3. Välj **Fler tjänster** och använd textrutan Filter för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Privileged Identity Management-programmet öppnas.

Om du är den första personen som använder Azure AD Privileged Identity Management i din katalog så tilldelas du automatiskt rollerna **Säkerhetsadministratör** och **Privilegierad rolladministratör** i katalogen. Endast privilegierade rolladministratörer kan hantera rolltilldelningar för användare. Dessutom kan du välja att köra [Säkerhetsguiden.](active-directory-privileged-identity-management-security-wizard.md) som från grunden lär dig hur du identifierar och tilldelar.

## <a name="navigate-to-your-tasks"></a>Gå till dina uppgifter
När Azure AD Privileged Identity Management har konfigurerats ser du alltid navigeringsbladet när du öppnar programmet. Använd det här bladet för att utföra dina identitetshanteringsaktiviteter.

![Toppnivåaktiviteter för PIM - skärmbild](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

* **Mina roller** tar dig till en lista över roller som har tilldelats dig. Du aktiverar alla roller som du är berättigad till i det här avsnittet.
* **Godkänna förfrågningar (förhandsgranskning)** visar en lista över väntande aktiveringsförfrågningar från användare i din katalog. [Läs mer.](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
* **Väntande förfrågningar (förhandsgranskning)** visar aktuella förfrågningar om aktivering.
* **Granska åtkomst** tar dig till alla väntande åtkomstgranskningar som du måste slutföra, oavsett om du granskar åtkomst åt dig själv eller någon annan.
* **Azure AD-katalogroller** finns under avsnittet ”Hantera” och är instrumentpanelen för privilegierade rolladministratörer. Där kan du hantera rolltilldelningar, ändra rollaktiveringsinställningar, starta åtkomstgranskningar och mycket mer. Alternativen i den här instrumentpanelen är inaktiverade för alla som inte är en privilegierad rolladministratör.

## <a name="next-steps"></a>Nästa steg
[Azure AD Privileged Identity Management-översikt](active-directory-privileged-identity-management-configure.md) innehåller mer information om hur du kan hantera administrativ åtkomst i din organisation.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

