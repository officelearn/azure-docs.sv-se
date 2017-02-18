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
ms.date: 02/14/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: 7b1510ccfed939e27bad4fc0e5fafd4383db4a35


---
# <a name="get-started-with-azure-ad-privileged-identity-management"></a>Komma igång med Azure Privileged Identity Management
Med Azure Active Directory (AD) Privileged Identity Management kan du hantera, kontrollera och övervaka åtkomst inom din organisation. Detta inkluderar åtkomst till resurser i Azure AD och andra Microsoft online-tjänster som Office 365 eller Microsoft Intune.

Den här artikeln visar hur du lägger till appen Azure AD PIM på instrumentpanelen på Azure-portalen.

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Innan du använder Azure AD Privileged Identity Management måste du lägga till programmet på instrumentpanelen på Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) som global administratör för din katalog.
2. Om din organisation har mer än en katalog väljer du ditt användarnamn längst upp till höger på Azure-portalen. Välj den katalog där du ska använda PIM.
3. Välj **Fler tjänster** och använd textrutan Filter för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Privileged Identity Management-programmet öppnas.

Om du är den första som använder Azure AD Privileged Identity Management i din katalog kommer [säkerhetsguiden](active-directory-privileged-identity-management-security-wizard.md) att vägleda dig genom den första tilldelningen. Därefter blir du automatiskt katalogens första **säkerhetsadministratör** och **privilegierade rolladministratör**. Endast en administratör av privilegierade roller kan komma åt det här programmet för att hantera åtkomsten för andra administratörer.  

## <a name="navigate-to-your-tasks"></a>Gå till dina uppgifter
När Azure AD Privileged Identity Management har ställts in ser du alltid navigeringsbladet när du öppnar programmet. Använd det här bladet för att utföra dina identitetshanteringsaktiviteter.

![Toppnivåaktiviteter för PIM - skärmbild](./media/active-directory-privileged-identity-management-getting-started/pim_tasks.png)

* **Aktivera min roller** tar dig till listan över roller som har tilldelats dig. Det är här du ska aktivera alla roller som du är berättigad till.
* **Hantera privilegierade roller** är instrumentpanel för privilegierade rolladministratörer, för att hantera rolltilldelningar, ändra rollaktiveringsinställningar starta åtkomstgranskningar och mycket mer. Alternativen i den här instrumentpanelen är inaktiverade för alla som inte är en privilegierad rolladministratör.
* **Granska privilegierad åtkomst** tar dig till alla väntande åtkomstgranskningar som du måste slutföra, oavsett om du granskar åtkomst åt dig själv eller någon annan. 

## <a name="next-steps"></a>Nästa steg
[Azure AD Privileged Identity Management-översikt](active-directory-privileged-identity-management-configure.md) innehåller mer information om hur du kan hantera administrativ åtkomst i din organisation.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png



<!--HONumber=Feb17_HO1-->


