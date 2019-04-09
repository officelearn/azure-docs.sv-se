---
title: Skapa en förhandsversion för virtuella skrivbord i Windows-värd-pool med Azure Marketplace – Azure
description: Så här skapar du en förhandsversion för virtuella skrivbord i Windows-värd-pool med Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: f539a71fccca116ee031781df855ec55158eb63a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257458"
---
# <a name="tutorial-create-a-host-pool-with-azure-marketplace"></a>Självstudier: Skapa en värdpool med Azure Marketplace

Värd-pooler är en samling av en eller flera identiska virtuella datorer i förhandsversion för virtuella skrivbord i Windows klient-miljöer. Varje värd-pool kan innehålla en appgrupp som användare kan interagera med precis som på en fysisk dator.

Den här artikeln beskriver hur du skapar en pool för värden inom ett virtuellt skrivbord i Windows-klient med hjälp av ett Microsoft Azure Marketplace-erbjudande. Detta innefattar att skapa en pool med värden i Windows virtuellt skrivbord, skapa en resursgrupp med virtuella datorer i en Azure-prenumeration, ansluter dessa virtuella datorer till Active Directory-domän och registrera de virtuella datorerna med virtuella Windows-skrivbordet.

Innan du börjar [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på <https://portal.azure.com>.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Köra Azure Marketplace erbjuder att etablera en ny värd-pool

Köra Azure Marketplace erbjuder att etablera en ny pool för värden:

1. Välj **+** eller **+ skapa en resurs**.
2. Ange **virtuella Windows-skrivbordet** i sökfönstret Marketplace.
3. Välj **Windows virtuellt skrivbord – etablera poolen värden**och välj sedan **skapa**.

Följ anvisningarna för att ange information för lämpliga blad.

### <a name="basics"></a>Grundläggande inställningar

Här är vad du gör för bladet grundläggande inställningar:

1. Ange ett namn för värd-poolen som är unikt i virtuellt skrivbord i Windows-klient.
2. Välj lämpligt alternativ för personligt skrivbord. Om du väljer **Ja**, varje användare som ansluter till den här värden poolen tilldelas permanent till en virtuell dator.
3. Ange en kommaavgränsad lista över användare som kan logga in till virtuella skrivbord i Windows-klienter och komma åt en stationär dator när Azure Marketplace-erbjudande är klar. Exempel: Om du vill tilldela user1@contoso.com och user2@contoso.com öppna genom att ange ”user1@contoso.com,user2@contoso.com”.
4. Välj **Skapa nytt** och ange ett namn för den nya resursgruppen.
5. För **plats**, välj platsen som det virtuella nätverket som är ansluten till Active Directory-servern.
6. Välj **OK**.

### <a name="configure-virtual-machines"></a>Konfigurera virtuella datorer

För bladet Konfigurera virtuella datorer:

1. Acceptera standardvärdena eller anpassa antalet och storleken på de virtuella datorerna.
2. Ange ett prefix för namnen på de virtuella datorerna. Exempel: Om du anger ”namnprefix” virtuella datorer kallas ”prefix-0”, ”prefix-1” och så vidare.
3. Välj **OK**.

### <a name="virtual-machine-settings"></a>Inställningar för virtuella datorer

För det virtuella datorn inställningar-bladet:

1. Välj den **Bildkälla** och ange information om hur du hittar det och hur du lagrar den. Om du väljer att inte använda hanterade diskar, väljer du det lagringskonto som innehåller VHD-filen.
2. Ange användarens huvudnamn och lösenordet för domänkontot som ansluter till de virtuella datorerna till Active Directory-domän. Det här samma användarnamn och lösenord kommer att skapas på de virtuella datorerna som ett lokalt konto. Senare kan du återställa dessa lokala konton.
3. Välj det virtuella nätverk som är ansluten till Active Directory-servern och välj ett undernät som värd för de virtuella datorerna.
4. Välj **OK**.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Förhandsversion av virtuella Windows Desktop-klientinformation

För virtuella Windows-skrivbordet klient information bladet:

1. Ange den **gruppnamn för virtuella Windows-skrivbordet klient** för klient-grupp som innehåller din klient. Om du inte har en specifik klient gruppnamn planerat kan du lämna den som standard.
2. Ange den **klientnamnet för virtuella Windows-skrivbordet** för klienten ska du skapa den här poolen för värden i.
3. Ange vilken typ av autentiseringsuppgifter som du vill använda för att autentisera som innehavare RDS ägare för virtuella Windows-skrivbordet. Om du väljer **tjänstens huvudnamn**, måste du även ange den **Azure AD-klient-ID** som är associerad med tjänstens huvudnamn.
4. Ange antingen autentiseringsuppgifterna för klientorganisationens administratörskonto. Tjänstens huvudnamn med autentiseringsuppgifter för lösenord stöds.
5. Välj **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Slutför installationen och skapa den virtuella datorn

För de två sista bladen:

1. I den **sammanfattning** bladet granska installationsinformationen om. Om du vill ändra något gå tillbaka till bladet lämplig och gör dina ändringar innan du fortsätter. Om informationen är korrekt, väljer **OK**.
2. I den **köpa** bladet hittar du ytterligare information om ditt köp på Azure Marketplace.
3. Välj **skapa** att distribuera din värd-pool.

Beroende på hur många virtuella datorer som du skapar, kan den här processen ta 30 minuter eller mer att slutföra.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Valfritt) Tilldela ytterligare användare i gruppen skrivbordsprogram

