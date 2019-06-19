---
title: Skapa en klient i Windows Virtual Desktop förhandsgranskning – Azure
description: Beskriver hur du ställer in förhandsversion för virtuella skrivbord i Windows-klienter i Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 3d418d9f18c98e1b6fdf39924ab41dae77fba291
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204757"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Självstudier: Skapa en klient i förhandsversion för virtuella skrivbord i Windows

Skapa en klient i förhandsversion för virtuella skrivbord i Windows är det första steget för att skapa din lösning för fjärrskrivbord. En klient är en grupp med en eller flera värden pooler. Varje värd-pool består av flera session värdar som körs som virtuella datorer i Azure och registrerats hos tjänsten virtuella Windows-skrivbordet. Varje värd-pool består även av en eller flera appgrupper som används för att publicera fjärrprogram för fjärrskrivbord och resurser för användare. Med en klient kan du skapa pooler för värden, skapa app-grupper, tilldela användare och skapa anslutningar via tjänsten.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Bevilja Azure Active Directory-behörigheter till virtuella skrivbord i Windows-tjänsten.
> * Tilldela rollen TenantCreator program till en användare i din Azure Active Directory-klient.
> * Skapa ett virtuellt skrivbord i Windows-klient.

Här är vad du behöver att konfigurera din virtuella skrivbordet i Windows-klient:

* Den [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) klient-ID för Windows virtuellt skrivbord användare.
* Ett globalt administratörskonto i Azure Active Directory-klient.
   * Detta gäller även för organisationer i Cloud Solution Provider (CSP) som skapar ett virtuellt skrivbord i Windows-klient för sina kunder. Om du arbetar i en CSP-organisation, måste du kunna logga in som global administratör för kundens Azure Active Directory-instans.
   * Administratörskontot måste hämtas från Azure Active Directory-klient som du försöker skapa virtuellt skrivbord i Windows-klient. Den här processen har inte stöd för Azure Active Directory B2B (Gäst)-konton.
   * Administratörskontot måste vara ett arbets- eller skolkonto konto.
* En Azure-prenumeration.

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Bevilja Azure Active Directory-behörigheter till tjänsten Windows Virtual Desktop-förhandsversion

Om du redan har beviljat behörigheter till virtuella Windows-skrivbordet för den här instansen av Azure Active Directory, kan du hoppa över det här avsnittet.

Bevilja behörighet till virtuella skrivbord i Windows-tjänsten kan den fråga Azure Active Directory för administrativa och slutanvändarnas aktiviteter.

För att ge Tjänstbehörigheter:

