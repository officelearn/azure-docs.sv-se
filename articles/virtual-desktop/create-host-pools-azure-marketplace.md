---
title: Windows-värd pool för virtuella skriv bord Azure Marketplace – Azure
description: Hur du skapar en Windows-pool för virtuella skriv bord med hjälp av Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: helohr
ms.openlocfilehash: 25dd4810cf8cccab8bcbf211da4f6abbcd147056
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020037"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Självstudie: skapa en värdbaserad pool med Azure Marketplace

Värdbaserade pooler är en samling av en eller flera identiska virtuella datorer i Windows-miljöer för virtuella Skriv bords klienter. Varje adresspool kan innehålla en app-grupp som användare kan interagera med på samma sätt som på ett fysiskt skriv bord.

I den här självstudien beskrivs hur du skapar en adresspool i en Windows-klient för virtuella skriv bord med hjälp av ett Microsoft Azure Marketplace erbjudande. Uppgifterna omfattar:

> [!div class="checklist"]
> * Skapa en adresspool i det virtuella Windows-skrivbordet.
> * Skapa en resurs grupp med virtuella datorer i en Azure-prenumeration.
> * Anslut de virtuella datorerna till Active Directory-domänen.
> * Registrera de virtuella datorerna med det virtuella Windows-skrivbordet.

Innan du börjar kan du [Hämta och importera Windows Virtual Desktop PowerShell-modulen](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) som ska användas i PowerShell-sessionen om du inte redan gjort det. Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Kör Azure Marketplace-erbjudandet för att etablera en ny värdbaserad pool

Så här kör du Azure Marketplace-erbjudandet för att etablera en ny adresspool:

1. På Azure Portal-menyn eller på **Start** sidan väljer du **skapa en resurs**.
2. Ange **virtuellt Windows-skrivbord** i fönstret Marketplace search.
3. Välj **Windows Virtual Desktop – etablera en adresspool**och välj sedan **skapa**.

Efter det följer du anvisningarna i nästa avsnitt för att ange informationen för lämpliga blad.

### <a name="basics"></a>Grundläggande inställningar

Det här kan du göra på bladet **grundläggande** :

1. Ange ett namn för den värdbaserade poolen som är unik i Windows-klienten för virtuella skriv bord.
2. Välj lämpligt alternativ för ett personligt skriv bord. Om du väljer **Ja**, kommer varje användare som ansluter till den här poolen permanent att tilldelas till en virtuell dator.
3. Ange en kommaavgränsad lista med användare som kan logga in på de virtuella Windows-klienterna och få åtkomst till en stationär dator när Azure Marketplace-erbjudandet har slutförts. Om du till exempel vill tilldela user1@contoso.com och user2@contoso.com åtkomst anger duuser1@contoso.comuser2@contoso.com.
4. Välj **Skapa nytt** och ange ett namn för den nya resurs gruppen.
5. För **plats**väljer du samma plats som det virtuella nätverk som har anslutning till Active Directory-servern.
6. Välj **Nästa: Konfigurera virtuella datorer >** .

>[!IMPORTANT]
>Om du använder en ren Azure Active Directory Domain Services-och Azure Active Directory-lösning, se till att distribuera din värddator i samma region som din Azure Active Directory Domain Services för att undvika fel i domän anslutning och autentiseringsuppgifter.

### <a name="configure-virtual-machines"></a>Konfigurera virtuella datorer

För bladet **Konfigurera virtuella datorer** :

1. Acceptera standardvärdena eller anpassa antalet och storleken på de virtuella datorerna.
2. Ange ett prefix för namnen på de virtuella datorerna. Om du till exempel anger namnet "prefix" kommer de virtuella datorerna att kallas "prefix-0," prefix-1, "och så vidare.
3. Välj **Nästa: inställningar för virtuella datorer**.

### <a name="virtual-machine-settings"></a>Inställningar för virtuella datorer

För bladet **Inställningar för virtuell dator** :