När Azure Marketplace-erbjudande är klar kan du tilldela ytterligare användare i gruppen skrivbordsprogram innan du startar testerna fullständig session-skrivbord på dina virtuella datorer. Om du redan har lagt till standardanvändare i Azure Marketplace-erbjudande och inte vill lägga till fler, kan du hoppa över det här avsnittet.

Om du vill tilldela användare till gruppen skrivbordsprogram, måste du öppna ett PowerShell-fönster. Efter det måste du ange följande två cmdletar.

Kör följande cmdlet för att logga in på den virtuella skrivbordet i Windows-miljön:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ange kontexten i virtuella Windows-skrivbordet klient gruppen som du angav i Azure Marketplace erbjuder följande cmdlet. Om du lämnar virtuellt skrivbord i Windows-klient gruppvärdet som standardvärde i Azure Marketplace erbjuder, kan du hoppa över det här steget.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

När du har gjort dessa två saker du kan lägga till användare i gruppen skrivbordsprogram med denna cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Användarens UPN måste matcha användarens identitet i Azure Active Directory (till exempel user1@contoso.com). Om du vill lägga till flera användare måste du köra denna cmdlet för varje användare.

När du har slutfört de här stegen kan kan användare läggs till i gruppen skrivbordsprogram logga in på virtuella Windows-skrivbordet med Remote Desktop-klienter som stöds och se en resurs för ett session-skrivbord.

Här följer de aktuella klienterna som stöds:

- [Fjärrskrivbordsklient för Windows 7 och Windows 10](connect-windows-7-and-10.md)
- [Virtuella Windows-skrivbordet webbklienten](connect-web.md)

>[!IMPORTANT]
>Om du vill att skydda din miljö för virtuella Windows-skrivbordet i Azure, rekommenderar vi du inte öppna inkommande port 3389 på dina virtuella datorer. Virtuella Windows-skrivbordet kräver inte en öppen inkommande port 3389 för användare att komma åt värden poolens virtuella datorer. Om du måste öppna port 3389 för felsökningsändamål kan vi rekommenderar att du använder [åtkomst till Virtuella just-in-time](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Nästa steg

Nu när du har gjort en värd pool och tilldelade användare att komma åt dess desktop, kan du hämta din värd-pool med RemoteApps. Mer information om hur du hanterar appar i virtuella Windows-skrivbordet finns i Hantera app grupper självstudien.

> [!div class="nextstepaction"]
> [Hantera grupper självstudier](./manage-app-groups.md)
