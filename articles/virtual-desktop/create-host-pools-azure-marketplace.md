---
title: Skapa en pool för förhandsversion för virtuella skrivbord i Windows-värd med hjälp av Azure Marketplace – Azure
description: Hur du skapar en pool för förhandsversion för virtuella skrivbord i Windows-värd med hjälp av Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: f692303140db1441aa34aacef62523d7f596dba1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204742"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Självstudier: Skapa en pool med värden med hjälp av Azure Marketplace

Värd-pooler är en samling av en eller flera identiska virtuella datorer i förhandsversion för virtuella skrivbord i Windows klient-miljöer. Varje värd-pool kan innehålla en appgrupp som användare kan interagera med precis som på en fysisk dator.

Den här självstudien beskrivs hur du skapar en pool för värden i ett virtuellt skrivbord i Windows-klient med hjälp av ett Microsoft Azure Marketplace-erbjudande. Aktiviteterna är:

> [!div class="checklist"]
> * Skapa en pool med värden i virtuella Windows-skrivbordet.
> * Skapa en resursgrupp med virtuella datorer i en Azure-prenumeration.
> * Anslut de virtuella datorerna till Active Directory-domänen.
> * Registrera de virtuella datorerna med virtuella Windows-skrivbordet.

Innan du börjar [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Köra Azure Marketplace erbjuder att etablera en ny värd-pool

Köra Azure Marketplace erbjuder att etablera en ny pool för värden:

1. Välj **+** eller **+ skapa en resurs**.
2. Ange **virtuella Windows-skrivbordet** i sökfönstret Marketplace.
3. Välj **Windows virtuellt skrivbord – etablera poolen värden**, och välj sedan **skapa**.

Följ anvisningarna för att ange information för lämpliga blad.

### <a name="basics"></a>Grundläggande inställningar

Här är vad du gör den **grunderna** bladet:

1. Ange ett namn för värd-poolen som är unikt i virtuellt skrivbord i Windows-klient.
2. Välj lämpligt alternativ för ett personligt skrivbord. Om du väljer **Ja**, varje användare som ansluter till den här värden poolen tilldelas permanent till en virtuell dator.
3. Ange en kommaavgränsad lista över användare som kan logga in till virtuella skrivbord i Windows-klienter och tillgång till en stationär dator när Azure Marketplace erbjuder har slutförts. Exempel: Om du vill tilldela user1@contoso.com och user2@contoso.com öppna genom att ange ”user1@contoso.com,user2@contoso.com”.
4. Välj **Skapa nytt** och ange ett namn för den nya resursgruppen.
5. För **plats**, välj platsen som det virtuella nätverket som är ansluten till Active Directory-servern.
6. Välj **OK**.

### <a name="configure-virtual-machines"></a>Konfigurera virtuella datorer

För den **Konfigurera virtuella datorer** bladet:

1. Acceptera standardvärdena eller anpassa antalet och storleken på de virtuella datorerna.
2. Ange ett prefix för namnen på de virtuella datorerna. Exempel: Om du anger ”namnprefix” virtuella datorer kallas ”prefix-0”, ”prefix-1” och så vidare.
3. Välj **OK**.

### <a name="virtual-machine-settings"></a>Inställningar för virtuella datorer

För den **inställningar för virtuella datorer** bladet:

>[!NOTE]
> Om du ska ansluta dina virtuella datorer till en Azure Active Directory Domain Services (Azure AD DS)-miljö, kontrollera att din anslutning till domänanvändare även är medlem i den [AAD DC-administratörsgruppen](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group).

1. För **Bildkälla**, väljer du den och ange information om hur du hittar det och hur du lagrar den. Om du väljer att inte använda hanterade diskar, väljer du det lagringskonto som innehåller VHD-filen.
2. Ange användarens huvudnamn och lösenordet för domänkontot som ansluter till de virtuella datorerna till Active Directory-domän. Det här samma användarnamn och lösenord kommer att skapas på de virtuella datorerna som ett lokalt konto. Senare kan du återställa dessa lokala konton.
3. Välj det virtuella nätverk som är ansluten till Active Directory-servern och välj sedan ett undernät som värd för de virtuella datorerna.
4. Välj **OK**.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Förhandsversion av virtuella Windows Desktop-klientinformation

För den **klientinformation för virtuella Windows-skrivbordet** bladet:

1. För **gruppnamn för virtuella Windows-skrivbordet klient**, anger du namnet för den klient-grupp som innehåller din klient. Lämna den som standard såvida inte du har en specifik klient gruppnamn.
2. För **klientnamnet för virtuella Windows-skrivbordet**, anger du namnet på klienten där du ska skapa den här poolen för värden.
3. Ange vilken typ av autentiseringsuppgifter som du vill använda för att autentisera som innehavare RDS ägare för virtuella Windows-skrivbordet. Om du har slutfört den [skapa tjänstens huvudnamn och rolltilldelningar med PowerShell självstudiekursen](./create-service-principal-role-powershell.md)väljer **tjänstens huvudnamn**. När **Azure AD-klient-ID** visas, ange ID för Azure Active Directory-instans som innehåller tjänstens huvudnamn.
4. Ange autentiseringsuppgifterna för klientorganisationens administratörskonto. Tjänstens huvudnamn med autentiseringsuppgifter för lösenord stöds.
5. Välj **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Slutför installationen och skapa den virtuella datorn

För de två sista bladen:

1. På den **sammanfattning** bladet granska installationsinformationen om. Om du vill ändra något gå tillbaka till bladet lämplig och gör dina ändringar innan du fortsätter. Om informationen är korrekt, väljer **OK**.
2. På den **köpa** bladet hittar du ytterligare information om ditt köp från Azure Marketplace.
3. Välj **skapa** att distribuera din värd-pool.

Beroende på hur många virtuella datorer som du skapar, kan den här processen ta 30 minuter eller mer att slutföra.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Valfritt) Tilldela ytterligare användare i gruppen skrivbordsprogram