>[!NOTE]
> Om du ansluter dina virtuella datorer till en Azure Active Directory Domain Services (Azure AD DS)-miljö måste du se till att din domän anslutning är medlem i [Administratörs gruppen för AAD-domänkontrollanten](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
>
> Kontot måste också ingå i den Azure AD DS-hanterade domänen eller Azure AD-klient-konton från externa kataloger som är associerade med din Azure AD-klient kan inte autentiseras korrekt under processen för domän anslutning. 

1. För **avbildnings källa**väljer du källan och anger den information som krävs för att hitta den och hur den ska lagras. Om du väljer att inte använda hanterade diskar väljer du det lagrings konto som innehåller VHD-filen.
2. Ange User Principal Name och lösen ord för det domän konto som ska ansluta till de virtuella datorerna till Active Directorys domänen. Samma användar namn och lösen ord kommer att skapas på de virtuella datorerna som ett lokalt konto. Du kan återställa de här lokala kontona senare.
3. Välj det virtuella nätverk som har anslutning till Active Directory-servern och välj sedan ett undernät som är värd för de virtuella datorerna.
4. Välj **Nästa: information om virtuella Windows-datorer**.

### <a name="windows-virtual-desktop-tenant-information"></a>Klient information för virtuella Windows-datorer

För bladet **information om virtuella Windows-datorer med virtuella skriv bord** :

1. Ange namnet på klient gruppen som innehåller din klient organisation för **Windows Virtual Desktop klient grupp namn**. Lämna det som standard om du inte angav ett angivet klient grupps namn.
2. Ange namnet på den klient där du vill skapa den här poolen för **Windows Virtual Desktop klient namn**.
3. Ange vilken typ av autentiseringsuppgifter du vill använda för att autentisera som Windows-ägare för klient organisation för virtuella skriv bord. Om du har slutfört [guiden skapa tjänst huvud namn och roll tilldelningar med PowerShell](./create-service-principal-role-powershell.md)väljer du **tjänstens huvud namn**. När **Azure AD-klient-ID** visas anger du ID: t för Azure Active Directory-instansen som innehåller tjänstens huvud namn.
4. Ange autentiseringsuppgifterna för klient organisationens administratörs konto. Det finns bara stöd för tjänst huvud namn med autentiseringsuppgifter för lösen ord.
5. Välj **Nästa: granska + skapa**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Slutför installationen och skapa den virtuella datorn

För de två sista bladen:

1. Granska installations informationen på bladet **Granska och skapa** . Om du behöver ändra något går du tillbaka till lämpligt blad och gör din ändring innan du fortsätter. Om informationen ser bra ut väljer du **OK**.
2. Välj **skapa** för att distribuera din värd-pool.

Beroende på hur många virtuella datorer du skapar kan den här processen ta 30 minuter eller mer att slutföra.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Valfritt Tilldela ytterligare användare till program gruppen Skriv bord

När Azure Marketplace-erbjudandet har slutförts kan du tilldela fler användare till gruppen Skriv bords program innan du börjar testa alla Station ära datorer på dina virtuella datorer. Om du redan har lagt till standard användare i Azure Marketplace-erbjudandet och inte vill lägga till fler kan du hoppa över det här avsnittet.

Om du vill tilldela användare till program gruppen Skriv bord måste du först öppna ett PowerShell-fönster. Efter det måste du ange följande två cmdletar.

Kör följande cmdlet för att logga in på Windows-miljön för virtuella skriv bord:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Lägg till användare i program gruppen Skriv bord med hjälp av denna cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Användarens UPN ska matcha användarens identitet i Azure Active Directory (till exempel user1@contoso.com). Om du vill lägga till flera användare måste du köra denna cmdlet för varje användare.

När du har slutfört de här stegen kan användare som har lagts till i gruppen Skriv bord logga in på Windows Virtual Desktop med stöd för fjärr skrivbords klienter och se en resurs för en session.

Här är de klienter som stöds:

- [Fjärr skrivbords klient för Windows 7 och Windows 10](connect-windows-7-and-10.md)
- [Webb klient för Windows Virtual Desktop](connect-web.md)

>[!IMPORTANT]
>Vi rekommenderar att du inte öppnar den inkommande port 3389 på dina virtuella datorer för att skydda din Windows-miljö för virtuella skriv bord i Azure. Virtuella Windows-datorer kräver inte en öppen inkommande port 3389 för att användare ska kunna komma åt värddatorns virtuella datorer. Om du måste öppna port 3389 för fel söknings syfte rekommenderar vi att du använder [just-in-Time VM-åtkomst](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en adresspool och tilldelat användare åtkomst till Skriv bordet kan du fylla i din värddator med RemoteApp-program. Mer information om hur du hanterar appar i virtuella Windows-datorer finns i den här självstudien:

> [!div class="nextstepaction"]
> [Själv studie kurs för hantering av app-grupper](./manage-app-groups.md)
