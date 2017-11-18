---
title: "Lägg till en anpassad domän till Azure AD | Microsoft Docs"
description: "Beskriver hur du lägger till en anpassad domän i Azure Active Directory."
services: active-directory
author: curtand
manager: michael.tillman
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 27e7449f039da8f7661d113999e1c4e5d76c3cf6
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>Snabbstart: Lägga till ett anpassat domännamn i Azure Active Directory

Varje Azure AD-katalog som medföljer ett första domännamn i form av *domainname*. onmicrosoft.com. Det ursprungliga domännamnet inte kan ändras eller tas bort, men du kan lägga till företagets domännamn till Azure AD samt. Din organisation har till exempel förmodligen andra domännamn som används för att göra affärs- och användare som loggar in med företagets domännamn. Lägga till anpassade domännamn i Azure AD kan du tilldela användarnamn i katalogen som dina användare som ”alice@contoso.com”. i stället för ”alice @*domännamn*. onmicrosoft.com'. Processen är enkel:

1. Lägga till det anpassade domännamnet i din katalog
2. Lägga till en DNS-post för domännamnet hos domännamnsregistratorn
3. Verifiera det anpassade domännamnet i Azure AD

## <a name="add-the-custom-domain-name-to-your-directory"></a>Lägga till det anpassade domännamnet i din katalog
1. Logga in på den [Azure-portalen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är en global administratör för katalogen.
2. Till vänster, Välj **anpassade domännamn**.
3. Välj **Lägg till anpassad domän**.
   
   ![Välj kommandot Lägg till](./media/add-custom-domain/add-custom-domain.png)
5. På **anpassade domännamn**, ange namnet på domänen i rutan, till exempel ”contoso.com” och väljer sedan **Lägg till domän**. Glöm inte att ta med .com, .net eller ett annat tillägg för toppnivån.
6. På ***domainname*** (ditt nya domännamn är rubrik), samla in information för DNS-posten använda senare för att verifiera det anpassade domännamnet i Azure AD.
   
   ![Hämta information om DNS-post](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> Om du planerar att federera dina lokala Windows Server AD med Azure AD, så du måste välja den **jag planerar att konfigurera den här domänen för enkel inloggning med min lokala Active Directory** kryssrutan när du kör verktyget Azure AD Connect att synkronisera dina kataloger. Du måste också registrera samma domännamn du väljer för federering med din lokala katalog i den **Azure AD Domain** steg i guiden. Du kan se hur det steget i guiden ser ut [i dessa anvisningar](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Om du inte har verktyget Azure AD Connect kan du [ladda ned det här](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>Lägga till en DNS-post för domännamnet hos domännamnsregistratorn
Nästa steg mot att kunna använda ditt anpassade domännamn med Azure AD är att du uppdaterar domänens DNS-zonfil. Azure AD kan kontrollera att din organisation äger det anpassade domännamnet. Du kan använda [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) för Azure/Office 365/externa DNS-poster i Azure, eller lägga till DNS-posten på [en annan DNS-registratorns](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Logga in hos domännamnsregistratorn för domänen. Om du inte har den åtkomst som krävs för att uppdatera DNS-posten så be den person eller grupp i din organisation som har denna åtkomst att utföra steg 2 och att meddela dig när det är klart.
2. Uppdatera DNS-zonfilen för domänen genom att lägga till DNS-posten som du fått från Azure AD. DNS-posten ändrar inga beteenden, till exempel e-postdirigering eller webbhosting.

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>Verifiera det anpassade domännamnet i Azure AD
När du har lagt till DNS-posten är du redo att verifiera domännamnet med Azure AD. Ett domännamn kan verifieras endast när DNS-poster har spridits. Spridningen tar ofta bara några sekunder, men kan ibland ta en timme eller mer. Om verifieringen inte fungerar första gången försöker du igen lite senare.

1. Logga in på [Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är en global administratör för klienten.
2. Välj **anpassade domännamn**.
3. Välj det overifierade domännamn som du vill kontrollera.
4. Kontrollera värdena och välj **Kontrollera** Slutför verifieringen.

Nu kan du [tilldela användarnamn som innehåller ditt domännamn](active-directory-users-create-azure-portal.md). Du kan skapa molnbaserade användarkonton eller uppdateringen tidigare synkroniserade lokalt information om användarkonton med hjälp av ditt domännamn. Du kan också ändra synkroniserade användarkonto domän suffix information med [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) eller [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

> [!TIP]
> Du kan lägga till upp till 900 hanterade domännamn. Om du konfigurerar alla dina domäner för federation lokal med Active Directory, kan du lägga till till högst 450 domännamn i varje katalog. Mer information finns i [federerat och hanterade domännamn](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="troubleshooting"></a>Felsökning
Om du inte kan verifiera ett eget domännamn kan du prova följande felsökningssteg:

1. **Vänta en timma**. DNS-poster måste spridas innan Azure AD kan verifiera domänen. Den här processen kan ta minst en timme.
2. **Kontrollera att DNS-posten har angetts och att den är korrekt**. Utför det här steget på webbplatsen för domännamnsregistratorn för domänen. Azure AD kan inte verifiera domännamnet om 
  * DNS-posten finns inte i DNS-zonfilen
  * Det är inte en exakt matchning med DNS-posten som Azure AD tillhandahåller. 
  
  Om du inte har den åtkomst som krävs för att uppdatera DNS-poster för domänen hos domännamnsregistratorn så dela DNS-posten med den person eller grupp i din organisation som har den åtkomst som krävs och be dem att lägga till DNS-posten.
3. **Ta bort domännamnet från en annan katalog i Azure AD**. Ett domännamn kan bara verifieras i en enskild katalog. Om ett domännamn för närvarande har verifierats i en annan katalog kan det inte verifieras i den nya katalogen tills det tas bort på den andra. Mer information om hur du tar bort domännamn finns i [Hantera anpassade domännamn](active-directory-domains-manage-azure-portal.md).    

Upprepa stegen i den här artikeln för att lägga till var och en av dina domännamn.

## <a name="learn-more"></a>Läs mer
[Översikt över anpassade domännamn i Azure AD](active-directory-domains-manage-azure-portal.md)

[Hantera egna domännamn](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du lägger till en anpassad domän till Azure AD i denna Snabbstart. 

Du kan använda följande länk för att lägga till en ny anpassad domän i Azure AD från Azure-portalen.

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 