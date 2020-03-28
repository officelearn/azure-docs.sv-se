---
title: Skapa en klient i Windows Virtual Desktop - Azure
description: I artikeln beskrivs hur du konfigurerar Windows Virtual Desktop-klienter i Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9f9f7f709d31967e892900ccb25657a5963c0379
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370227"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>Självstudiekurs: Skapa en klient i Windows Virtual Desktop

Att skapa en klient i Windows Virtual Desktop är det första steget mot att bygga en virtuell lösning för skrivbordet. En klient är en grupp med en eller flera värdpooler. Varje värdpool består av flera sessionsvärdar som körs som virtuella datorer i Azure och är registrerade i Windows Virtual Desktop-tjänsten. Varje värdpool består också av en eller flera appgrupper som används för att publicera fjärrskrivbords- och fjärrprogramresurser för användare. Med en klient kan du skapa värdpooler, skapa appgrupper, tilldela användare och upprätta anslutningar via tjänsten.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Bevilja Azure Active Directory-behörigheter till Tjänsten Windows Virtual Desktop.
> * Tilldela rollen TenantCreator-program till en användare i din Azure Active Directory-klientorganisation.
> * Skapa en Windows Virtual Desktop-klient.

## <a name="what-you-need-to-set-up-a-tenant"></a>Vad du behöver för att konfigurera en klient

Innan du börjar konfigurera windows virtual desktop-klienten kontrollerar du att du har följande saker:

