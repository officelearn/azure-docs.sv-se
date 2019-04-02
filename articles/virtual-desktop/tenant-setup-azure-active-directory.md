---
title: Skapa en klient i Windows Virtual Desktop förhandsgranskning – Azure
description: Beskriver hur du ställer in förhandsversion för virtuella skrivbord i Windows-klienter i Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 1c66b3de9e18cb74c43f20499e4065c7ec7ae5ca
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801689"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Självstudier: Skapa en klient i förhandsversion för virtuella skrivbord i Windows

Skapa en klient i förhandsversion för virtuella skrivbord i Windows är det första steget mot att bygga ut din skrivbordsvirtualisering-lösning. En klient är en grupp med en eller flera värden pooler. Varje värd-pool består av flera session värdar som körs som virtuella datorer i Azure och registrerats hos tjänsten virtuella Windows-skrivbordet. Varje värd-pool består även av en eller flera appgrupper som används för att publicera fjärrprogram för fjärrskrivbord och resurser för användare. Med en klient kan du bygga ut värden pooler, skapa app-grupper, tilldela användare och skapa anslutningar via tjänsten.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Bevilja Azure Active Directory-behörigheter till virtuella skrivbord i Windows-tjänsten.
> * Tilldela rollen TenantCreator program till en användare i din Azure Active Directory-klient.
> * Skapa ett virtuellt skrivbord i Windows-klient.

Här är vad du behöver att konfigurera din virtuella skrivbordet i Windows-klient:

* Den [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) klient-ID för Windows virtuellt skrivbord användare.
* Ett globalt administratörskonto i Azure Active Directory-klient.
   * Detta gäller även för Cloud Solution Provider (CSP) organisationer som skapar ett virtuellt skrivbord i Windows-klient för sina kunder. Om du är en CSP-organisation, måste du kunna logga in som global administratör för kundens Azure Active Directory.
   * Administratörskontot måste hämtas från Azure Active Directory-klient som du försöker skapa virtuellt skrivbord i Windows-klient. Den här processen har inte stöd för Azure Active Directory B2B (Gäst)-konton.
   * Administratörskontot måste vara ett arbets- eller skolkonto konto.
* En Azure-prenumerations-ID

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Bevilja Azure Active Directory-behörigheter till tjänsten Windows Virtual Desktop-förhandsversion

Om du redan har beviljat behörigheter till virtuella Windows-skrivbordet för den här Azure Active Directory, kan du hoppa över det här avsnittet.

Bevilja behörigheter till virtuella skrivbord i Windows-tjänsten kan den fråga efter Azure Active Directory för administrativa och slutanvändarnas aktiviteter.

För att ge Tjänstbehörigheter:

1. Öppna en webbläsare och Anslut till den [virtuella Windows-skrivbordet samtyckessida](https://rdweb.wvd.microsoft.com).
2. För **godkänna alternativet** > **Serverapp**, ange namn för Azure Active Directory-klient eller katalog-ID och välj sedan **skicka**.
        ID: T är kundens Microsoft-ID från partnerportalen för Cloud Solution Provider-kunder. För företagskunder, ID: T finns under **Azure Active Directory** > **egenskaper** > **katalog-ID**.
3. Logga in på det virtuella Windows-skrivbordet medgivande sida med ett globalt administratörskonto. Till exempel om du har med Contoso-organisationen ditt konto kanske admin@contoso.com eller admin@contoso.onmicrosoft.com.  
4. Välj **Acceptera**.
5. Vänta en minut.
6. Gå tillbaka till den [virtuella Windows-skrivbordet samtyckessida](https://rdweb.wvd.microsoft.com).
7. Gå till **godkänna alternativet** > **Klientappen**, anger du samma Azure AD-klientnamn eller katalog-ID och välj sedan **skicka**.
8. Logga in på virtuella Windows-skrivbordet samtyckessida som global administratör som du gjorde i steg 3.
9. Välj **Acceptera**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Tilldela rollen TenantCreator program till en användare i din Azure Active Directory-klient

Programroll TenantCreator kan genom att tilldela en Azure Active Directory-användare användaren skapa ett virtuellt skrivbord i Windows-klient som är associerade med Azure Active Directory. Du behöver använda ditt globala administratörskonto för att tilldela rollen TenantCreator.

Tilldela rollen TenantCreator program med ditt globala administratörskonto:

1. Öppna en webbläsare och Anslut till den [Azure Active Directory-portalen](https://aad.portal.azure.com) med ditt globala administratörskonto.
   - Om du arbetar med flera Azure AD-klienter, är det bäst att öppna en privat webbläsarsession och kopiera och klistra in URL: er i adressfältet.
2. Välj **företagsprogram**, Sök efter **virtuella Windows-skrivbordet**. Du ser de två programmen som du har angett ditt medgivande för i föregående avsnitt. Dessa två appar väljer du **virtuella Windows-skrivbordet**.
3. Välj **användare och grupper**och välj sedan **Lägg till användare**.
4. Välj användare och grupper i den **Lägg till tilldelning** bladet
5. Sök efter ett användarkonto som kommer att skapa din virtuella skrivbordet i Windows-klient.
   - Detta kan vara kontot som global administratör för enkelhetens skull.
6. Välj användarkontot, klickar du på den **Välj** och välj sedan **tilldela**.

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Skapa en förhandsversion för virtuella skrivbord i Windows-klient

Nu när du har beviljats behörigheter för virtuellt skrivbord i Windows-tjänsten att fråga efter Azure Active Directory och har tilldelats rollen TenantCreator till ett användarkonto kan skapa du ett virtuellt skrivbord i Windows-klient.

Först [hämta och importera modulen Windows virtuellt skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

Logga in på Windows virtuella skrivbord som använder användarkontot TenantCreator med denna cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Därefter skapar du en ny virtuellt skrivbord i Windows-klient som är associerade med Azure Active Directory-klient:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Inom hakparenteser värdena ska ersättas med värden som är relevanta för din organisation och klient. Anta exempelvis att du är Windows Virtual Desktop TenantCreator för Contoso-organisationen. Kör du cmdlet: en ut så här:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Nästa steg

När du har skapat din klient, måste du göra en värd-pool. Om du vill veta mer om värden pooler kan du fortsätta till självstudien för att skapa en pool med värden i virtuella Windows-skrivbordet.

> [!div class="nextstepaction"]
> [Virtuella Windows-skrivbordet värd pool självstudien](./create-host-pools-azure-marketplace.md)
