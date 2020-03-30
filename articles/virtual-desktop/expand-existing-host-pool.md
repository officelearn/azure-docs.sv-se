---
title: Utöka befintlig värdpool med nya sessionsvärdar – Azure
description: Så här expanderar du en befintlig värdpool med nya sessionsvärdar i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365227"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Expandera en befintlig värdpool med nya sessionsvärdar

När du ökar användningen i värdpoolen kan du behöva utöka din befintliga värdpool med nya sessionsvärdar för att hantera den nya belastningen.

I den här artikeln får du information om hur du kan utöka en befintlig värdpool med nya sessionsvärdar.

## <a name="what-you-need-to-expand-the-host-pool"></a>Vad du behöver för att utöka värdpoolen

Innan du börjar kontrollerar du att du har skapat en värdpool och en värddator för en virtuell session med någon av följande metoder:

- [Azure Marketplace-erbjudande](./create-host-pools-azure-marketplace.md)
- [GitHub Azure Resource Manager-mall](./create-host-pools-arm-template.md)
- [Skapa en värdpool med PowerShell](./create-host-pools-powershell.md)

Du behöver också följande information från när du först skapade värdpoolen och värdvärdarna:

- VM-storlek, bild- och namnprefix
- Autentiseringsuppgifter för domänanslutning och Windows Virtual Desktop-klientadministratör
- Namn på virtuellt nätverk och undernätsnamn

De följande tre avsnitten är tre metoder som du kan använda för att expandera värdpoolen. Du kan göra antingen med vilket distributionsverktyg du är bekväm med.

>[!NOTE]
>Under distributionsfasen visas felmeddelanden för den tidigare värd-VM-resursen för sessionen om de för närvarande stängs av. Dessa fel inträffar eftersom Azure inte kan köra PowerShell DSC-tillägget för att verifiera att virtuella datorer för sessionsvärdar är korrekt registrerade i din befintliga värdpool. Du kan ignorera dessa fel på ett säkert sätt eller undvika fel genom att starta alla virtuella datorer för sessionsvärdar i den befintliga värdpoolen innan du startar distributionsprocessen.

## <a name="redeploy-from-azure"></a>Omdistribution från Azure

Om du redan har skapat en värdpool och värdvärd virtuella datorer med Azure [Marketplace-erbjudandet](./create-host-pools-azure-marketplace.md) eller [GitHub Azure Resource Manager-mallen](./create-host-pools-arm-template.md)kan du distribuera om samma mall från Azure-portalen. Omdevisera om mallen återupptänder automatiskt all information som du angav i den ursprungliga mallen förutom lösenord.

Så här distribuerar du om Azure Resource Manager-mallen för att expandera en värdpool:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Sök efter **resursgrupper** i sökfältet högst upp i Azure-portalen och välj objektet under **Tjänster**.
3. Sök efter och välj den resursgrupp som du skapade när du skapade värdpoolen.
4. Välj **Distributioner**på panelen till vänster i webbläsaren .
5. Välj lämplig distribution för processen för att skapa värdpool:
     - Om du har skapat den ursprungliga värdpoolen med Azure Marketplace-erbjudandet väljer du distributionen som börjar med **rds.wvd-provision-host-pool**.
     - Om du har skapat den ursprungliga värdpoolen med GitHub Azure Resource Manager-mallen väljer du distributionen med namnet **Microsoft.Template**.
6. Välj **Omdedela**.
     
     >[!NOTE]
     >Om mallen inte distribueras om automatiskt när du väljer **Distribuera om**väljer du **Mall** på panelen till vänster i webbläsaren och väljer sedan **Distribuera**.

7. Välj den resursgrupp som innehåller de virtuella sessionsvärdspelarna i den befintliga värdpoolen.
     
     >[!NOTE]
     >Om du ser ett fel som talar om för dig att välja en annan resursgrupp även om den du angav är korrekt, väljer du en annan resursgrupp och väljer sedan den ursprungliga resursgruppen.