När du har Azure Marketplace erbjuder har slutförts, kan du tilldela fler användare i gruppen skrivbordsprogram innan du startar testerna fullständig session-skrivbord på dina virtuella datorer. Om du redan har lagt till standardanvändare i Azure Marketplace-erbjudande och inte vill lägga till fler, kan du hoppa över det här avsnittet.

Om du vill tilldela användare till gruppen skrivbordsprogram, måste du öppna ett PowerShell-fönster. Efter det måste du ange följande två cmdletar.

Kör följande cmdlet för att logga in på den virtuella skrivbordet i Windows-miljön:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Lägg till användare i gruppen skrivbordsprogram genom att använda denna cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Användarens UPN måste matcha användarens identitet i Azure Active Directory (till exempel user1@contoso.com). Om du vill lägga till flera användare måste du köra denna cmdlet för varje användare.

När du har slutfört de här stegen kan kan användare läggs till i gruppen skrivbordsprogram logga in på virtuella Windows-skrivbordet med Remote Desktop-klienter som stöds och se en resurs för ett session-skrivbord.

Här följer de aktuella klienterna som stöds:

- [Fjärrskrivbordsklient för Windows 7 och Windows 10](connect-windows-7-and-10.md)
- [Virtuella Windows-skrivbordet webbklienten](connect-web.md)

>[!IMPORTANT]
>Om du vill att skydda din miljö för virtuella Windows-skrivbordet i Azure, rekommenderar vi du inte öppna inkommande port 3389 på dina virtuella datorer. Virtuella Windows-skrivbordet kräver inte en öppen inkommande port 3389 för användare att komma åt värden poolens virtuella datorer. Om du måste öppna port 3389 för felsökningsändamål kan vi rekommenderar att du använder [åtkomst till Virtuella just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Nästa steg

Nu när du har gjort en värd pool och tilldelade användare att komma åt dess desktop, kan du fylla i din värd-pool med RemoteApp-program. Mer information om hur du hanterar appar i Windows virtuellt skrivbord finns den här självstudien:

> [!div class="nextstepaction"]
> [Hantera grupper självstudier](./manage-app-groups.md)