1. Öppna en webbläsare och Anslut till den [virtuella Windows-skrivbordet samtyckessida](https://rdweb.wvd.microsoft.com).
2. För **godkänna alternativet** > **Serverapp**, ange namn för Azure Active Directory-klient eller katalog-ID och välj sedan **skicka**.
        
   ID: T är kundens Microsoft-ID från partnerportalen för Cloud Solution Provider-kunder. För företagskunder, ID: T finns under **Azure Active Directory** > **egenskaper** > **katalog-ID**.
3. Logga in på det virtuella Windows-skrivbordet medgivande sida med ett globalt administratörskonto. Till exempel om du har med Contoso-organisationen ditt konto kanske admin@contoso.com eller admin@contoso.onmicrosoft.com.  
4. Välj **Acceptera**.
5. Vänta en minut.
6. Gå tillbaka till den [virtuella Windows-skrivbordet samtyckessida](https://rdweb.wvd.microsoft.com).
7. Gå till **godkänna alternativet** > **Klientappen**, anger du samma Azure Active Directory-klientnamn eller katalog-ID och välj sedan **skicka**.
8. Logga in på virtuella Windows-skrivbordet samtyckessida som global administratör, precis som i steg 3.
9. Välj **Acceptera**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Tilldela rollen TenantCreator program till en användare i din Azure Active Directory-klient

Programroll TenantCreator kan genom att tilldela en Azure Active Directory-användare användaren skapa ett virtuellt skrivbord i Windows-klient som är associerade med Azure Active Directory-instans. Du behöver använda ditt globala administratörskonto för att tilldela rollen TenantCreator.

Tilldela rollen TenantCreator program:

1. Öppna en webbläsare och Anslut till den [Azure-portalen](https://portal.azure.com) med ditt globala administratörskonto.
   
   Om du arbetar med flera olika Azure Active Directory-klienter, är det en bra idé att öppna en privat webbläsarsession och kopiera och klistra in URL: er i adressfältet.
2. I sökfältet i Azure-portalen, söker du efter **företagsprogram** och välj den post som visas under den **Services** kategori.
3. Inom **företagsprogram**, Sök efter **virtuella Windows-skrivbordet**. Du ser de två programmen som du tillhandahöll medgivande för i föregående avsnitt. Dessa två appar väljer du **virtuella Windows-skrivbordet**.
   ![En skärmbild av sökresultaten när du söker efter ”Windows virtuellt skrivbord” i ”Enterprise-program”. Appen med namnet ”Windows virtuellt skrivbord” är markerad.](media/tenant-enterprise-app.png)
4. Välj **Användare och grupper**. Du kan se att den administratör som godkänts i programmet redan visas med den **standard åtkomst** tilldelats rollen. Detta är inte tillräckligt för att skapa ett virtuellt skrivbord i Windows-klient. Fortsätt att följa dessa instruktioner för att lägga till den **TenantCreator** roll till en användare.
   ![En skärmbild av användare och grupper som tilldelats hantera företagsprogram ”Windows virtuella skrivbordet”. Skärmbilden visar endast en tilldelning för ”standardåtkomst”.](media/tenant-default-access.png)
5. Välj **Lägg till användare**, och välj sedan **användare och grupper** i den **Lägg till tilldelning** bladet.
6. Sök efter ett användarkonto som kommer att skapa din virtuella skrivbordet i Windows-klient. Detta kan vara kontot som global administratör för enkelhetens skull.

   ![En skärmbild av en användare att lägga till som ”TenantCreator”.](media/tenant-assign-user.png)

   > [!NOTE]
   > Du måste välja en användare (eller en grupp som innehåller en användare) som kommer från den här Azure Active Directory-instansen. Du kan inte välja en gästanvändare (B2B) eller ett huvudnamn för tjänsten.

7. Välj användarkontot, Välj den **Välj** och välj sedan **tilldela**.
8. På den **Windows virtuellt skrivbord - användare och grupper** kontrollerar du att du ser en ny post med den **TenantCreator** rolltilldelningen för den användare som skapar virtuellt skrivbord i Windows-klient.
   ![En skärmbild av användare och grupper som tilldelats hantera företagsprogram ”Windows virtuella skrivbordet”. Skärmbilden innehåller nu en andra posten för en användare som tilldelats rollen ”TenantCreator”.](media/tenant-tenant-creator-added.png)

Innan du fortsätter på att skapa virtuellt skrivbord i Windows-klient, behöver du två typer av information:
- Azure Active Directory-klient-ID (eller **katalog-ID**)
- Ditt Azure-prenumerations-ID

Du hittar ditt Azure Active Directory-klient-ID (eller **katalog-ID**):
1. I samma Azure portal-session, söker du efter **Azure Active Directory** i sökfältet och välj den post som visas under den **Services** kategori.
   ![En skärmbild av sökresultatet för ”Azure Active Directory” i Azure-portalen. Sökresultatet under ”tjänster” är markerad.](media/tenant-search-azure-active-directory.png)
2. Rulla nedåt tills du hittar **egenskaper**, och markera den.
3. Leta efter **katalog-ID**, och välj sedan ikonen för Urklipp. Klistra in den på en praktisk plats så att du kan använda den senare som den **AadTenantId** värde.
   ![En skärmbild av Azure Active Directory-egenskaperna. Musen hovrar över ikonen för ”katalog-ID” kopiera och klistra in Urklipp.](media/tenant-directory-id.png)

Hitta din Azure-prenumerations-ID:
1. I samma Azure portal-session, söker du efter **prenumerationer** i sökfältet och välj den post som visas under den **Services** kategori.
   ![En skärmbild av sökresultatet för ”Azure Active Directory” i Azure-portalen. Sökresultatet under ”tjänster” är markerad.](media/tenant-search-subscription.png)
2. Ange den prenumeration du vill använda för att ta emot tjänstmeddelanden för virtuella Windows-skrivbordet.
3. Leta efter **prenumerations-ID**, och sedan hovra över värdet tills en ikon för Urklipp visas. Välj ikonen Urklipp och klistra in den på en praktisk plats så att du kan använda den senare som den **AzureSubscriptionId** värde.
   ![En skärmbild av egenskaper för Azure-prenumeration. Musen hovrar över ikonen för ”prenumerations-ID” kopiera och klistra in Urklipp.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Skapa en förhandsversion för virtuella skrivbord i Windows-klient

Nu när du har beviljats behörigheter för virtuellt skrivbord i Windows-tjänsten frågar Azure Active Directory och har tilldelats rollen TenantCreator till ett användarkonto kan skapa du ett virtuellt skrivbord i Windows-klient.

Först [hämta och importera modulen Windows virtuellt skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

Logga in på Windows virtuella skrivbord med hjälp av TenantCreator-användarkonto med denna cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Därefter skapar du en ny virtuellt skrivbord i Windows-klient som är associerade med Azure Active Directory-klient:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Ersätt inom hakparenteser värden med värden som är relevanta för din organisation och klient. Det namn du väljer för din nya virtuella skrivbordet i Windows-klient ska vara globalt unikt. Anta exempelvis att du är Windows Virtual Desktop TenantCreator för Contoso-organisationen. Kör du cmdlet: en ut så här:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Nästa steg

När du har skapat din klient, måste du skapa ett tjänstobjekt i Azure Active Directory och tilldela den till en roll i virtuella Windows-skrivbordet. Tjänstens huvudnamn kan du ska kunna distribuera Windows Virtual Desktop Azure Marketplace erbjuder att skapa en pool med värden. Om du vill veta mer om värden pooler kan du fortsätta till självstudien för att skapa en pool med värden i virtuella Windows-skrivbordet.

> [!div class="nextstepaction"]
> [Skapa tjänstens huvudnamn och rolltilldelningar med PowerShell](./create-service-principal-role-powershell.md)