* Azure Active Directory-klient-ID för Windows Virtual Desktop-användare. [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
* Ett globalt administratörskonto i Azure Active Directory-klienten.
   * Detta gäller även CSP-organisationer (Cloud Solution Provider) som skapar en Windows Virtual Desktop-klient för sina kunder. Om du är i en CSP-organisation måste du kunna logga in som global administratör för kundens Azure Active Directory-instans.
   * Administratörskontot måste hämtas från Azure Active Directory-klienten där du försöker skapa Windows Virtual Desktop-klienten. Den här processen stöder inte Azure Active Directory B2B-konton (gäst).
   * Administratörskontot måste vara ett arbets- eller skolkonto.
* En Azure-prenumeration.

Du måste ha klient-ID, globalt administratörskonto och Azure-prenumeration redo så att processen som beskrivs i den här självstudien kan fungera korrekt.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Bevilja behörigheter till Windows Virtual Desktop

Om du redan har beviljat behörigheter till Windows Virtual Desktop för den här Azure Active Directory-instansen hoppar du över det här avsnittet.

Genom att bevilja behörigheter till Tjänsten Windows Virtual Desktop kan den fråga Azure Active Directory för administrativa uppgifter och slutanvändare.

Så här beviljar du tjänstbehörigheterna:

1. Öppna en webbläsare och påbörja administratörssamtyckeflödet till [serverappen för Windows Virtual Desktop](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Om du hanterar en kund och behöver ge administratörsgodkännande för kundens katalog anger du följande URL i webbläsaren och ersätter {tenant} med Azure AD-domännamnet för kunden. Om kundens organisation till exempel har registrerat Azure AD-domännamnet för contoso.onmicrosoft.com ersätter du {tenant} med contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Logga in på medgivandesidan för Windows Virtual Desktop med ett globalt administratörskonto. Om du till exempel var med i Contoso-organisationen kan ditt konto vara admin@contoso.com eller admin@contoso.onmicrosoft.com.
3. Välj **Acceptera**.
4. Vänta i en minut så att Azure AD kan spela in medgivande.
5. Öppna en webbläsare och påbörja administratörssamtyckeflödet till [klientappen för Windows Virtual Desktop](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Om du hanterar en kund och behöver ge administratörsgodkännande för kundens katalog anger du följande URL i webbläsaren och ersätter {tenant} med Azure AD-domännamnet för kunden. Om kundens organisation till exempel har registrerat Azure AD-domännamnet för contoso.onmicrosoft.com ersätter du {tenant} med contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Logga in på samtyckessidan för Windows Virtual Desktop som global administratör, som du gjorde i steg 2.
7. Välj **Acceptera**.

## <a name="assign-the-tenantcreator-application-role"></a>Tilldela programrollen Klientcreator

Genom att tilldela en Azure Active Directory-användare rollen TenantCreator-program kan användaren skapa en Windows Virtual Desktop-klient som är associerad med Azure Active Directory-instansen. Du måste använda ditt globala administratörskonto för att tilldela rollen TenantCreator.

Så här tilldelar du programrollen TenantCreator:

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera programrollen TenantCreator. Sök efter och välj **Företagsprogram**. Om du arbetar med flera Azure Active Directory-klienter är det bäst att öppna en privat webbläsarsession och kopiera och klistra in webbadresserna i adressfältet.

   ![Skärmbild av sökning efter Enterprise-program i Azure-portalen](media/azure-portal-enterprise-applications.png)
2. Sök efter Windows **Virtual Desktop**i **Företagsprogram**. Du ser de två program som du har gett medgivande för i föregående avsnitt. Av dessa två appar väljer du **Windows Virtual Desktop**.
   
   ![En skärmbild av sökresultaten när du söker efter "Windows Virtual Desktop" i "Enterprise-program". Appen "Windows Virtual Desktop" är markerad.](media/tenant-enterprise-app.png)
3. Välj **Användare och grupper**. Du kan se att administratören som beviljade samtycke till programmet redan finns med rollen **Standardåtkomst** tilldelad. Detta räcker inte för att skapa en Windows Virtual Desktop-klient. Fortsätt att följa dessa instruktioner för att lägga till **rollen Klientcreator** till en användare.
   
   ![En skärmbild av de användare och grupper som har tilldelats för att hantera företagsprogrammet "Windows Virtual Desktop". Skärmbilden visar bara en tilldelning, som är för "Standardåtkomst".](media/tenant-default-access.png)
4. Välj **Lägg till användare**och välj sedan Användare och **grupper** på fliken Lägg **till tilldelning.**
5. Sök efter ett användarkonto som skapar din Windows Virtual Desktop-klient. För enkelhetens skull kan detta vara det globala administratörskontot.
   - Om du använder en Microsoft contosoadmin@live.com Identity contosoadmin@outlook.comProvider som eller så kanske du inte kan logga in på Windows Virtual Desktop. Vi rekommenderar att du använder admin@contoso.com admin@contoso.onmicrosoft.com ett domänspecifikt konto som eller i stället.

   ![En skärmbild av att välja en användare att lägga till som "TenantCreator".](media/tenant-assign-user.png)
   > [!NOTE]
   > Du måste välja en användare (eller en grupp som innehåller en användare) som kommer från den här Azure Active Directory-instansen. Du kan inte välja en gästanvändare (B2B) eller ett huvudnamn för tjänsten.

6. Markera användarkontot, välj knappen **Välj** och välj sedan **Tilldela**.
7. På sidan **Windows Virtual Desktop - Användare och grupper** kontrollerar du att du ser en ny post med rollen **Klientcreator** som tilldelats den användare som ska skapa windows virtuella skrivbordsklientinnehavaren.

   ![En skärmbild av de användare och grupper som har tilldelats för att hantera företagsprogrammet "Windows Virtual Desktop". Skärmbilden innehåller nu en andra post för en användare som tilldelats rollen "TenantCreator".](media/tenant-tenant-creator-added.png)

Innan du fortsätter med att skapa din Windows Virtual Desktop-klient behöver du två informationsdelar:

   - Ditt Azure Active Directory-klient-ID (eller **katalog-ID)**
   - Ditt Azure-prenumerations-ID

Så här hittar du ditt Azure Active Directory-klient-ID (eller **katalog-ID):**
1. I samma [Azure-portalsession](https://portal.azure.com) söker du efter och väljer **Azure Active Directory**.

   ![En skärmbild av sökresultaten för "Azure Active Directory" i Azure-portalen. Sökresultatet under "Tjänster" markeras.](media/tenant-search-azure-active-directory.png)
2. Bläddra nedåt tills du hittar **Egenskaper**och markera den sedan.
3. Leta efter **katalog-ID**och välj sedan ikonen urklipp. Klistra in den på en praktisk plats så att du kan använda den senare som **AadTenantId-värdet.**

   ![En skärmbild av Azure Active Directory-egenskaperna. Musen håller muspekaren över Urklippsikonen för att kopiera och klistra in "Katalog-ID".](media/tenant-directory-id.png)

Så här hittar du ditt Azure-prenumerations-ID:
1. I samma [Azure-portalsession](https://portal.azure.com) söker du efter och väljer **Prenumerationer**.
   
   ![En skärmbild av sökresultaten för "Azure Active Directory" i Azure-portalen. Sökresultatet under "Tjänster" markeras.](media/tenant-search-subscription.png)
2. Välj den Azure-prenumeration som du vill använda för att ta emot meddelanden om Windows Virtual Desktop-tjänst.
3. Leta efter **prenumerations-ID**och hovra sedan över värdet tills en urklippsikon visas. Välj ikonen urklipp och klistra in den på en praktisk plats så att du kan använda den senare som **AzureSubscriptionId-värde.**
   
   ![En skärmbild av Azure-prenumerationsegenskaperna. Musen håller muspekaren över urklippsikonen för att kopiera och klistra in prenumerations-ID.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Skapa en Windows Virtual Desktop-klientorganisation

Nu när du har beviljat windows virtual desktop-tjänsten behörigheter att fråga Azure Active Directory och tilldelat rollen TenantCreator till ett användarkonto, kan du skapa en Windows Virtual Desktop-klient.

Hämta och importera först [Windows Virtual Desktop-modulen](/powershell/windows-virtual-desktop/overview/) som du kan använda i PowerShell-sessionen om du inte redan har gjort det.

Logga in på Windows Virtual Desktop med hjälp av klientcreatoranvändarkontot med den här cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Därefter skapar du en ny Windows Virtual Desktop-klient som är associerad med Azure Active Directory-klienten:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Ersätt de parenteserade värdena med värden som är relevanta för din organisation och klient. Namnet du väljer för din nya Windows Virtual Desktop-klient bör vara globalt unikt. Anta till exempel att du är Windows Virtual Desktop TenantCreator för Contoso-organisationen. Den cmdlet du skulle köra skulle se ut så här:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Det är en bra idé att tilldela administrativ åtkomst till en andra användare om du någonsin hamnar utelåst från ditt konto, eller om du åker på semester och behöver någon som fungerar som klientadministratör i din frånvaro. Om du vill tilldela administratörsåtkomst till en `<TenantName>` andra `<Upn>` användare kör du följande cmdlet med och ersätts med klientnamnet och den andra användarens UPN.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Nästa steg

När du har skapat din klientorganisation måste du skapa ett tjänsthuvudnamn i Azure Active Directory och tilldela den en roll i Windows Virtual Desktop. Tjänsthuvudstaden gör att du kan distribuera Azure Marketplace-erbjudandet för Windows Virtual Desktop Marketplace för att skapa en värdpool. Om du vill veta mer om värdpooler fortsätter du till självstudien för att skapa en värdpool i Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Skapa tjänstens huvudnamn och rolltilldelningar med PowerShell](./create-service-principal-role-powershell.md)
