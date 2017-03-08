---
title: "Lägga till ett eget domännamn i Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du lägger till ditt företags domännamn i Azure Active Directory och hur du verifierar namnet."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 35a6e20a-9907-432b-9d36-16b916a5c249
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/30/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: f1f3d6fa1ba29b0d43d29c7d9cccfc430fdd6f1e
ms.openlocfilehash: 9803b4e41223bc979e4c0b0a4f22586a441c28df
ms.lasthandoff: 02/22/2017


---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>Lägga till ett anpassat domännamn i Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure-portal](active-directory-domains-add-azure-portal.md)
> * [Klassisk Azure-portal](active-directory-add-domain.md)
> 
> 

Din organisation använder ett eller flera domännamn för att göra affärer och dina användare loggar in i företagsnätverket med företagets domännamn. Nu när du använder Azure Active Directory (AD Azure) kan du även lägga till företagets domännamn i Azure AD. På så sätt kan du tilldela användarnamn i katalogen som dina användare känner igen, som ”alice@contoso.com”. Processen är enkel:

1. Lägga till det anpassade domännamnet i din katalog
2. Lägga till en DNS-post för domännamnet hos domännamnsregistratorn
3. Verifiera det anpassade domännamnet i Azure AD

> [!NOTE]
> Om du planerar att konfigurera det anpassade domännamn som ska användas med Active Directory Federation Services (AD FS) eller en annan säkerhetstokentjänst (STS) på företagets nätverk så följ anvisningarna i [Lägg till och konfigurera en domän för federation med Azure Active Directory](active-directory-add-domain-federated.md). Detta är användbart om du tänker synkronisera användare från din företagskatalog med Azure AD och [hash-synkronisering av lösenord](active-directory-aadconnectsync-implement-password-synchronization.md) inte uppfyller dina krav.
> 
> 

## <a name="add-a-custom-domain-name-to-your-directory"></a>Lägga till ett eget domännamn i katalogen
1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com/) med ett användarkonto som är en global administratör i Azure AD-katalogen.
2. I **Active Directory** öppnar du din katalog och väljer fliken **Domäner**.
3. Välj **Lägg till** i kommandofältet. Ange den anpassade domänens namn, t.ex. contoso.com. Glöm inte att inkludera .com, .net eller något annat toppnivåtillägg, och låt kryssrutan för enkel inloggning (federation) vara avmarkerad.
4. Välj **Lägg till**.
5. Gå till den andra sidan i guiden Lägg till domän och hämta den DNS-post som Azure AD ska använda för att verifiera att din organisation äger det anpassade domännamnet .

Nu när du har lagt till domännamnet måste Azure AD kontrollera att din organisation äger domännamnet. Innan Azure AD kan utföra den här kontrollen måste du lägga till en DNS-post i DNS-zonfilen för domännamnet. Den här uppgiften utförs på webbplatsen för domännamnsregistratorn för domännamnet.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Lägg till DNS-posten hos domänens domännamnsregistrator
Nästa steg mot att kunna använda ditt anpassade domännamn med Azure AD är att du uppdaterar domänens DNS-zonfil. Detta gör det möjligt för Azure AD att verifiera att din organisation äger det anpassade domännamnet.

1. Logga in hos domännamnsregistratorn för domänen. Om du inte har den åtkomst som krävs för att uppdatera DNS-posten så be den person eller grupp i din organisation som har denna åtkomst att utföra steg 2 och att meddela dig när det är klart.
2. Uppdatera DNS-zonfilen för domänen genom att lägga till DNS-posten som du fått från Azure AD. Den här DNS-posten gör att Azure AD kan verifiera ditt ägarskap för domänen. DNS-posten ändrar inga beteenden, till exempel e-postdirigering eller webbhosting.

Om du behöver hjälp med att lägga till DNS-posten kan du läsa [Anvisningar om hur man lägger till en DNS-post hos en vanlig DNS-registrator](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verifiera domännamnet med Azure AD
När du har lagt till DNS-posten kan du verifiera domännamnet med Azure AD.

Om guiden **Lägg till domän** fortfarande är öppen väljer du **Verifiera** på den tredje sidan i guiden. När du väljer **Verifiera** söker Azure AD efter DNS-posten i domänens DNS-zonfil. Azure AD kan bara verifiera domännamnet efter det att DNS-posterna har spritts. Spridningen tar ofta bara några sekunder, men kan ibland ta en timme eller mer. Om verifieringen inte fungerar första gången så försök igen lite senare.

Om guiden **Lägg till domän** inte är öppen kan du verifiera domänen på [den klassiska Azure-portalen](https://manage.windowsazure.com/):

1. Logga in med ett användarkonto som är en global administratör i Azure AD-katalogen.
2. Öppna katalogen och välj fliken **Domäner**.
3. Markera det domännamn som du vill verifiera och välj **Kontrollera** i kommandofältet.
4. Slutför verifieringen genom att välja **Verifiera** i dialogrutan.

Nu kan du [tilldela användarnamn som innehåller ditt domännamn](active-directory-add-domain-add-users.md).

## <a name="troubleshooting"></a>Felsökning
Om du inte kan verifiera ett anpassat domännamn så pröva med följande. Vi börjar med de vanligaste och arbetar oss vidare till de minst vanliga.

1. **Vänta en timma**. DNS-posterna måste spridas innan Azure AD kan verifiera domänen. Detta kan ta en timma eller mer.
2. **Kontrollera att DNS-posten har angetts och att den är korrekt**. Utför det här steget på webbplatsen för domännamnsregistratorn för domänen. Azure AD kan inte verifiera domännamnet om DNS-posten inte finns i DNS-zonfilen, eller om den inte exakt matchar DNS-posten från Azure AD. Om du inte har den åtkomst som krävs för att uppdatera DNS-poster för domänen hos domännamnsregistratorn så dela DNS-posten med den person eller grupp i din organisation som har den åtkomst som krävs och be dem att lägga till DNS-posten.
3. **Ta bort domännamnet från en annan katalog i Azure AD**. Ett domännamn kan bara verifieras i en enskild katalog. Om ett domännamn tidigare verifierades i en annan katalog måste du ta bort det därifrån innan du kan verifiera det i din nya katalog. Mer information om hur du tar bort domännamn finns i [Hantera anpassade domännamn](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Lägga till fler anpassade domännamn
Om din organisation använder flera anpassade domännamn, t.ex. contoso.com och contosobank.com, kan du lägga till dem. Du kan lägga till upp till 900 domännamn. Lägg till vart och ett av dina domännamn genom att följa samma steg i den här artikeln.

## <a name="next-steps"></a>Nästa steg
* [Tilldela användarnamn som innehåller ditt domännamn](active-directory-add-domain-add-users.md)
* [Hantera egna domännamn](active-directory-add-manage-domain-names.md)
* [Lär dig mer om domänhanteringsbegrepp i Azure AD](active-directory-add-domain-concepts.md)
* [Visa en företagsanpassad sida när användarna loggar in](active-directory-add-company-branding.md)
* [Hantera domännamn i Azure AD med hjälp av PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)