8. Ange följande URL för *_artifactsLocation:*`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Ange det nya totala antalet sessionsvärdar som du vill ha i *Rdsh Antal instanser*. Om du till exempel utökar värdpoolen från fem sessionsvärdar till åtta anger du **8**.
10. Ange samma befintliga domänlösenord som du använde för den befintliga domänen UPN. Ändra inte användarnamnet, eftersom det orsakar ett fel när du kör mallen.
11. Ange samma klientadministratörslösenord som du använde för det användar- eller program-ID som du angav för *Klientadministratörsupp- eller program-ID*. Återigen, ändra inte användarnamnet.
12. Slutför inlämningen för att utöka värdpoolen.

## <a name="run-the-azure-marketplace-offering"></a>Kör Azure Marketplace-erbjudandet

Följ instruktionerna i [Skapa en värdpool med hjälp av Azure Marketplace](./create-host-pools-azure-marketplace.md) tills du når Kör Azure [Marketplace-erbjudandet för att etablera en ny värdpool](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool). När du kommer till den punkten måste du ange följande information för varje flik:

### <a name="basics"></a>Grundläggande inställningar

Alla värden i det här avsnittet ska matcha det du angav när du först skapade värdpoolen och värdvärdarna, med undantag för *vanliga skrivbordsanvändare:*

1.    För *Prenumeration*väljer du den prenumeration där du först skapade värdpoolen.
2.    För *resursgrupp*väljer du samma resursgrupp där de befintliga värdpoolsessionsvärdvärdatorerna finns.
3.    För *Region*väljer du samma region där de befintliga värdpoolsessionsvärdvärdarna finns.
4.    För *Hostpool-namn*anger du namnet på den befintliga värdpoolen.
5.    För *skrivbordstyp*väljer du den skrivbordstyp som matchar den befintliga värdpoolen.
6.    För *standardskrivbordsanvändare*anger du en kommaavgränsad lista över ytterligare användare som du vill logga in på Windows Virtual Desktop-klienter och komma åt ett skrivbord när Azure Marketplace-erbjudandet har slutförts. Om du till exempel user3@contoso.com vill user4@contoso.com tilldela user3@contoso.comochuser4@contoso.comkomma åt anger du , .
7.    Välj **nästa: Konfigurera virtuell dator**.

>[!NOTE]
>Med undantag för *standardskrivbordsanvändare*måste alla fält matcha exakt det som har konfigurerats i den befintliga värdpoolen. Om det finns en obalans som kommer att resultera i en ny värdpool.

### <a name="configure-virtual-machines"></a>Konfigurera virtuella datorer

Alla parametervärden i det här avsnittet ska matcha det du angav när du först skapade värdpoolen och värdvärdarna, förutom det totala antalet virtuella datorer. Antalet virtuella datorer som du anger är antalet virtuella datorer i den utökade värdpoolen:

1. Välj den vm-storlek som matchar de befintliga virtuella datorerna för sessionsvärdar.
    
    >[!NOTE]
    >Om den specifika vm-storleken du letar efter inte visas i vm-storleksväljaren beror det på att vi inte har 2000 till Azure Marketplace-verktyget ännu. Om du vill begära en vm-storlek skapar du en begäran eller upvote en befintlig begäran i [Windows Virtual Desktop UserVoice-forumet](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Anpassa parametrarna *Användningsprofil,* *Totalt antal användare*och Antal virtuella *datorer* för att välja det totala antalet sessionsvärdar som du vill ha i värdpoolen. Om du till exempel expanderar värdpoolen från fem sessionsvärdar till åtta konfigurerar du dessa alternativ för att komma till åtta virtuella datorer.
3. Ange ett prefix för namnen på de virtuella datorerna. Om du till exempel anger namnet "prefix" kallas de virtuella datorerna "prefix-0", "prefix-1" och så vidare.
4. Välj **Nästa: Inställningar för virtuella datorer**.

### <a name="virtual-machine-settings"></a>Inställningar för virtuella datorer

Alla parametervärden i det här avsnittet ska matcha det du angav när du först skapade värdpoolen och värdvärdarna:

1. För *bildkälla* och *image OS-version*anger du samma information som du angav när du först skapade värdpoolen.
2. För *AD-domänen join UPN* och tillhörande lösenord anger du samma information som du angav när du först skapade värdpoolen för att ansluta till de virtuella datorerna till Active Directory-domänen. Dessa autentiseringsuppgifter används för att skapa ett lokalt konto på dina virtuella datorer. Du kan återställa dessa lokala konton för att ändra deras autentiseringsuppgifter senare.
3. För den virtuella nätverksinformationen väljer du samma virtuella nätverk och undernät för var den befintliga värdpoolens värddator för värd finns.
4. Välj **Nästa: Konfigurera information om virtuellt skrivbord i Windows**.

### <a name="windows-virtual-desktop-information"></a>Information om virtuellt skrivbord i Windows

Alla parametervärden i det här avsnittet ska matcha det du angav när du först skapade värdpoolen och värdvärdarna:

1. För *Klientgruppnamnet för Windows Virtual Desktop*anger du namnet på klientgruppen som innehåller din klientorganisation. Lämna det som standard om du inte angav ett specifikt klientgruppnamn.
2. För *Windows Virtual Desktop-klientnamn*anger du namnet på klienten där du ska skapa den här värdpoolen.
3. Ange samma autentiseringsuppgifter som du använde när du först skapade värdpoolen och värdvärds-VM. Om du använder ett tjänsthuvudnamn anger du ID:et för Azure Active Directory-instansen där tjänstens huvudnamn finns.
4. Välj **Nästa : Granska + skapa**.

## <a name="run-the-github-azure-resource-manager-template"></a>Köra GitHub Azure Resource Manager-mallen

Följ instruktionerna i [Kör Azure Resource Manager-mallen för att etablera en ny värdpool](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) och ange alla samma parametervärden förutom *Rdsh-antalet instanser*. Ange antalet virtuella datorer för sessionsvärdar som du vill använda i värdpoolen när du har kört mallen. Om du till exempel utökar värdpoolen från fem sessionsvärdar till åtta anger du **8**.

## <a name="next-steps"></a>Nästa steg

Nu när du har utökat din befintliga värdpool kan du logga in på en Windows Virtual Desktop-klient för att testa dem som en del av en användarsession. Du kan ansluta till en session med någon av följande klienter:

- [Ansluta med Windows-skrivbordsklienten](./connect-windows-7-and-10.md)
- [Ansluta med webbklienten](./connect-web.md)
- [Ansluta med Android-klienten](./connect-android.md)
- [Ansluta med macOS-klienten](./connect-macos.md)
- [Ansluta med iOS-klienten](./connect-ios.md)
